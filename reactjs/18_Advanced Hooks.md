<details>
<summary>1. Can you explain the concept of custom hooks in React? How do they differ from standard hooks, and when should you use them?</summary>

**Answer:**  
Custom hooks are JavaScript functions that use one or more built-in React hooks (`useState`, `useEffect`, etc.) to encapsulate and reuse logic across components. They follow the "use" naming convention and can manage state, side effects, or other behaviors that need to be shared among multiple components.

**Differences from standard hooks:**

- **Custom hooks** are created by developers for specific use cases, while **standard hooks** are built into React.
- Custom hooks encapsulate reusable logic, while standard hooks provide generic functionality (e.g., `useState` for managing state).

**When to use custom hooks:**

- To avoid duplicating logic across components.
- When managing shared logic for concerns like API calls, event listeners, or form handling.

</details>  
</br>

<details>
<summary>2. How would you create a custom hook for managing form validation logic? Provide an example and explain how it ensures reusability.</summary>

**Answer:**  
Here’s an example of a custom hook for form validation:

```javascript
import { useState } from "react";

function useFormValidation(initialValues, validate) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});

  const handleChange = (event) => {
    const { name, value } = event.target;
    setValues({ ...values, [name]: value });
    if (validate) {
      const validationErrors = validate(name, value);
      setErrors({ ...errors, ...validationErrors });
    }
  };

  const resetForm = () => {
    setValues(initialValues);
    setErrors({});
  };

  return { values, errors, handleChange, resetForm };
}
```

**Usage:**

```javascript
const validate = (name, value) => {
  const errors = {};
  if (name === "email" && !value.includes("@")) errors.email = "Invalid email!";
  return errors;
};

const { values, errors, handleChange, resetForm } = useFormValidation(
  { email: "" },
  validate
);
```

**Reusability:**

- Abstracts validation logic into a single function, usable across multiple forms.
- Supports dynamic rules based on the `validate` function.

</details>  
</br>

<details>
<summary>3. What are potential pitfalls of using custom hooks in a complex application? How do you debug issues when custom hooks fail to work as expected?</summary>

**Answer:**  
**Potential pitfalls:**

1. **Over-engineering:** Creating overly generic hooks can lead to complexity without clear benefits.
2. **Performance issues:** If hooks aren’t optimized, they can trigger unnecessary re-renders.
3. **Debugging complexity:** Abstracting logic into hooks may make it harder to trace the source of issues.
4. **Dependencies:** Poorly managed dependencies in hooks (e.g., in `useEffect`) can cause bugs or stale data.

**Debugging approaches:**

- **Isolate the hook:** Test it independently in a small component.
- **Console logging:** Log state updates and effect triggers inside the hook.
- **React DevTools:** Inspect the component tree and verify props and state.
- **Unit testing:** Write tests to ensure the hook behaves as expected under different scenarios.

</details>  
</br>

<details>
<summary>4. How can you optimize a custom hook to minimize unnecessary re-renders in components that use it?</summary>

**Answer:**

- **Use `useMemo` and `useCallback`:** Memoize expensive calculations or functions returned from the hook.
- **Return minimal data:** Only return values or functions that the component needs.
- **Avoid triggering updates unnecessarily:** Ensure state updates in the hook are precise (e.g., avoid setting the same state repeatedly).
- **Separate concerns:** Split logic into smaller hooks to avoid interdependencies.

Example:

```javascript
const useOptimizedHook = () => {
  const memoizedValue = useMemo(() => computeExpensiveValue(), []);
  const memoizedCallback = useCallback(() => performAction(), []);

  return { memoizedValue, memoizedCallback };
};
```

</details>  
</br>

<details>
<summary>5. Can you explain how custom hooks can handle asynchronous operations, such as fetching data? Provide an example of error handling within a custom hook.</summary>

**Answer:**  
Custom hooks can handle asynchronous operations like API calls using `useEffect` and state management.

Example:

```javascript
import { useState, useEffect } from "react";

function useFetch(url) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let isMounted = true; // To handle component unmount during fetch.
    setLoading(true);
    fetch(url)
      .then((response) => {
        if (!response.ok) throw new Error("Network response was not ok");
        return response.json();
      })
      .then((data) => isMounted && setData(data))
      .catch((err) => isMounted && setError(err.message))
      .finally(() => isMounted && setLoading(false));

    return () => {
      isMounted = false;
    };
  }, [url]);

  return { data, error, loading };
}
```

</details>  
</br>

<details>
<summary>6. What are the differences between `useRef` and `useState` for persisting values across renders? When would you choose one over the other?</summary>

**Answer:**  
| **Aspect** | **`useRef`** | **`useState`** |  
|------------------|--------------------------------------------|------------------------------------------|  
| **Re-renders** | Does not trigger re-renders. | Triggers a re-render on state update. |  
| **Purpose** | For storing mutable values or DOM refs. | For storing component state. |  
| **Performance** | Better for values that don't affect UI. | Use for values that impact rendering. |

**When to choose:**

- Use `useRef` for mutable values like timers, previous values, or DOM nodes.
- Use `useState` for values that need to trigger UI updates when changed.

</details>  
</br>

<details>
<summary>7. Explain how `useRef` can be used for accessing DOM elements. How does it differ from using callback refs?</summary>

**Answer:**  
`useRef` can be used to directly reference a DOM element:

```javascript
import { useRef, useEffect } from "react";

function App() {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus(); // Access DOM node and focus the input
  }, []);

  return <input ref={inputRef} />;
}
```

**Difference from callback refs:**

- `useRef` creates a persistent object that stays the same across renders.
- Callback refs are functions called when a ref is attached or detached:

```javascript
const setRef = (node) => {
  if (node) node.focus();
};

<input ref={setRef} />;
```

- Callback refs provide more flexibility but can introduce additional re-renders if not managed correctly.

</details>  
</br>

<details>
<summary>8. What are non-DOM-related use cases for `useRef`? Can you provide a practical example of managing a mutable value?</summary>

**Answer:**  
Non-DOM-related use cases for `useRef` include:

- Storing mutable values that persist across renders but don’t trigger re-renders.
- Keeping track of previous values.
- Managing timers or intervals.

**Example:** Tracking the previous value of a state:

```javascript
import { useRef, useEffect } from "react";

function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}

// Usage
const [count, setCount] = useState(0);
const prevCount = usePrevious(count);
```

</details>  
</br>

<details>
<summary>9. Can `useRef` cause memory leaks in a React component? If so, how would you avoid it?</summary>

**Answer:**  
`useRef` itself does not inherently cause memory leaks, but improper use can lead to leaks. For example, attaching long-running timers or event listeners to `useRef` without cleaning them up can cause issues.

**To avoid memory leaks:**

- Always clean up timers, intervals, or event listeners in a `useEffect` cleanup function.
- Ensure proper disposal of large objects stored in `useRef`.

**Example:**

```javascript
const timerRef = useRef(null);

useEffect(() => {
  timerRef.current = setInterval(() => console.log("Running"), 1000);
  return () => clearInterval(timerRef.current); // Cleanup
}, []);
```

</details>  
</br>

<details>
<summary>10. Imagine a situation where you need to debounce a user input. How would you implement it using `useRef`?</summary>

**Answer:**  
**Debouncing user input using `useRef`:**

```javascript
import { useRef } from "react";

function useDebouncedCallback(callback, delay) {
  const timerRef = useRef(null);

  const debouncedCallback = (...args) => {
    if (timerRef.current) clearTimeout(timerRef.current);
    timerRef.current = setTimeout(() => {
      callback(...args);
    }, delay);
  };

  return debouncedCallback;
}

// Usage
const debouncedSearch = useDebouncedCallback(
  (value) => console.log(value),
  500
);

<input onChange={(e) => debouncedSearch(e.target.value)} />;
```

</details>  
</br>

<details>
<summary>11. Can you explain the key differences between `useLayoutEffect` and `useEffect`? How does their execution timing differ?</summary>

**Answer:**  
**Key differences:**

- `useEffect`: Runs after the browser has painted, making it suitable for non-blocking operations like data fetching.
- `useLayoutEffect`: Runs synchronously before the browser paints, making it ideal for DOM measurements or synchronizations that need to occur before the user sees the UI.

**Execution timing:**

- `useEffect`: Executes asynchronously after rendering and painting.
- `useLayoutEffect`: Executes synchronously after rendering but before painting.

</details>  
</br>

<details>
<summary>12. When would using `useLayoutEffect` be necessary, and what are the potential performance implications of using it incorrectly?</summary>

**Answer:**  
**When to use `useLayoutEffect`:**

- When you need to measure or manipulate the DOM before the browser paints (e.g., to prevent layout shift).
- For synchronizing animations or resolving UI glitches.

**Performance implications of incorrect use:**

- Using `useLayoutEffect` unnecessarily can block rendering, leading to poor performance.
- It should not contain long-running or asynchronous code.

</details>  
</br>

<details>
<summary>13. Provide an example where using `useEffect` instead of `useLayoutEffect` would cause a visual glitch or bug.</summary>

**Answer:**  
**Example:** Measuring the width of an element after rendering:

```javascript
const MyComponent = () => {
  const ref = useRef(null);
  const [width, setWidth] = useState(0);

  // Incorrect: Using useEffect may result in a flicker as the measurement happens after painting
  useLayoutEffect(() => {
    setWidth(ref.current.offsetWidth);
  }, []);

  return (
    <div ref={ref} style={{ width: width > 100 ? "50%" : "100%" }}>
      Content
    </div>
  );
};
```

If `useEffect` is used, the flicker occurs because the update happens after the initial paint.

</details>  
</br>

<details>
<summary>14. Why does React warn against using `useLayoutEffect` on the server side? How would you conditionally use it for client-side-only rendering?</summary>

**Answer:**  
**Why the warning?**  
`useLayoutEffect` relies on the DOM, which is not available during server-side rendering (SSR). It can cause hydration warnings if used on the server.

**Solution:** Use `useEffect` as a fallback:

```javascript
const useIsomorphicLayoutEffect =
  typeof window !== "undefined" ? useLayoutEffect : useEffect;
```

</details>  
</br>

<details>
<summary>15. How would you debug and resolve performance issues caused by `useLayoutEffect` in a React component tree?</summary>

**Answer:**  
**Steps to debug and resolve performance issues:**

1. **Analyze dependencies:** Ensure that `useLayoutEffect` dependencies are correct to avoid unnecessary executions.
2. **Measure impact:** Use React DevTools Profiler to identify performance bottlenecks.
3. **Optimize the effect:** Move non-essential code to `useEffect` if possible.
4. **Avoid long-running tasks:** Use `useMemo` or `useCallback` to cache expensive computations.

</details>  
</br>

<details>
<summary>16. What is `useImperativeHandle`, and why is it used in React? How does it interact with `React.forwardRef`?</summary>

**Answer:**  
`useImperativeHandle` customizes the ref exposed by `React.forwardRef`. It is used when you want to control the methods or properties that are accessible from the parent component.

**Example:**

```javascript
const CustomInput = React.forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
  }));

  return <input ref={inputRef} />;
});

const Parent = () => {
  const inputRef = useRef();
  return (
    <>
      <CustomInput ref={inputRef} />
      <button onClick={() => inputRef.current.focus()}>Focus Input</button>
    </>
  );
};
```

</details>  
</br>

<details>
<summary>17. Explain the difference between directly exposing a DOM node using `forwardRef` and customizing its interface using `useImperativeHandle`.</summary>

**Answer:**

- **Directly exposing a DOM node:** Allows the parent to access the DOM node without any abstraction.
- **Customizing with `useImperativeHandle`:** Provides a controlled interface, hiding implementation details and exposing only necessary methods.

</details>  
</br>

<details>
<summary>18. How would you implement a reusable input component that exposes custom methods like `focus` and `clear` using `useImperativeHandle`?</summary>

**Answer:**

```javascript
const CustomInput = React.forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => (inputRef.current.value = ""),
  }));

  return <input ref={inputRef} />;
});

// Usage
const Parent = () => {
  const ref = useRef();
  return (
    <>
      <CustomInput ref={ref} />
      <button onClick={() => ref.current.focus()}>Focus</button>
      <button onClick={() => ref.current.clear()}>Clear</button>
    </>
  );
};
```

</details>  
</br>

<details>
<summary>19. Can you explain a scenario where overusing `useImperativeHandle` can lead to anti-patterns or difficult-to-maintain code? How would you avoid this?</summary>

**Answer:**  
**Scenario:**  
Overusing `useImperativeHandle` to expose multiple methods or complex APIs can make the component difficult to maintain and test.

**Solution:**

- Limit the exposed interface to only essential methods.
- Use declarative patterns whenever possible, falling back to imperative logic only for edge cases like integrating with third-party libraries.

</details>  
</br>

<details>
<summary>20. How does the use of `useImperativeHandle` affect the declarative nature of React, and in what scenarios is its use justified?</summary>

**Answer:**  
**Impact on declarative nature:**  
`useImperativeHandle` introduces an imperative API, deviating from React’s declarative paradigm. It should be used sparingly to avoid complicating the component hierarchy.

**Justified scenarios:**

- When integrating with non-React libraries that require imperative APIs.
- When exposing specific actions for reusable components, such as `focus` or `scroll`.

</details>  
</br>

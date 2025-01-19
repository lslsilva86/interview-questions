<details>

<summary>1. What are the primary differences between `React.memo` and `useMemo` in terms of how and when they optimize component rendering?</summary>

- **`React.memo`** is used to optimize a React component by memoizing its rendered output. It prevents unnecessary re-renders if the props haven't changed (shallow comparison). It is a **higher-order component (HOC)** that wraps a component.
- **`useMemo`** is a **hook** that memoizes the result of a computation and only recomputes it when the dependencies change. It’s typically used within a component to avoid expensive recalculations for derived values.

</details>

</br>

<details>

<summary>2. Can you explain a scenario where using `React.memo` could cause performance issues instead of improving performance?</summary>

- If the props being passed are complex (e.g., objects, arrays) and frequently change, the shallow comparison done by `React.memo` can become expensive because it happens on every render.
- In scenarios where the cost of comparing props exceeds the cost of re-rendering, `React.memo` can hurt performance instead of improving it.

</details>

</br>

<details>

<summary>3. How does `useMemo` decide whether to recompute a memoized value? What are the potential pitfalls of incorrect dependency array usage?</summary>

- **Decision**: `useMemo` recomputes a value if any of the dependencies in its dependency array change (using reference equality for comparison).
- **Pitfalls**:
  - Omitting necessary dependencies can lead to stale or incorrect memoized values.
  - Including unnecessary dependencies can cause excessive recomputations.
  - Accidentally mutating dependencies (e.g., arrays or objects) can lead to unexpected recomputations.

</details>

</br>

<details>

<summary>4. Write a component using `useMemo` to optimize the calculation of a derived state from a list of items. Explain why this optimization is necessary.</summary>

```jsx
import React, { useMemo, useState } from "react";

function ExpensiveCalculation({ items }) {
  const [filter, setFilter] = useState("");

  // Expensive calculation
  const filteredItems = useMemo(() => {
    console.log("Filtering items...");
    return items.filter((item) => item.includes(filter));
  }, [items, filter]);

  return (
    <div>
      <input
        type="text"
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Filter items"
      />
      <ul>
        {filteredItems.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

- **Why necessary**: Without `useMemo`, the `filter` operation would execute on every render, even if `items` and `filter` didn’t change. This optimization reduces the computation frequency.

</details>

</br>

<details>

<summary>5. Why might `useMemo` not always be the best choice for optimization? When should you avoid using it?</summary>

- **Overhead**: `useMemo` itself has a cost, so if the computation is cheap, the overhead of memoization may outweigh its benefits.
- **Complexity**: Overusing `useMemo` can make code harder to read and maintain.
- **Avoid if**:
  - Computations are inexpensive.
  - Dependencies rarely change.

</details>

</br>

<details>

<summary>6. How does `useCallback` prevent unnecessary re-renders, and how is it different from `useMemo`?</summary>

- **Prevents Re-renders**: `useCallback` memoizes a function reference, ensuring that a new function instance is not created unless dependencies change. This helps avoid unnecessary re-renders when functions are passed as props.
- **Difference**:
  - `useMemo` memoizes **values**, while `useCallback` memoizes **functions**.
  - `useCallback(fn, deps)` is equivalent to `useMemo(() => fn, deps)`.

</details>

</br>

<details>

<summary>7. If a parent component uses `useCallback` for a function passed as a prop, but the child doesn’t use `React.memo`, will it prevent unnecessary re-renders? Explain.</summary>

- No, it will not prevent unnecessary re-renders.
  - `useCallback` ensures that the function reference is stable, but if the child component isn’t wrapped with `React.memo`, it will re-render regardless because React doesn't skip rendering non-memoized components.

</details>

</br>

<details>

<summary>8. Explain how closures in JavaScript interact with `useCallback`. How can this lead to unexpected behaviors in React components?</summary>

- **Closures**: When a function is created inside a component, it captures variables from its surrounding scope. If these variables change over time, the closure may retain stale values.
- **Unexpected behavior**:

  - Using `useCallback` with a missing dependency can cause the memoized function to use outdated state/props, leading to incorrect behavior (stale closure).

- **Example**:

```jsx
const handleClick = useCallback(() => {
  console.log(count); // May log stale `count`
}, []); // Missing `count` in dependencies
```

- **Fix**:

```jsx
const handleClick = useCallback(() => {
  console.log(count); // Correct behavior
}, [count]);
```

</details>

</br>

<details>

<summary>9. Provide a code example where using `useCallback` causes a memory leak or a stale closure issue. How would you fix it?</summary>

- **Example of Memory Leak**:

```jsx
import React, { useState, useCallback, useEffect } from "react";

function Timer() {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount(count + 1); // Stale closure: `count` is not updated
  }, []); // Missing `count` in dependencies

  useEffect(() => {
    const interval = setInterval(increment, 1000);
    return () => clearInterval(interval); // Cleanup is necessary to prevent memory leaks
  }, [increment]);

  return <div>Count: {count}</div>;
}
```

- **Fix**: Add `count` as a dependency to `useCallback`:

```jsx
const increment = useCallback(() => {
  setCount((prevCount) => prevCount + 1); // Use functional update to avoid stale closure
}, []);
```

</details>

</br>

<details>

<summary>10. What are the trade-offs of using `useCallback` for every function declared inside a React component? When might this optimization be unnecessary?</summary>

- **Trade-offs**:

  - Overhead: `useCallback` itself has a cost (e.g., creating and storing memoized functions).
  - Complexity: Overusing `useCallback` can make the code harder to read and maintain.
  - Not always beneficial: For lightweight functions or components that re-render infrequently, `useCallback` may not improve performance.

- **When unnecessary**:
  - When the function is cheap to create and doesn’t cause re-renders in child components.
  - When the component is unlikely to re-render often.

</details>

</br>

<details>

<summary>11. Explain React’s reconciliation process and how it determines which parts of the UI to update. How does it achieve optimal rendering?</summary>

- React uses the **reconciliation process** to compare the virtual DOM with the previous version to determine what has changed.
- React optimizes rendering using:
  - **Diffing algorithm**: React performs a diff on the virtual DOM to find changes.
  - **Keyed comparison**: React identifies and reuses DOM elements efficiently when keys are consistent.
  - **Batching updates**: Multiple state changes are batched to minimize re-renders.

</details>

</br>

<details>

<summary>12. What are the key differences between the "mounting" and "updating" phases of reconciliation?</summary>

- **Mounting phase**:

  - Occurs when a component is rendered for the first time.
  - React builds the virtual DOM and inserts the elements into the real DOM.

- **Updating phase**:
  - Occurs when a component’s state or props change.
  - React calculates the differences between the new virtual DOM and the previous one, then updates only the necessary parts of the real DOM.

</details>

</br>

<details>

<summary>13. How does React handle key differences in sibling components during reconciliation? Provide a code example where improper key usage leads to rendering issues.</summary>

- React uses **keys** to identify elements in a list. Keys help React match existing elements with new ones during updates.
- **Improper key usage**:

  - If keys are not unique or consistent, React will re-create DOM elements unnecessarily, causing performance issues and incorrect behavior.

- **Example**:

```jsx
const items = ["Apple", "Banana", "Cherry"];
return items.map((item) => <div key={Math.random()}>{item}</div>); // Bad: Keys change on every render
```

- **Fix**:

```jsx
return items.map((item, index) => <div key={index}>{item}</div>); // Consistent keys
```

</details>

</br>

<details>

<summary>14. Describe how React optimizes updates with the virtual DOM and diffing algorithm. Can you explain the time complexity of React's diffing algorithm?</summary>

- React creates a **virtual DOM** to represent the UI in memory and minimizes updates to the real DOM by computing changes through a diffing algorithm.
- **Time Complexity**:
  - React avoids comparing every node by assuming **two elements of different types will never have the same structure**.
  - Complexity is reduced to \( O(n) \), where \( n \) is the number of elements in the tree.

</details>

</br>

<details>

<summary>15. In what scenarios would React’s reconciliation process fail to optimize rendering, and how can you mitigate these inefficiencies?</summary>

- **Scenarios**:

  - Missing or improper keys in lists.
  - Passing new object/array references as props on every render.
  - Frequent re-renders caused by unnecessary state updates.

- **Mitigation**:
  - Use `React.memo` for memoizing components.
  - Use `useMemo` and `useCallback` to prevent re-creating values and functions.
  - Ensure keys are unique and stable in lists.

</details>

</br>

<details>

<summary>16. How does `React.memo` interact with the reconciliation process? What happens under the hood when a component wrapped with `React.memo` receives new props?</summary>

- **Interaction**: `React.memo` compares the current props with the previous props using shallow comparison. If props haven’t changed, React skips rendering the component.
- **Under the hood**:
  - React stores the last rendered output of the component.
  - If props are identical, React reuses the previous output instead of re-rendering.

</details>

</br>

<details>

<summary>17. What role do keys play in React's reconciliation process? What problems arise when keys are not unique or consistent?</summary>

- **Role of keys**:

  - Keys help React identify which elements in a list have changed, been added, or removed.
  - They optimize the reconciliation process by enabling React to match elements correctly.

- **Problems**:
  - Non-unique keys can cause React to mix up elements, leading to incorrect updates.
  - Changing keys on every render forces React to re-create elements unnecessarily.

</details>

</br>

<details>

<summary>18. You have a parent component with multiple child components. Each child receives props from the parent. How would you optimize the component tree to prevent unnecessary re-renders in the children using `React.memo`, `useMemo`, and `useCallback`?</summary>

- **Steps**:
  - Wrap child components with `React.memo` to prevent re-renders when props don’t change.
  - Use `useMemo` to memoize derived data passed as props.
  - Use `useCallback` to memoize callback functions passed to children.

</details>

</br>

<details>

<summary>19. Consider a React application with heavy computational logic inside a component. How would you refactor it using `useMemo`, and how would you measure the performance improvement?</summary>

- **Refactor**: Move the computational logic into a `useMemo` hook and add necessary dependencies to its dependency array.
- **Measurement**:
  - Use React Developer Tools’ profiler to measure render times.
  - Compare render times before and after applying `useMemo`.

</details>

</br>

<details>

<summary>20. Write a React component tree where improper memoization (with `React.memo`, `useCallback`, or `useMemo`) causes performance degradation. Then refactor it to fix the issue.</summary>

```jsx
// Improper memoization: Passing inline functions
<Parent>
  <Child onClick={() => console.log("Clicked")} />
</Parent>
```

**Refactor**:

```jsx
const memoizedCallback = useCallback(() => {
  console.log("Clicked");
}, []);
<Parent>
  <Child onClick={memoizedCallback} />
</Parent>;
```

</details>

</br>

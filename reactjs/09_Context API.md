<details>
<summary>1. What are the main differences between React's Context API and other state management libraries like Redux? When would you choose one over the other?</summary>

- **React Context API**:

  - Provides a way to share values (state) globally without prop drilling.
  - Suitable for static or rarely changing data like themes, user authentication, or locale settings.
  - Simpler and built into React; no need for external dependencies.
  - No middleware support or devtools for debugging.

- **Redux**:

  - A predictable state container with centralized state management.
  - Suitable for complex state management with frequent updates or actions.
  - Middleware support (e.g., Redux Saga, Redux Thunk) for handling side effects.
  - Offers powerful debugging tools like Redux DevTools.

- **When to choose**:
  - Use Context API for simple and localized state-sharing needs.
  - Use Redux for large-scale applications requiring a predictable state structure, middleware, and robust debugging.

</details>  
</br>

<details>
<summary>2. Explain the lifecycle of a React component in the context of the Context API. How does a change in the context value affect the components consuming it?</summary>

- When the `value` of a `Context.Provider` changes, React triggers a re-render of all consuming components (using `useContext` or `Context.Consumer`) in the subtree.
- During rendering:
  - The consuming components will re-evaluate and use the latest context value.
- React uses **reference equality** to detect changes in the context value. If the new value is different from the previous one (via `===`), it triggers updates.
- Key consideration:
  - If the `value` object is re-created on every render (e.g., `{}` or functions), it can cause unnecessary re-renders.

</details>  
</br>

<details>
<summary>3. How does React decide which components to re-render when the context value changes? What can you do to optimize performance in these cases?</summary>

- React compares the new `value` with the previous one using reference equality (`===`).
- If the value is different, all components consuming the context will re-render.

**Optimization strategies**:

1. **Memoize the value**:
   Use `useMemo` to prevent unnecessary updates:
   ```jsx
   const memoizedValue = useMemo(() => ({ key: value }), [value]);
   <Context.Provider value={memoizedValue}>
   ```
2. **Split contexts**:
   Create multiple contexts for different parts of the state to minimize re-renders.
3. **Avoid overuse of context**:
   For frequently updated state, consider other state management solutions like Redux or lifting state up.

</details>  
</br>

<details>
<summary>4. Given the following code snippet, identify any potential issues and optimize it:</summary>

```jsx
const ThemeContext = createContext();

const App = () => {
  const theme = { darkMode: true };

  return (
    <ThemeContext.Provider value={theme}>
      <ChildComponent />
    </ThemeContext.Provider>
  );
};

const ChildComponent = () => {
  const theme = useContext(ThemeContext);
  return <div>{theme.darkMode ? "Dark Mode" : "Light Mode"}</div>;
};
```

**Issues**:

1. The `theme` object is recreated on every render, causing all consuming components to re-render unnecessarily.
2. This can impact performance, especially with deeply nested components.

**Optimized code**:

```jsx
const ThemeContext = createContext();

const App = () => {
  const theme = useMemo(() => ({ darkMode: true }), []);

  return (
    <ThemeContext.Provider value={theme}>
      <ChildComponent />
    </ThemeContext.Provider>
  );
};

const ChildComponent = () => {
  const theme = useContext(ThemeContext);
  return <div>{theme.darkMode ? "Dark Mode" : "Light Mode"}</div>;
};
```

</details>  
</br>

<details>
<summary>5. Write a custom hook, `useTheme`, that abstracts the logic of consuming a context called `ThemeContext`. Ensure it provides error handling for cases where the provider is not used.</summary>

```jsx
import { useContext, createContext } from "react";

const ThemeContext = createContext();

export const ThemeProvider = ({ children }) => {
  const theme = { darkMode: true };
  return (
    <ThemeContext.Provider value={theme}>{children}</ThemeContext.Provider>
  );
};

export const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error("useTheme must be used within a ThemeProvider");
  }
  return context;
};
```

Usage:

```jsx
const Component = () => {
  const { darkMode } = useTheme();
  return <div>{darkMode ? "Dark Mode" : "Light Mode"}</div>;
};
```

</details>  
</br>

<details>
<summary>6. How would you handle scenarios where a component needs to consume data from multiple contexts? Provide an example.</summary>

- Use nested `useContext` calls or create a composite context to simplify the interface.

**Example with multiple `useContext` calls**:

```jsx
const UserContext = createContext();
const ThemeContext = createContext();

const Component = () => {
  const user = useContext(UserContext);
  const theme = useContext(ThemeContext);

  return (
    <div style={{ color: theme.darkMode ? "white" : "black" }}>
      Welcome, {user.name}!
    </div>
  );
};
```

**Example with a composite context**:

```jsx
const CombinedContext = createContext();

const CombinedProvider = ({ children }) => {
  const user = { name: "John" };
  const theme = { darkMode: true };

  return (
    <CombinedContext.Provider value={{ user, theme }}>
      {children}
    </CombinedContext.Provider>
  );
};

const Component = () => {
  const { user, theme } = useContext(CombinedContext);
  return (
    <div style={{ color: theme.darkMode ? "white" : "black" }}>
      Welcome, {user.name}!
    </div>
  );
};
```

</details>  
</br>

<details>
<summary>7. How can you make the Context API more scalable in a large application with deeply nested components and multiple contexts?</summary>

1. **Use context splitting**:

   - Create separate contexts for distinct parts of the application state to avoid unnecessary re-renders.
   - Example: `ThemeContext`, `AuthContext`, `SettingsContext`.

2. **Memoization**:

   - Use `useMemo` to ensure context values are stable and prevent re-renders.

3. **Custom hooks**:

   - Abstract context logic into reusable custom hooks to improve readability and modularity.

4. **Avoid unnecessary nesting**:

   - Use patterns like a single "AppState" context to combine related contexts, reducing nested providers.

5. **Selector pattern**:
   - Use context selectors to consume only the specific part of the context state needed.

**Example of context selector pattern**:

```jsx
const UserContext = createContext();

const useUserName = () => {
  const { name } = useContext(UserContext);
  return name;
};

const Component = () => {
  const name = useUserName();
  return <div>Welcome, {name}!</div>;
};
```

</details>  
</br>

<details>
<summary>8. Can you implement a dynamic Context API where the context itself can change dynamically at runtime (e.g., switching between different contexts based on user input)?</summary>

Yes, a dynamic Context API can be implemented by managing the active context dynamically within a parent component and providing the active context's value through a common provider.

**Example**:

```jsx
import React, { createContext, useContext, useState } from "react";

const ThemeContext = createContext();
const LanguageContext = createContext();

const DynamicProvider = ({ children }) => {
  const [activeContext, setActiveContext] = useState("theme");

  const contextValue = {
    theme: { darkMode: true },
    language: { current: "English" },
  };

  const getContextProvider = () => {
    if (activeContext === "theme") {
      return (
        <ThemeContext.Provider value={contextValue.theme}>
          {children}
        </ThemeContext.Provider>
      );
    }
    return (
      <LanguageContext.Provider value={contextValue.language}>
        {children}
      </LanguageContext.Provider>
    );
  };

  return (
    <>
      <button onClick={() => setActiveContext("theme")}>Switch to Theme</button>
      <button onClick={() => setActiveContext("language")}>
        Switch to Language
      </button>
      {getContextProvider()}
    </>
  );
};

const Component = () => {
  const theme = useContext(ThemeContext);
  const language = useContext(LanguageContext);

  return (
    <div>
      {theme && <p>Dark Mode: {theme.darkMode ? "On" : "Off"}</p>}
      {language && <p>Language: {language.current}</p>}
    </div>
  );
};
```

</details>  
</br>

<details>
<summary>9. What would happen if the value passed to the `Provider` is a function or an object that is recreated on every render? How can this issue be mitigated?</summary>

- **What happens**:

  - React uses reference equality (`===`) to compare the new value with the previous one.
  - If the `value` is a function or object recreated on every render, consuming components will re-render unnecessarily, even if the value's actual content hasn't changed.

- **How to mitigate**:
  1. **Memoize the value**:
     ```jsx
     const memoizedValue = useMemo(() => ({ key: value }), [value]);
     <Context.Provider value={memoizedValue} />;
     ```
  2. **Use callbacks**:
     Use `useCallback` for functions:
     ```jsx
     const memoizedFunction = useCallback(() => doSomething(), []);
     <Context.Provider value={memoizedFunction} />;
     ```

</details>  
</br>

<details>
<summary>10. Explain how you would implement a context value that changes frequently, such as a `count` in a counter app, while minimizing re-renders.</summary>

- Use a **state management hook** like `useReducer` or `useState` to handle frequently changing values.
- Memoize other parts of the context to isolate the frequently changing state.

**Example**:

```jsx
import React, { createContext, useReducer, useContext } from "react";

const CounterContext = createContext();

const counterReducer = (state, action) => {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };
    case "DECREMENT":
      return { count: state.count - 1 };
    default:
      return state;
  }
};

export const CounterProvider = ({ children }) => {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <CounterContext.Provider value={{ state, dispatch }}>
      {children}
    </CounterContext.Provider>
  );
};

export const useCounter = () => useContext(CounterContext);

// Usage
const CounterComponent = () => {
  const { state, dispatch } = useCounter();

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>Increment</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>Decrement</button>
    </div>
  );
};
```

</details>  
</br>

<details>
<summary>11. What are some common pitfalls when using `createContext` and `useContext`? How can these be avoided?</summary>

**Pitfalls**:

1. **Re-rendering issues**:

   - Caused by passing new object or function references without memoization.
   - Solution: Use `useMemo` or `useCallback` to stabilize the `value`.

2. **Context value outside provider**:

   - Trying to consume a context without wrapping the component in its provider.
   - Solution: Add error handling in custom hooks.

3. **Overusing Context**:

   - Using Context API for frequently updated state can lead to performance issues.
   - Solution: Use other state management solutions like Redux or localized state with `useState`.

4. **Tight coupling**:

   - Consuming context directly in components makes them tightly coupled.
   - Solution: Use custom hooks to abstract the logic.

5. **Nested providers**:
   - Too many providers can make the component tree hard to manage.
   - Solution: Use a single context provider for grouped states when possible.

</details>  
</br>

<details>
<summary>12. What happens if you try to consume a context outside its provider? How would you debug this issue?</summary>

- **What happens**:

  - The consuming component will use the default value of the context (if provided).
  - If no default value exists, it may result in unexpected behavior or an error.

- **How to debug**:
  1. Check if the consuming component is wrapped in the provider.
  2. Add error handling in the custom hook:
     ```jsx
     const useCustomContext = () => {
       const context = useContext(SomeContext);
       if (!context) {
         throw new Error(
           "useCustomContext must be used within a SomeContext.Provider"
         );
       }
       return context;
     };
     ```

</details>  
</br>

<details>
<summary>13. Can the `useContext` hook be used in class components? If not, how would you share context data with a class component?</summary>

- **`useContext` cannot be used in class components** because it is a React hook and hooks can only be used in functional components.
- **To share context with class components**:
  1. Use `Context.Consumer`:
     ```jsx
     <Context.Consumer>
       {(value) => <ClassComponent value={value} />}
     </Context.Consumer>
     ```
  2. Pass context as a prop:
     ```jsx
     const value = useContext(MyContext);
     <ClassComponent value={value} />;
     ```

</details>  
</br>

<details>
<summary>14. How would you handle default values in `createContext` if the value type is non-primitive (e.g., an object or array)?</summary>

- Provide a sensible default value:

  ```jsx
  const MyContext = createContext({ key: "default", data: [] });
  ```

- Alternatively, use `null` and handle it in the consumer with error handling:

  ```jsx
  const MyContext = createContext(null);
  ```

- Use a custom hook to enforce handling default values:
  ```jsx
  const useMyContext = () => {
    const context = useContext(MyContext);
    if (!context) {
      throw new Error("useMyContext must be used within a Provider");
    }
    return context;
  };
  ```

</details>  
</br>

<details>
<summary>15. What are the limitations of the Context API for state management, and how would you overcome them in a real-world application?</summary>

**Limitations**:

1. Re-renders for all consuming components when the context value changes.
2. Not suitable for large applications with highly dynamic state changes.
3. Debugging and tracking state changes can be harder compared to Redux with DevTools.

**Overcoming limitations**:

1. Use `useMemo` to optimize performance.
2. Split context into smaller, specialized contexts.
3. Combine Context API with other state management libraries for complex apps.

</details>  
</br>

<details>
<summary>16. How can you measure the performance impact of using the Context API in your React application?</summary>

- **Tools and techniques**:
  1. Use React DevTools to inspect the components that re-render when context changes.
  2. Use logging or profiling to track re-renders.
  3. Use `React.memo` or `useMemo` to avoid unnecessary renders.

</details>  
</br>

<details>
<summary>17. Write a test case to verify that a component correctly consumes and displays data from a context.</summary>

**Test case example using React Testing Library**:

```jsx
import { render, screen } from "@testing-library/react";
import { MyContext } from "./MyContext";
import MyComponent from "./MyComponent";

test("renders context value", () => {
  const contextValue = { key: "test-value" };

  render(
    <MyContext.Provider value={contextValue}>
      <MyComponent />
    </MyContext.Provider>
  );

  expect(screen.getByText("test-value")).toBeInTheDocument();
});
```

</details>  
</br>

<details>
<summary>18. How would you test components that depend on a dynamically changing context value?</summary>

- **Mock the context provider**:
  Use multiple values in the test by wrapping the component with a mocked provider:

  ```jsx
  test("dynamic context value changes", () => {
    const { rerender } = render(
      <MyContext.Provider value={{ key: "initial-value" }}>
        <MyComponent />
      </MyContext.Provider>
    );

    expect(screen.getByText("initial-value")).toBeInTheDocument();

    rerender(
      <MyContext.Provider value={{ key: "updated-value" }}>
        <MyComponent />
      </MyContext.Provider>
    );

    expect(screen.getByText("updated-value")).toBeInTheDocument();
  });
  ```

</details>  
</br>

<details>
<summary>19. If you have a large tree of components, how would you debug performance issues caused by excessive re-renders due to Context API usage?</summary>

- **Steps**:
  1. Use React DevTools Profiler to find components that are re-rendering unnecessarily.
  2. Check `Context.Provider` values for unnecessary re-creation.
  3. Memoize context values using `useMemo`.
  4. Split large contexts into smaller ones.

</details>  
</br>

<details>
<summary>20. How does React's concurrent rendering mode affect the behavior of components using the Context API?</summary>

- **Impact**:

  - React may interrupt rendering to prioritize higher-priority updates.
  - Context updates might not immediately propagate if React is deferring lower-priority updates.

- **Considerations**:
  - Ensure stable context values with `useMemo` to avoid unexpected behavior.
  - Test your application in concurrent mode to identify potential issues.

</details>  
</br>

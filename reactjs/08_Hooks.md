<details>
<summary>1. Explain how the `useState` hook works. How does React ensure the state is preserved across renders?</summary>

**Answer**: The `useState` hook allows React components to maintain local state. When you call `useState(initialValue)`, React initializes state and returns a state variable and a setter function. React ensures the state is preserved by associating the state with the component's **render fiber**. During subsequent renders, React retrieves the correct state value by referencing the component's fiber tree node. The state is updated through the setter, and React triggers a re-render when it detects changes.

</details>  
</br>

<details>
<summary>2. What happens if you call the `setState` function inside a loop? Explain the implications and behavior of batching updates.</summary>

**Answer**: Calling `setState` inside a loop will enqueue multiple state updates. In React 18 and later, updates are **batched** by default during the same event loop execution. This means React processes all state updates at once and applies the final state after the loop finishes, reducing unnecessary renders. However, if `setState` depends on the previous state, you should use the functional updater pattern (`setState((prevState) => ...)`) to ensure correctness.

</details>  
</br>

<details>
<summary>3. How do you handle scenarios where the `setState` depends on the previous state? Why is this approach necessary?</summary>

**Answer**: Use the functional updater pattern:

```jsx
setState((prevState) => prevState + 1);
```

This is necessary because state updates in React are asynchronous and may be batched. Directly referencing the current state (e.g., `setState(state + 1)`) might result in stale state if multiple updates occur simultaneously. The functional updater ensures React always uses the latest state.

</details>  
</br>

<details>
<summary>4. Demonstrate how to debounce a `useState` update to prevent excessive renders during rapid user inputs, such as in a search bar.</summary>

**Answer**:

```jsx
import { useState, useEffect } from "react";

function SearchBar() {
  const [searchTerm, setSearchTerm] = useState("");
  const [debouncedTerm, setDebouncedTerm] = useState("");

  useEffect(() => {
    const handler = setTimeout(() => setDebouncedTerm(searchTerm), 300);
    return () => clearTimeout(handler); // Cleanup timeout
  }, [searchTerm]);

  useEffect(() => {
    if (debouncedTerm) {
      console.log("API call for:", debouncedTerm);
    }
  }, [debouncedTerm]);

  return (
    <input
      type="text"
      value={searchTerm}
      onChange={(e) => setSearchTerm(e.target.value)}
    />
  );
}
```

This ensures `setDebouncedTerm` is updated only after 300ms of no input, reducing excessive renders.

</details>  
</br>

<details>
<summary>5. What are the common pitfalls of forgetting the dependency array in `useEffect`? How does it affect performance and correctness?</summary>

**Answer**: Forgetting the dependency array means the effect will run **on every render**, leading to:

- **Performance issues**: Continuous execution of potentially expensive effects.
- **Incorrect behavior**: Unintended side effects, such as repeated API calls or infinite loops if the effect updates state causing a re-render.

To fix this, always specify dependencies to control when the effect should re-run.

</details>  
</br>

<details>
<summary>6. How can you achieve component lifecycle methods (`componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`) using `useEffect`? Provide examples.</summary>

**Answer**:

- **`componentDidMount`**: Use `useEffect` with an empty dependency array:

  ```jsx
  useEffect(() => {
    console.log("Component mounted");
  }, []);
  ```

- **`componentDidUpdate`**: Use `useEffect` with specific dependencies:

  ```jsx
  useEffect(() => {
    console.log("Component updated");
  }, [dependency]);
  ```

- **`componentWillUnmount`**: Return a cleanup function in the effect:
  ```jsx
  useEffect(() => {
    return () => console.log("Component unmounted");
  }, []);
  ```

Combine these patterns to mimic lifecycle methods.

</details>  
</br>

<details>
<summary>7. Explain the "closure trap" in `useEffect`. How can it lead to stale state issues, and how do you solve them?</summary>

**Answer**: The "closure trap" occurs when `useEffect` captures a stale version of a variable or state due to JavaScript closures. This happens because the effect retains the values from the render in which it was created.

**Example**:

```jsx
useEffect(() => {
  const interval = setInterval(() => console.log(count), 1000);
  return () => clearInterval(interval);
}, []);
```

Here, `count` will always log its initial value due to the stale closure.

**Solution**: Use the current value by adding the variable as a dependency:

```jsx
useEffect(() => {
  const interval = setInterval(() => console.log(count), 1000);
  return () => clearInterval(interval);
}, [count]);
```

</details>  
</br>

<details>
<summary>8. How do you prevent `useEffect` from running on the initial render but only on subsequent updates?</summary>

**Answer**: Use a flag or ref to track the initial render:

```jsx
import { useEffect, useRef } from "react";

function Component() {
  const isInitialRender = useRef(true);

  useEffect(() => {
    if (isInitialRender.current) {
      isInitialRender.current = false;
      return; // Skip on initial render
    }
    console.log("Effect ran after update");
  }, [dependency]);

  return <div>Component</div>;
}
```

This ensures the effect skips the first render and runs only on updates.

</details>  
</br>

<details>
<summary>9. Why is it a bad practice to include functions or objects as dependencies in `useEffect`? What alternatives exist to handle this situation?</summary>

**Answer**: Including functions or objects as dependencies can cause unnecessary re-renders because these values are often re-created on every render, even if their contents don't change. This can lead to infinite loops in effects.

**Alternatives**:

- **Memoize functions** using `useCallback`:
  ```jsx
  const memoizedCallback = useCallback(() => {
    // Function logic
  }, [dependencies]);
  ```
- **Memoize objects** using `useMemo`:
  ```jsx
  const memoizedObject = useMemo(() => ({ key: value }), [dependencies]);
  ```
  This ensures stable references and prevents unnecessary re-execution of the effect.

</details>  
</br>

<details>
<summary>10. How is `useContext` different from `Context.Consumer` in terms of re-renders? Explain with an example.</summary>

**Answer**: `useContext` triggers re-renders for the entire component whenever the context value changes, regardless of whether the component uses all parts of the context. In contrast, `Context.Consumer` can selectively re-render only the parts of the component that directly use the context value.

**Example**:

- Using `useContext`:

  ```jsx
  const contextValue = useContext(MyContext); // Entire component re-renders on context change
  ```

- Using `Context.Consumer`:
  ```jsx
  <MyContext.Consumer>
    {value => <span>{value}</span>} // Only <span> re-renders
  </MyContext.Consumer>
  ```

To optimize performance with `useContext`, you can split the context into smaller pieces or use memoization techniques.

</details>  
</br>

<details>
<summary>11. What are the limitations of `useContext` in a large application with deeply nested component trees? How can these limitations be mitigated?</summary>

**Answer**: Limitations of `useContext` include:

1. **Global re-renders**: Any change to the context value triggers re-renders for all components consuming the context, even if only a subset of components uses the updated value.
2. **Difficulty in debugging**: Tracking where the context value changes can be challenging in large applications.
3. **Reduced modularity**: Strong coupling to the context provider makes components less reusable.

**Mitigations**:

- Split contexts for different parts of the state.
- Use memoization (`React.memo`) to prevent unnecessary re-renders.
- Use libraries like `Recoil` or `Jotai` for fine-grained state management.

</details>  
</br>

<details>
<summary>12. Describe a scenario where `useContext` can lead to performance bottlenecks. How can you resolve it using memoization?</summary>

**Answer**: A performance bottleneck occurs when `useContext` causes unnecessary re-renders in deeply nested components because the context value changes, even if the component doesn't use the updated part of the context.

**Example**:
If a context contains both `user` and `theme`, changing `user` will re-render components that only depend on `theme`.

**Resolution**:

1. Split context into multiple smaller contexts (e.g., `UserContext` and `ThemeContext`).
2. Memoize provider values:
   ```jsx
   const userProviderValue = useMemo(() => ({ user, setUser }), [user]);
   <UserContext.Provider value={userProviderValue}>
   ```

This prevents unnecessary re-renders of components relying only on stable parts of the context.

</details>  
</br>

<details>
<summary>13. Why must hooks be called in the same order during every render? Explain the technical reasoning behind this restriction.</summary>

**Answer**: React uses a single array to track the state and effects for each component. Hooks are assigned specific indices in this array during the initial render. If the order of hook calls changes, React cannot correctly associate state values or effects with the appropriate hook, leading to unpredictable behavior or runtime errors.

By enforcing consistent order, React maintains a predictable mapping of hooks to their stored state values.

</details>  
</br>

<details>
<summary>14. Can you call hooks inside a conditional block or a loop? Why or why not? How can you refactor code to avoid this?</summary>

**Answer**: Hooks **cannot** be called inside conditional blocks or loops because this would break the rule that hooks must be called in the same order during every render. This can lead to React losing track of the hook's state.

**Refactor**:
Instead of conditional hooks, use conditionally executed logic:

```jsx
useEffect(() => {
  if (condition) {
    // Execute logic
  }
}, [condition]);
```

For loops, call hooks outside the loop and manage data accordingly.

</details>  
</br>

<details>
<summary>15. What happens if you use a custom hook that internally breaks the rules of hooks? How does React behave in such cases?</summary>

**Answer**: If a custom hook violates the rules of hooks (e.g., calling hooks conditionally), React's execution order is disrupted. This leads to runtime errors like:

```
Error: Hooks can only be called inside the body of a function component.
```

To avoid this, ensure all hooks within a custom hook follow the rules of hooks and maintain consistent call order.

</details>  
</br>

<details>
<summary>16. How do React hooks rely on the call stack and execution context? Explain how this influences their usage rules.</summary>

**Answer**: Hooks rely on React's internal mechanism of tracking the call order in the component's execution context. During each render, React executes the component function and matches each hook call to its stored state or effect based on the order of calls. If the order is inconsistent, React's call stack becomes misaligned, breaking the state management.

This necessitates rules like consistent order and calling hooks only at the top level of components or custom hooks.

</details>  
</br>

<details>
<summary>17. How would you debug a component where hooks seem to be firing out of order or causing unexpected state behavior?</summary>

**Answer**:

1. **Check hook rules**: Ensure hooks are called consistently and not inside loops, conditions, or nested functions.
2. **Add logging**: Log state and effect calls to trace execution flow.
3. **Custom hook isolation**: Verify custom hooks adhere to rules and don't cause side effects.
4. **React DevTools**: Use React DevTools to inspect hook states and identify issues.

</details>  
</br>

<details>
<summary>18. If a hook depends on variables that change frequently, how can you optimize it to prevent performance degradation?</summary>

**Answer**:

- Use `useCallback` to memoize functions:
  ```jsx
  const memoizedCallback = useCallback(() => {
    // Logic
  }, [dependencies]);
  ```
- Use `useMemo` to memoize computed values:
  ```jsx
  const memoizedValue = useMemo(() => computeExpensiveValue(), [dependencies]);
  ```
- Avoid redundant dependencies by restructuring the logic to minimize updates.

</details>  
</br>

<details>
<summary>19. What strategies would you use to manage state logic shared across multiple components, while adhering to the rules of hooks?</summary>

**Answer**:

1. **Context API**: Use `useContext` to share global state.
2. **Custom hooks**: Encapsulate shared state logic in reusable custom hooks.
3. **State management libraries**: Use libraries like Redux, Recoil, or Zustand for more complex shared state.

</details>  
</br>

<details>
<summary>20. Can you use multiple state variables with `useState` or combine them into one object? What are the trade-offs of each approach?</summary>

**Answer**:

- **Multiple `useState` variables**:

  - Easier to manage individual states.
  - Fine-grained updates (better performance for unrelated state updates).

- **Single object state**:
  - Cleaner grouping of related states.
  - Requires spreading the object during updates:
    ```jsx
    setState((prevState) => ({ ...prevState, newValue }));
    ```

Choose based on whether states are logically related.

</details>  
</br>

<details>
<summary>21. Explain how you can implement your own version of `useState` using `useReducer`.</summary>

**Answer**:

```jsx
function useCustomState(initialState) {
  const reducer = (state, action) => action;
  const [state, dispatch] = useReducer(reducer, initialState);

  const setState = (newState) => dispatch(newState);

  return [state, setState];
}
```

This mimics `useState` by directly dispatching the new state.

</details>  
</br>

<details>
<summary>22. How does React decide when to clean up an effect, and how does the cleanup process work internally?</summary>

**Answer**: React cleans up an effect when:

1. The component unmounts.
2. The dependencies of the effect change.

React calls the cleanup function provided in the `useEffect` return value to clean up side effects (e.g., removing event listeners, clearing intervals).

</details>  
</br>

<details>
<summary>23. Write a custom hook that utilizes `useState`, `useEffect`, and `useContext` to manage a global state that syncs with local storage.</summary>

**Answer**:

```jsx
function useLocalStorage(key, defaultValue) {
  const [state, setState] = useState(() => {
    const storedValue = localStorage.getItem(key);
    return storedValue ? JSON.parse(storedValue) : defaultValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(state));
  }, [key, state]);

  return [state, setState];
}
```

</details>  
</br>

<details>
<summary>24. What are the implications of using hooks in a concurrent rendering environment like React 18? How can you ensure compatibility?</summary>

**Answer**: Hooks must remain pure and side-effect free to support React's concurrent rendering. Effects should clean up properly and avoid mutating shared state during renders. Use:

- `useTransition` for smoother updates.
- Proper memoization (`useMemo`, `useCallback`) to prevent unnecessary computations.

</details>  
</br>

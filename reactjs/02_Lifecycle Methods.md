<details>

<summary>1. Explain the React component lifecycle phases.</summary>
React component lifecycle phases include:

- **Mounting**: When a component is inserted into the DOM. Lifecycle methods include:

  - `constructor`
  - `getDerivedStateFromProps`
  - `render`
  - `componentDidMount`

- **Updating**: When a component re-renders due to state or prop changes. Lifecycle methods include:

  - `getDerivedStateFromProps`
  - `shouldComponentUpdate`
  - `render`
  - `getSnapshotBeforeUpdate`
  - `componentDidUpdate`

- **Unmounting**: When a component is removed from the DOM. Lifecycle method:
  - `componentWillUnmount`
    </details></br>

<details>

<summary>2. How would you optimize a component's rendering performance during the updating phase?</summary>
To optimize rendering performance during the updating phase:

- Use `shouldComponentUpdate` for class components.
- Use `React.memo` for functional components.
- Memoize expensive calculations using `React.useMemo` or `React.useCallback`.
- Ensure child components re-render only when their props or state changes.
- Avoid deep object or array comparisons in props and state.
  </details></br>

<details>

<summary>3. What problems might arise if you perform asynchronous operations directly inside a lifecycle method?</summary>
Problems with asynchronous operations in lifecycle methods:

- **Memory Leaks**: If the component unmounts before the operation completes.
- **State Updates on Unmounted Components**: Can cause warnings or unexpected behavior.
- **Race Conditions**: Operations completing out of order can lead to inconsistent state.

Solutions include cleaning up operations in `componentWillUnmount` or using a flag to track the mounted state.

</details></br>

<details>

<summary>4. How does the behavior of lifecycle methods differ when React is used in strict mode?</summary>

In Strict Mode:

- Certain lifecycle methods, such as `componentDidMount` and `componentDidUpdate`, are invoked twice during development.
- This helps detect unexpected side effects and improves code robustness.
- `componentWillMount`, `componentWillReceiveProps`, and `componentWillUpdate` are deprecated and not used in strict mode.
  </details></br>

<details>

<summary>5. What is the purpose of `componentDidMount`?</summary>

`componentDidMount` is used for:

- Initializing non-React dependencies.
- Making API calls to fetch data.
- Setting up event listeners or subscriptions.
- Performing DOM manipulations.
  </details></br>

<details>

<summary>6. How can you simulate `componentDidMount` functionality in a functional component?</summary>

Simulate `componentDidMount` in a functional component using `useEffect` with an empty dependency array:

```javascript
useEffect(() => {
  // Initialization logic here
}, []);
```

</details></br>

<details>

<summary>7. What happens if a `setState` call is made inside `componentDidMount`?</summary>

If `setState` is called inside `componentDidMount`:

- A re-render is triggered after the initial render.
- It is safe to use, but it causes an additional render, which can impact performance if overused.
  </details></br>

<details>

<summary>8. What is the difference between `componentDidUpdate` and `getSnapshotBeforeUpdate`?</summary>

**Differences**:

- `componentDidUpdate`: Executes after the DOM has updated. Used for side effects like API calls or logging.
- `getSnapshotBeforeUpdate`: Executes just before DOM updates. Used for capturing DOM states (e.g., scroll position) before the update.

Use `getSnapshotBeforeUpdate` when you need pre-update information, and process it in `componentDidUpdate`.

</details></br>

<details>

<summary>9. How can you prevent infinite loops in `componentDidUpdate`?</summary>

Prevent infinite loops by comparing current and previous props or state before triggering an update:

```javascript
componentDidUpdate(prevProps, prevState) {
  if (prevProps.data !== this.props.data) {
    // Perform actions or state updates
  }
}
```

This ensures `setState` or other actions are only executed when a relevant change occurs.

</details></br>

<details>

<summary>10. When should you use `getSnapshotBeforeUpdate` instead of `componentDidUpdate`?</summary>

Use `getSnapshotBeforeUpdate` when:

- You need to capture DOM state before updates, such as scroll positions or dimensions.
- Example: Save scroll position before re-rendering a list to restore it post-update.
  </details></br>

<details>
<summary>11. Why is it important to use `componentWillUnmount`?</summary>

`componentWillUnmount` is important for cleanup tasks, such as:

- Removing event listeners to prevent memory leaks.
- Cancelling API calls or subscriptions.
- Clearing timers, intervals, or other scheduled operations.
- Ensuring no further updates are made to unmounted components.

Neglecting proper cleanup can lead to performance issues or unexpected errors.

</details></br>

<details>

<summary>12. How would you ensure that a component's `setState` does not execute after it has unmounted?</summary>

To prevent `setState` from executing on an unmounted component:

- Use a flag to track if the component is mounted:

  ```javascript
  let isMounted = true;

  componentDidMount() {
    isMounted = true;
  }

  componentWillUnmount() {
    isMounted = false;
  }

  fetchData().then(() => {
    if (isMounted) {
      this.setState({ data });
    }
  });
  ```

- Use libraries like `AbortController` for cancelling fetch requests.
  </details></br>

<details>
<summary>13. How would you handle side effects across multiple lifecycle phases in a class component?</summary>

Handle side effects across lifecycle phases by splitting tasks appropriately:

- **`componentDidMount`**: Fetch initial data or set up subscriptions.
- **`componentDidUpdate`**: Update data when specific props/state change.
- **`componentWillUnmount`**: Clean up resources or subscriptions.

Example:

```javascript
componentDidMount() {
  this.fetchData();
}

componentDidUpdate(prevProps) {
  if (prevProps.id !== this.props.id) {
    this.fetchData();
  }
}

componentWillUnmount() {
  this.cleanupResources();
}
```

</details></br>

<details>
<summary>14. If a parent component unmounts a child component, what happens to the child component's lifecycle methods?</summary>

When a parent unmounts a child:

- The child’s `componentWillUnmount` is triggered.
- No other lifecycle methods are called during unmounting.
- Cleanup tasks defined in `componentWillUnmount` (like removing listeners or cancelling timers) are executed.
  </details></br>

<details>
<summary>15. Explain how React manages lifecycle methods internally with the Fiber architecture.</summary>

React Fiber manages lifecycle methods by:

- Breaking rendering work into small units called fibers.
- Prioritizing updates based on urgency (e.g., user interactions over background tasks).
- Scheduling and pausing rendering work as needed for smoother user experience.
- Invoking lifecycle methods during reconciliation for mount, update, or unmount operations.
  </details></br>

<details>
<summary>16. How can you ensure that data fetched in `componentDidMount` is correctly cleaned up in `componentWillUnmount`?</summary>

Ensure proper cleanup of fetched data by:

- Cancelling API calls using tools like `AbortController`:

  ```javascript
  const controller = new AbortController();

  componentDidMount() {
    fetch('/api/data', { signal: controller.signal })
      .then(response => response.json())
      .then(data => this.setState({ data }))
      .catch(err => {
        if (err.name === 'AbortError') {
          console.log('Fetch aborted');
        }
      });
  }

  componentWillUnmount() {
    controller.abort();
  }
  ```

- Unsubscribing from any event listeners or observables.
  </details></br>

<details>
<summary>17. How can you replicate the behavior of `componentDidUpdate` in a functional component?</summary>

Replicate `componentDidUpdate` using `useEffect` with dependencies:

```javascript
useEffect(() => {
  // Logic here runs after updates to the dependency
}, [dependency]);
```

If you need to compare previous values, use a custom hook or a `useRef` to store the previous state/prop.

</details></br>

<details>
<summary>18. What are the differences in handling cleanup between `componentWillUnmount` and the cleanup function in `useEffect`?</summary>

**`componentWillUnmount`**:

- Runs once during unmounting.
- Used for cleanup in class components.

**Cleanup in `useEffect`**:

- Runs either during unmount or before the next effect re-runs.
- More flexible as it covers scenarios like prop/state changes.

Example:

```javascript
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Timer running");
  }, 1000);

  return () => {
    clearInterval(timer); // Cleanup
  };
}, []);
```

</details></br>

<details>
<summary>19. What happens if you forget to include a dependency in the dependency array of `useEffect`?</summary>

If a dependency is omitted:

- The effect does not re-run when the omitted dependency changes.
- This can cause bugs due to stale variables or incorrect data.
- Example:
  ```javascript
  useEffect(() => {
    console.log("This runs only once, even if dependency changes.");
  }, []); // Missing dependencies
  ```
  </details></br>

<details>
<summary>20. What are some common pitfalls when transitioning lifecycle methods in a class component to functional components with hooks?</summary>

Common pitfalls include:

- **Incorrect Dependency Arrays**: Forgetting dependencies or adding too many can lead to unexpected re-renders or stale data.
- **Handling Cleanup**: Forgetting to add cleanup logic in `useEffect` leads to memory leaks.
- **Nested Effects**: Overusing multiple `useEffect` hooks can make code harder to read and maintain.
- **State Management**: Over-complicating state updates when transitioning from `setState` to `useState`.

Solution: Refactor gradually and test thoroughly to ensure functionality remains intact.

</details></br>

<details>
  <summary>1. How would you decide between `useReducer` and `useState` when building a component? Provide specific scenarios.</summary>

**Answer:**

- `useState` is ideal for simple, local state management, where state updates are not complex. For example, when toggling a boolean, managing a form input, or handling simple counters, `useState` is more appropriate because it’s easier to set up and read.
- `useReducer` should be used when the state logic is more complex, particularly when multiple state variables need to change in response to different actions. For example, managing a complex form with multiple fields, or handling state changes based on external actions (such as fetching data), `useReducer` helps by centralizing the state update logic. Another scenario is when you need to implement patterns like undo/redo or handle complex state transitions based on the dispatched actions.
- **Example:** For a form with multiple input fields and validation logic, `useReducer` would manage different actions (e.g., field update, validation error) in a centralized manner, improving maintainability and avoiding the complexity of managing multiple `useState` calls.
</details>
<br>

<details>
  <summary>2. What are the trade-offs of using `useReducer` in terms of code readability, performance, and debugging compared to `useState`?</summary>

**Answer:**

- **Code Readability:** `useState` is simpler to read and maintain, as each state variable is self-contained. For smaller components, `useState` provides clarity by having separate state variables. However, with complex state changes, `useReducer` makes state logic more manageable by centralizing action handling in one place, but it can be harder to follow at first glance, especially for developers unfamiliar with reducers.
- **Performance:** From a performance perspective, `useReducer` might introduce some overhead due to the need to dispatch actions and re-evaluate state with each update. However, in complex state scenarios, it helps avoid unnecessary re-renders as actions can be grouped, and changes can be batched. `useState` can result in more renders, as each state change triggers a re-render for its component.
- **Debugging:** `useState` can be easier to debug in simple cases because you can trace the value of each state directly. With `useReducer`, debugging might involve understanding actions and their payloads, and following through multiple action dispatches. However, tools like Redux DevTools (when used with `useReducer`) provide powerful debugging and state tracking features.
</details>
<br>

<details>
  <summary>3. How do you handle side effects in a `useReducer` implementation?</summary>

**Answer:**

- Side effects in `useReducer` can be handled using the `useEffect` hook. The `useEffect` hook listens for changes in the state managed by the reducer, and based on those changes, it can trigger actions such as API calls, data manipulation, or other side effects.
- If an asynchronous side effect is needed (such as an API request), you can dispatch an action to indicate that the side effect has started, and once the side effect is complete, dispatch another action with the result. This keeps the reducer's logic pure and predictable.
- Example:

  ```js
  const [state, dispatch] = useReducer(reducer, initialState);

  useEffect(() => {
    const fetchData = async () => {
      dispatch({ type: "FETCH_REQUEST" });
      try {
        const result = await fetchDataFromApi();
        dispatch({ type: "FETCH_SUCCESS", payload: result });
      } catch (error) {
        dispatch({ type: "FETCH_FAILURE", payload: error });
      }
    };
    fetchData();
  }, []);
  ```

</details>
<br>

<details>
  <summary>4. Can you describe a real-world application where `useReducer` is a better choice than `useState`? How would you handle state transitions for undo/redo functionality?</summary>

**Answer:**

- **Example Application:** A drawing application with multiple tools (pen, eraser, shapes), where the user can draw on a canvas and then need to undo/redo their actions. In this case, `useReducer` is better because the state transitions are more complex, involving multiple actions like adding a shape, changing the drawing color, or clearing the canvas. Each of these actions requires state updates that might depend on previous states, and you need a centralized way of managing these updates.

- **Undo/Redo Handling:**
  To implement undo/redo functionality, we can store the previous states in an array (history). Whenever a new action is performed (like drawing a shape), you push the new state to the history array. To handle undo, we pop the last state from the history and revert to that state.

  ```js
  const initialState = {
    drawingHistory: [],
    currentDrawing: null,
  };

  function reducer(state, action) {
    switch (action.type) {
      case "DRAW_SHAPE":
        return {
          ...state,
          currentDrawing: action.payload,
          drawingHistory: [...state.drawingHistory, state.currentDrawing],
        };
      case "UNDO":
        const previousState =
          state.drawingHistory[state.drawingHistory.length - 1];
        return {
          ...state,
          currentDrawing: previousState,
          drawingHistory: state.drawingHistory.slice(0, -1),
        };
      default:
        return state;
    }
  }

  const [state, dispatch] = useReducer(reducer, initialState);
  ```

This approach helps in managing state transitions more effectively, and `useReducer` provides a clear, predictable way of handling multiple actions in response to user interactions.

</details>
<br>

<details>
  <summary>5. How would you manage a scenario where multiple unrelated states in a component become entangled when using `useReducer`?</summary>

**Answer:**

- When using `useReducer`, it's important to keep the state logically organized. If multiple unrelated states are becoming entangled, it might be a sign that the reducer is managing too much state or the state is too complex.
- One solution is to **split reducers** into smaller, more focused ones. This way, each reducer can handle a specific part of the state and related actions, ensuring that each piece of state is managed independently.
- **Example:** In a component managing both user authentication and user profile, instead of managing both states in a single reducer, you could have one reducer for authentication and another for profile management:

  ````js
  const authReducer = (state, action) => {
  switch (action.type) {
  case 'LOGIN':
  return { ...state, user: action.payload, isAuthenticated: true };
  case 'LOGOUT':
  return { ...state, user: null, isAuthenticated: false };
  default:
  return state;
  }
  };

      const profileReducer = (state, action) => {
        switch (action.type) {
          case 'UPDATE_PROFILE':
            return { ...state, profile: action.payload };
          default:
            return state;
        }
      };

      const [authState, dispatchAuth] = useReducer(authReducer, { user: null, isAuthenticated: false });
      const [profileState, dispatchProfile] = useReducer(profileReducer, { profile: null });
      ```
      - By splitting the reducers, we avoid entanglement of unrelated states and make the code more maintainable.

  </details>
  <br>
  ````

<details>
  <summary>6. What are the performance implications of using Context API compared to prop drilling in large-scale applications? How do you mitigate performance issues with Context?</summary>

**Answer:**

- **Performance Implications:**
  - **Context API** can lead to unnecessary re-renders when the context value changes because it triggers re-renders for all components consuming the context. In a large-scale application with many components subscribing to context, this can become a performance bottleneck.
  - **Prop drilling** avoids this issue by directly passing down state as props, which can be more performance-efficient in some cases. However, in deeply nested components, it can lead to cumbersome code and maintenance challenges.
- **Mitigation Strategies:**
  - **Memoization:** To avoid unnecessary re-renders, you can use `React.memo` for components and `useMemo` for context values to ensure that components only re-render when the context value actually changes.
  - **Context Splitting:** For large applications, it’s a good idea to split the context into smaller, more specific contexts, so components only subscribe to the relevant parts of the state.
  - **Lazy Loading and Code Splitting:** Using techniques like lazy loading can help in only loading components when they are needed, thus reducing the initial render time and improving performance.

Example of using `useMemo` for optimizing context:

```js
const MemoizedContext = React.createContext();

const MyComponent = () => {
  const contextValue = useMemo(() => ({ value: "some value" }), []);
  return (
    <MemoizedContext.Provider value={contextValue}>
      ...
    </MemoizedContext.Provider>
  );
};
```

</details>
<br>

<details>
  <summary>7. When would you consider prop drilling as a better alternative to using Context API? Can you give a practical example?</summary>

**Answer:**

- **Prop drilling** may be preferable when the state is used by only a few components within a specific part of the component tree. Context API is generally useful when state needs to be shared across a large portion of the component tree, but using it unnecessarily can introduce performance overhead.
- A practical example could be a simple component tree where only a few components need access to a specific state. Using prop drilling in this case would keep the application simpler and more efficient.

Example:
In a small form component where only the parent and a couple of child components need to know the form’s state, prop drilling is sufficient:

```js
const Parent = () => {
  const [formState, setFormState] = useState({ name: "", email: "" });

  return <Child formState={formState} setFormState={setFormState} />;
};

const Child = ({ formState, setFormState }) => {
  return (
    <InputField
      value={formState.name}
      onChange={(e) => setFormState({ ...formState, name: e.target.value })}
    />
  );
};
```

This approach avoids the complexity of context and is more efficient for small applications.

</details>
<br>

<details>
  <summary>8. How does the Context API handle updates and how can it lead to unnecessary re-renders? Provide a detailed explanation.</summary>

**Answer:**

- **Context API Updates:**

  - When a value provided by a context changes, **all components consuming that context** will re-render, regardless of whether they actually use the updated value. This can lead to performance issues, especially in large applications with deeply nested components.
  - Re-renders are triggered by context changes because React’s default behavior is to re-render all consumers of the context whenever the context value changes, even if the value hasn't changed for the particular consumer.

- **Avoiding Unnecessary Re-renders:**
  - **Memoization:** Using `useMemo` to memoize the context value can help prevent unnecessary re-renders by ensuring that the context provider only re-renders when its value actually changes.
  - **Component-level optimization:** Use `React.memo` on components consuming context to ensure they only re-render when their props (or context values) change.
  - **Context Splitting:** By splitting the context into smaller, more focused contexts, each component only subscribes to the specific piece of state it needs, reducing the impact of context updates.

Example:

```js
const Context = React.createContext();

const App = () => {
  const [count, setCount] = useState(0);
  const value = useMemo(() => ({ count, setCount }), [count]);

  return (
    <Context.Provider value={value}>
      <Component />
    </Context.Provider>
  );
};
```

This will prevent unnecessary re-renders of `Component` if the `count` value remains the same.

</details>
<br>

<details>
  <summary>9. What are the limitations of Context API in managing state for highly dynamic components? How would you design a better state management solution for such scenarios?</summary>

**Answer:**

- **Limitations of Context API:**

  - **Frequent State Changes:** In highly dynamic components where state updates frequently, the Context API may lead to performance issues due to excessive re-renders, as any change in the context value triggers a re-render of all consuming components.
  - **Scalability:** For complex state logic or scenarios where state needs to be shared across many components, the Context API can become difficult to maintain and scale.

- **Better State Management Solution:**
  - For highly dynamic components, third-party state management libraries such as **Redux** or **Zustand** could be more appropriate, as they are optimized for handling frequent updates and provide better control over re-renders.
  - Another solution is to **split context** and use it more sparingly, providing smaller chunks of state to components that only need them.
  - For some applications, **local state** in components or using `useReducer` for complex state management might be a better fit to avoid the overhead of context.

Example:

```js
const countState = useReducer(reducer, initialState);
```

This allows more control over state updates, especially when multiple components update different parts of the state concurrently.

</details>
<br>

<details>
  <summary>10. Imagine you’re refactoring a legacy application that uses deep prop drilling for passing data. What challenges would you face when transitioning to Context API, and how would you address them?</summary>

**Answer:**

- **Challenges in Refactoring:**

  - **Performance Impact:** Transitioning to Context API can initially result in unnecessary re-renders, especially if the context is not optimized using `useMemo` or `React.memo`.
  - **Initial Learning Curve:** Developers might face a learning curve as they transition from prop drilling to using context, especially if they are unfamiliar with its patterns.
  - **Potential for Overuse:** Overusing Context API for small or localized state can lead to unnecessary complexity in the application.

- **Addressing the Challenges:**
  - **Splitting Context:** Start by identifying parts of the state that need to be shared globally and break the context into smaller, more focused pieces.
  - **Optimizing Performance:** Use `useMemo` to ensure that context values do not cause unnecessary re-renders. Also, make use of `React.memo` to optimize re-renders of components consuming the context.
  - **Gradual Transition:** Refactor incrementally, moving only the most appropriate parts of the state to the context while retaining local state for less dynamic data.

Example:

```js
const ThemeContext = React.createContext();

const App = () => {
  const themeValue = useMemo(() => ({ theme: "dark" }), []);
  return <ThemeContext.Provider value={themeValue}>...</ThemeContext.Provider>;
};
```

This minimizes the number of unnecessary re-renders caused by updates to the context value.

</details>
<br>

<details>
  <summary>11. You’re building a React app with deeply nested components. Some state is simple and local, while other state is shared globally but updates frequently. How would you architect the app? Use both `useReducer` and Context API in your answer.</summary>
  
  **Answer:**
  - **Architecture:**
    - For **local state**, use `useState` or `useReducer` within individual components. This avoids unnecessary complexity for simple states and keeps the local logic isolated.
    - For **global state** that needs to be shared across deeply nested components, use **Context API**. You can combine it with `useReducer` to handle complex state transitions (e.g., user authentication, theme management).
  
  - **Optimizations:**
    - **Splitting Context:** Divide the context into smaller, targeted contexts for specific parts of the app (e.g., user state, theme state, notification state). This will limit the impact of state changes and reduce re-renders.
    - **Memoization:** Use `useMemo` to prevent unnecessary re-renders of consumers that don’t need to update on each state change.
  
  Example:
  ```js
  const UserContext = React.createContext();
  const ThemeContext = React.createContext();

    const App = () => {
    const [userState, dispatchUser] = useReducer(userReducer, initialUserState);
    const [themeState, dispatchTheme] = useReducer(themeReducer, initialThemeState);

        return (
        <UserContext.Provider value={{ state: userState, dispatch: dispatchUser }}>
            <ThemeContext.Provider value={{ state: themeState, dispatch: dispatchTheme }}>
            <Component />
            </ThemeContext.Provider>
        </UserContext.Provider>
        );

    };
    ```

This allows for efficient state management in a large application with global and local state requirements.

</details>
<br>

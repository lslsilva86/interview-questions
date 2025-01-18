## **React.js**:

**Basic Concepts**:

<details>
<summary> 
How does React's Virtual DOM improve performance compared to direct DOM manipulation?</summary>

The Virtual DOM is a lightweight copy of the real DOM. React updates the Virtual DOM, calculates the difference (diffing algorithm), and applies changes to the real DOM in batches, reducing costly direct DOM operations.

</details>

<details>
<summary>
Can you explain how React updates the DOM under the hood</summary>

React uses a reconciliation process:

- It compares the Virtual DOM with a new version after state/props changes.
- It identifies changes using a diffing algorithm.
- Only the necessary changes are updated in the real DOM.

</details>

<details>
<summary>
What are the limitations of using JSX in React</summary>

- JSX is not valid JavaScript, requiring a compilation step (via Babel).
- Conditional rendering can be verbose.
- Complexity increases with deeply nested JSX.

</details>

<details>
<summary>
How does React handle rendering when the state or props change</summary>

React triggers a re-render when the state or props change. It re-runs the `render()` method, updates the Virtual DOM, and applies differences to the real DOM.

</details>

<details>
<summary>
What happens if a React component's state is updated inside the `render` method</summary>

This leads to an infinite loop, as `render` calls `setState`, causing another `render`.

</details>

<details>
<summary>
How do you handle default props in both functional and class components</summary>

- **Functional Components:** Use `defaultProps`:
  ```js
  MyComponent.defaultProps = { propName: "defaultValue" };
  ```
- **Class Components:** Use the same approach or set default values in the constructor.

</details>

<details>
<summary>
What is the difference between controlled and uncontrolled components in React forms</summary>

- **Controlled Component:** Form data is managed by React state (via `value` or `checked`).
- **Uncontrolled Component:** Form data is managed by the DOM directly using refs.

</details>

<details>
<summary>
How does React ensure immutability when updating state</summary>

React enforces immutability by requiring updates through `setState` (class) or state updater functions (`useState`). This ensures the old state is not modified, enabling the diffing algorithm to work efficiently.

</details>

<details>
<summary>
What are the common pitfalls when using keys in a list, and why are they important</summary>

- Avoid using array indexes as keys—they lead to bugs with reordering.
- Unique, stable keys ensure React can efficiently update and reorder list items.

</details>

<details>
 
<summary>
How does React handle asynchronous updates to state using `setState`</summary>

- `setState` is batched and asynchronous for performance.
- State updates are merged, not replaced, and callbacks can access the latest state.

</details>

<details>
 
<summary>
What is the difference between `props.children` and `React.Children`</summary>

- `props.children` is the child node(s) passed to a component.
- `React.Children` provides utility methods (e.g., mapping, counting) for handling `props.children`.

</details>

<details>
 
<summary>
Why is it recommended not to directly mutate state in React</summary>

Directly mutating state:

- Bypasses React's change detection.
- Prevents re-renders.
- Can lead to unpredictable bugs.

</details>

<details>
 
<summary>
What is the difference between shallow rendering and deep rendering in React</summary>

- **Shallow Rendering:** Tests a component without rendering child components.
- **Deep Rendering:** Fully renders the component tree, including children.

</details>

<details>
 
<summary>
How does React differentiate between a DOM tag and a custom component in JSX</summary>

- DOM tags (e.g., `div`, `span`) are lowercase.
- Custom components start with an uppercase letter.

</details>

<details>
 
<summary>
What is the significance of the `key` prop, and how does it help React optimize rendering</summary>

The `key` prop uniquely identifies elements in a list, enabling React to:

- Track and match elements efficiently.
- Prevent unnecessary re-renders or DOM manipulations.

</details>

<details>
 
<summary>
How can you optimize the rendering of components with large data sets in React</summary>

- Use windowing libraries like `react-window` or `react-virtualized`.
- Implement `React.memo` and avoid re-rendering unchanged components.

</details>

<details>
 
<summary>
What happens if you call `setState` with the same value as the current state</summary>

React will skip re-rendering, as it detects no change in state.

</details>

<details>
 
<summary>
Can you explain why React is considered declarative rather than imperative</summary>

React focuses on **what** the UI should look like (declarative) rather than **how** to perform updates (imperative). React abstracts the update logic.

</details>

<details>
 
<summary>
How does React reconcile differences when rendering lists of elements</summary>

React compares elements using their `key` props. Elements with unchanged keys are reused; others are added, updated, or removed.

</details>

<details>
 
<summary>
What is the role of `getDerivedStateFromProps`, and when should it be used</summary>

Used to derive state from props when the state depends on external changes. It's a static lifecycle method in class components.

</details>

<details>
 
<summary>
How do you prevent a child component from re-rendering when the parent component updates</summary>

- Use `React.memo` to memoize functional components.
- Implement `shouldComponentUpdate` in class components.

</details>

<details>
 
<summary>
What are the potential drawbacks of using context for state management in large applications</summary>

- Overuse leads to performance issues due to unnecessary re-renders.
- Difficult to debug and maintain compared to tools like Redux.

</details>

<details>
 
<summary>
How does React differ from traditional MVC frameworks like AngularJS</summary>

- React focuses only on the View layer.
- It uses a unidirectional data flow instead of two-way data binding.

</details>

<details>
 
<summary>
What is the difference between `createElement` and JSX in React</summary>

- `createElement`: A JavaScript function to create React elements.
- JSX: A syntax sugar that compiles into `createElement` calls.

</details>

<details>
 
<summary>
How do you structure a React application to ensure scalability and maintainability</summary>

- Modular file organization (e.g., feature-based, component-based).
- Clear separation of concerns (e.g., state management, API calls, and UI).
- Use tools like TypeScript for type safety and code maintainability.

</details>

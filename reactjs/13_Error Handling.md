<details>

<summary>1. What is the role of `componentDidCatch` in class-based components? How does it differ from `getDerivedStateFromError`?</summary>

- **`componentDidCatch`** is used to catch JavaScript errors anywhere in the component tree, log the error details, and perform side effects like reporting the error to an external logging service.
- **`getDerivedStateFromError`** is a static method used to update the component's state after an error is thrown. It is specifically for rendering fallback UI.
- **Key Difference**: `componentDidCatch` is for logging and performing side effects, while `getDerivedStateFromError` is only for updating state to render fallback UI.

</details>  
</br>

<details>

<summary>2. Can `componentDidCatch` be used for rendering fallback UI? Why or why not?</summary>

- No, `componentDidCatch` cannot directly render fallback UI. It is a side-effect-only lifecycle method.
- To render fallback UI, you must update the component state using `getDerivedStateFromError` and conditionally render based on that state.

</details>  
</br>

<details>

<summary>3. What types of errors can be caught by an error boundary? Which errors will not be caught?</summary>

- **Caught Errors**:

  - Errors in the rendering phase (e.g., during `render()` or child component rendering).
  - Errors in lifecycle methods like `componentDidMount` or `componentDidUpdate`.
  - Errors in constructors or during reconciliation.

- **Not Caught**:
  - Errors in event handlers (use `try-catch` in event handlers).
  - Errors in asynchronous code like Promises or `setTimeout`.
  - Errors outside the React component tree (e.g., global or DOM errors).

</details>  
</br>

<details>

<summary>4. Explain how error boundaries work with child components and whether they can catch errors in asynchronous code, such as `setTimeout` or Promises.</summary>

- Error boundaries catch errors thrown in child components during rendering, lifecycle methods, and reconciliation.
- **Asynchronous Code**: Error boundaries do not catch errors in `setTimeout` or Promises directly because these errors occur outside the React rendering process. You must handle them using `try-catch` or by rejecting Promises with explicit error handling.

</details>  
</br>

<details>

<summary>5. In what scenarios would `getDerivedStateFromError` and `componentDidCatch` both be used in the same component? Provide an example.</summary>

- Use both when you want to:
  1. Update the UI to show a fallback (using `getDerivedStateFromError`).
  2. Log or handle the error (using `componentDidCatch`).

**Example**:

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true }; // Update state for fallback UI
  }

  componentDidCatch(error, errorInfo) {
    console.error("Logging error:", error, errorInfo); // Log error details
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

</details>  
</br>

<details>

<summary>6. Can you describe the relationship between `getDerivedStateFromError` and `componentDidCatch` in terms of their lifecycle execution order?</summary>

- When an error occurs:
  1. React invokes `getDerivedStateFromError` first to update the state with error information.
  2. React then invokes `componentDidCatch` to perform side effects like logging.
- `getDerivedStateFromError` is static and synchronous, while `componentDidCatch` allows asynchronous side effects.

</details>  
</br>

<details>

<summary>7. How would you log errors captured in an error boundary to an external monitoring service? Provide a code example.</summary>

**Example**:

```jsx
import * as Sentry from "@sentry/react";

class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    Sentry.captureException(error, { extra: errorInfo }); // Log error to Sentry
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

</details>  
</br>

<details>

<summary>8. Discuss how to design error boundaries in an application with multiple layers (e.g., pages, components) to ensure user-friendly error handling and minimal disruption.</summary>

- Place **global error boundaries** at the root level to catch unhandled errors across the app.
- Add **localized error boundaries** for critical sections (e.g., feature modules, widgets) to isolate errors and allow unaffected parts to function.
- Provide user-friendly fallback UI tailored to the context of the error (e.g., reload buttons for a widget, navigation to a safe page).
- Use logging in all error boundaries to report errors for debugging.

**Example Structure**:

```jsx
<RootErrorBoundary>
  <Header />
  <PageErrorBoundary>
    <PageContent />
  </PageErrorBoundary>
  <Footer />
</RootErrorBoundary>
```

</details>  
</br>

<details>

<summary>9. What would happen if an error boundary itself throws an error? How would you handle this scenario?</summary>

- If an error boundary throws an error, React cannot recover, and the app crashes.
- To handle this:
  - Add a **fallback root-level error boundary** that wraps the entire app.
  - Test and isolate error boundaries to ensure they handle errors robustly.

**Example**:

```jsx
const FallbackRoot = ({ error }) => (
  <div>
    <h1>Critical error occurred</h1>
    <p>{error.message}</p>
  </div>
);

class RootErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  render() {
    if (this.state.hasError) {
      return <FallbackRoot error={this.state.error} />;
    }
    return this.props.children;
  }
}
```

</details>  
</br>

<details>

<summary>10. Can you have nested error boundaries, and how do they behave if both a child and a parent boundary encounter an error?</summary>

- Yes, you can have nested error boundaries.
- If both a child and parent error boundary encounter errors, the child boundary will handle its own error first, allowing the parent to continue functioning.
- If the child boundary fails or does not exist, the parent error boundary will catch the error.

**Example**:

```jsx
<ParentErrorBoundary>
  <ChildErrorBoundary>
    <ProblematicComponent />
  </ChildErrorBoundary>
</ParentErrorBoundary>
```

</details>  
</br>

<details>

<summary>11. How can you customize fallback UI in an error boundary based on the type or context of the error? Provide a code example.</summary>

- You can customize the fallback UI by inspecting the error object or additional context passed in `componentDidCatch`.

**Example**:

```jsx
class CustomErrorBoundary extends React.Component {
  state = { hasError: false, errorType: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, errorType: error.type };
  }

  render() {
    if (this.state.hasError) {
      if (this.state.errorType === "NetworkError") {
        return <h1>Network error occurred. Please try again later.</h1>;
      }
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

</details>  
</br>

<details>

<summary>12. Write an error boundary that distinguishes between production and development environments for different logging and UI behaviors.</summary>

**Example**:

```jsx
class EnvironmentErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    if (process.env.NODE_ENV === "production") {
      // Send error details to monitoring service
      console.log("Logging error in production:", error, errorInfo);
    } else {
      // Log error details to console in development
      console.error("Error in development:", error, errorInfo);
    }
  }

  render() {
    if (this.state.hasError) {
      return <h1>An error occurred.</h1>;
    }
    return this.props.children;
  }
}
```

</details>  
</br>

<details>

<summary>13. Functional components do not have `componentDidCatch`. How do you handle errors in functional components effectively?</summary>

- Use an error boundary implemented in a parent component (class-based or library-based).
- Use libraries like `react-error-boundary` to integrate error handling with functional components.
- Catch errors in hooks or event handlers using `try-catch`.

</details>  
</br>

<details>

<summary>14. What is the role of React's `useErrorBoundary` hook (from libraries such as `react-error-boundary`) in error handling?</summary>

- `useErrorBoundary` is a custom hook provided by libraries like `react-error-boundary` to simplify error handling in functional components.
- It provides methods like `resetErrorBoundary` to reset the error state and allows rendering fallback UI in a declarative way.

**Example**:

```jsx
import { useErrorBoundary } from "react-error-boundary";

function ChildComponent() {
  const { showBoundary } = useErrorBoundary();

  return (
    <button onClick={() => showBoundary(new Error("Test error"))}>
      Trigger Error
    </button>
  );
}
```

</details>  
</br>

<details>

<summary>15. How can you use React Context and hooks to propagate error states across multiple functional components? Provide a detailed example.</summary>

- Create a React Context to store error states.
- Use a custom hook to provide error handling logic.

**Example**:

```jsx
const ErrorContext = React.createContext();

export function ErrorProvider({ children }) {
  const [error, setError] = React.useState(null);

  const throwError = (err) => setError(err);
  const resetError = () => setError(null);

  return (
    <ErrorContext.Provider value={{ error, throwError, resetError }}>
      {children}
    </ErrorContext.Provider>
  );
}

export function useError() {
  return React.useContext(ErrorContext);
}
```

</details>  
</br>

<details>

<summary>16. If functional components cannot define their own error boundaries, how can you use error boundaries in an application with only functional components?</summary>

- Use class-based error boundaries as wrapper components around functional components.
- Alternatively, use libraries like `react-error-boundary` to implement error boundaries declaratively.

</details>  
</br>

<details>

<summary>17. How would you handle errors thrown by a hook or during rendering in a functional component?</summary>

- Wrap the functional component in an error boundary to catch rendering errors.
- Use `try-catch` to handle errors within hooks or async operations.

**Example in a hook**:

```jsx
function useSafeHook() {
  try {
    // Hook logic
  } catch (error) {
    console.error(error);
  }
}
```

</details>  
</br>

<details>

<summary>18. How do functional components integrate error handling with React’s `Suspense` component? What are the challenges when fetching data with `React.lazy`?</summary>

- **Integration**: Use `Suspense` to handle lazy loading of components. Wrap `Suspense` in an error boundary to catch errors.
- **Challenges**: If data fetching fails, you must combine error boundaries with fallback UI for graceful degradation.

</details>  
</br>

<details>

<summary>19. Write a custom hook to manage and log errors within a functional component. How would you ensure that it integrates seamlessly with error boundaries?</summary>

**Example**:

```jsx
function useErrorLogger() {
  const [error, setError] = React.useState(null);

  const logError = (err) => {
    console.error("Logging error:", err);
    setError(err);
  };

  return { error, logError };
}
```

</details>  
</br>

<details>

<summary>20. How can custom hooks introduce new challenges in error handling compared to class-based components?</summary>

- Hooks rely on the parent component or external boundaries for error handling.
- If a hook throws an error during rendering, it crashes the entire React tree unless an error boundary is present.

</details>  
</br>

<details>

<summary>21. How would you implement retry logic in a functional component for an operation that fails (e.g., a failed API request)?</summary>

- Use `useState` to track retries and `useEffect` to trigger retries.

**Example**:

```jsx
function useFetchWithRetry(url) {
  const [retries, setRetries] = React.useState(0);
  const [data, setData] = React.useState(null);

  React.useEffect(() => {
    fetch(url)
      .then((res) => res.json())
      .then(setData)
      .catch(() => setRetries((r) => r + 1));
  }, [url, retries]);

  return { data, retry: () => setRetries((r) => r + 1) };
}
```

</details>  
</br>

<details>

<summary>22. Describe how you would reset the error state in a functional component to allow users to retry after encountering an error.</summary>

- Use `useState` to manage error state and reset it on user action.

**Example**:

```jsx
function ErrorHandlingComponent() {
  const [error, setError] = React.useState(null);

  const handleRetry = () => setError(null);

  if (error) {
    return <button onClick={handleRetry}>Retry</button>;
  }

  return <div>Component Content</div>;
}
```

</details>  
</br>

<details>

<summary>23. In a React application using both class-based and functional components, describe a strategy for handling errors globally, locally, and within asynchronous code.</summary>

- Use a global error boundary at the root for unhandled errors.
- Add localized boundaries for critical sections.
- Handle async errors with `try-catch` and custom error handling hooks.

</details>  
</br>

<details>

<summary>24. How would you handle errors in a mixed-component tree where some parts use class components (with error boundaries) and others use functional components?</summary>

- Wrap both types of components with class-based error boundaries or use library-based solutions like `react-error-boundary` to create reusable boundaries.

</details>  
</br>

<details>

<summary>25. Implement a solution where errors thrown in deeply nested functional components are caught and displayed gracefully to the user without disrupting the entire app.</summary>

- Wrap the entire component tree or critical sections with an error boundary and use context to manage error propagation.

</details>  
</br>

<details>

<summary>26. Design a reusable error handling architecture that combines error boundaries and context for a React app.</summary>

- Use React Context to store and propagate error states.
- Combine error boundaries to isolate rendering errors from async error propagation via Context.

</details>  
</br>

<details>

<summary>27. Integrate error boundaries and error handling hooks with a third-party service like Sentry, and explain how to capture additional context such as user actions or Redux state when an error occurs.</summary>

- Use `componentDidCatch` to log error details to Sentry.
- Capture Redux state or user actions as additional context using Sentry’s API.

</details>  
</br>

<details>

<summary>28. What are the potential performance impacts of wrapping every functional component in an error boundary? How would you optimize error boundary placement?</summary>

- Wrapping every component in an error boundary may increase rendering overhead and memory usage.
- Optimize by placing boundaries at logical boundaries (e.g., pages, widgets) instead of every component.

</details>  
</br>

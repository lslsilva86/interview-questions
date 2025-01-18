<details>
<summary>1. Explain the differences between `if` statements, ternary operators, and logical `&&` in the context of conditional rendering in React. When would you use each?</summary>

- **`if` statements**: Used outside JSX to conditionally execute blocks of code. It's helpful for more complex logic where multiple conditions need to be checked or actions performed before rendering.
  ```jsx
  if (isLoggedIn) {
    return <Dashboard />;
  }
  return <Login />;
  ```
- **Ternary operators**: Inline conditional logic. It's ideal for concise, single-condition rendering where you have two possible outcomes.
  ```jsx
  return isLoggedIn ? <Dashboard /> : <Login />;
  ```
- **Logical `&&`**: Renders content only when the condition is truthy. Ideal for rendering something or nothing without an alternative.
  ```jsx
  return isLoggedIn && <Dashboard />;
  ```
  </details>

</br>

<details>
<summary>2. What are some common pitfalls of using `&&` for conditional rendering? Provide an example where using `&&` might lead to an unexpected UI behavior.</summary>

- **Pitfall**: `&&` evaluates falsy values like `0`, `null`, or `undefined`. This can lead to unintended rendering of `0` or no rendering at all.
  Example:
  ```jsx
  const count = 0;
  return <div>{count && <p>Count: {count}</p>}</div>;
  ```
  - Expected: Nothing (since `count` is 0).
  - Actual: `0` is rendered because `count` is falsy but still evaluated.
  </details>

</br>

<details>
<summary>3. How does React handle the falsy values `0`, `null`, `undefined`, and `false` in conditional rendering? Provide examples of how they behave when used with `&&`.</summary>

- **Falsy Values Behavior**:
  - `false`, `null`, and `undefined`: React ignores and doesn’t render them.
  - `0`: React renders `0`.

Examples:

```jsx
return (
  <div>
    {false && <p>This will not render</p>} // Nothing renders
    {null && <p>This will not render</p>} // Nothing renders
    {undefined && <p>This will not render</p>} // Nothing renders
    {0 && <p>This will not render</p>} // Outputs: 0
  </div>
);
```

</details>

</br>

<details>
<summary>4. Given the following code, what will be rendered, and why?</summary>

```jsx
const count = 0;
return <div>{count && <span>Count is non-zero</span>}</div>;
```

- **Output**: `0`
- **Why**: Since `count` is `0` (a falsy value), the right-hand side of `&&` (`<span>Count is non-zero</span>`) is not evaluated. However, `0` itself is rendered because React doesn’t ignore it.
</details>

</br>

<details>
<summary>5. Refactor the following `if`-based rendering into a single JSX expression using a ternary operator:</summary>

```jsx
if (isLoggedIn) {
  return <Dashboard />;
} else {
  return <Login />;
}
```

**Refactored Code**:

```jsx
return isLoggedIn ? <Dashboard /> : <Login />;
```

</details>

</br>

<details>
<summary>6. Consider the following code:</summary>

```jsx
const showMessage = false;
const message = "Hello, World!";
return <div>{showMessage && message}</div>;
```

- **Output**: Nothing renders.
- **Why**: `showMessage` is `false`, so the `&&` operator short-circuits and does not evaluate `message`. React ignores `false` in the output.

**Behavior with `null` or `undefined`:**

- If `showMessage` is `null` or `undefined`, the result will also be nothing rendered, as React ignores these falsy values.
</details>

</br>

<details>
<summary>7. What’s the output of this component, and why?</summary>

```jsx
const value = null;
return (
  <div>
    {value && <p>Value is not null</p>}
    {value ? <p>Truthy value</p> : <p>Falsy value</p>}
  </div>
);
```

- **Output**:
  ```html
  <div>
    <p>Falsy value</p>
  </div>
  ```
- **Why**:
  - `value && <p>Value is not null</p>`: `value` is `null` (falsy), so the `<p>` is not rendered.
  - `value ? <p>Truthy value</p> : <p>Falsy value</p>`: Since `value` is falsy, the ternary operator evaluates the `false` branch (`<p>Falsy value</p>`).
  </details>

</br>

<details>
<summary>8. Transform the following JSX code into equivalent `if` statements for rendering:</summary>

```jsx
const showContent = true;
return (
  <div>{showContent ? <p>Content is shown</p> : <p>Content is hidden</p>}</div>
);
```

**Equivalent Code**:

```jsx
if (showContent) {
  return (
    <div>
      <p>Content is shown</p>
    </div>
  );
} else {
  return (
    <div>
      <p>Content is hidden</p>
    </div>
  );
}
```

</details>

</br>

<details>
<summary>9. How would you handle multiple conditions for rendering different components, such as a dashboard for admin users, a basic interface for regular users, and a login page for unauthenticated users? Demonstrate this using:</summary>

**Using `if` statements:**

```jsx
if (!isAuthenticated) {
  return <Login />;
} else if (userRole === "admin") {
  return <AdminDashboard />;
} else {
  return <UserDashboard />;
}
```

**Using ternary operators:**

```jsx
return !isAuthenticated ? (
  <Login />
) : userRole === "admin" ? (
  <AdminDashboard />
) : (
  <UserDashboard />
);
```

**Using logical `&&`:**

```jsx
return (
  <>
    {!isAuthenticated && <Login />}
    {isAuthenticated && userRole === "admin" && <AdminDashboard />}
    {isAuthenticated && userRole !== "admin" && <UserDashboard />}
  </>
);
```

</details>

</br>

<details>
<summary>10. Write a function-based React component where the rendering logic for a list of items changes based on the following conditions:</summary>

- If the list is empty, display "No items available."
- If the list has 1-5 items, display the items in a `ul`.
- If the list has more than 5 items, display the count of items and a button to "View All".

**Implementation:**

```jsx
const ItemList = ({ items }) => {
  return (
    <div>
      {items.length === 0 && <p>No items available.</p>}
      {items.length > 0 && items.length <= 5 && (
        <ul>
          {items.map((item, index) => (
            <li key={index}>{item}</li>
          ))}
        </ul>
      )}
      {items.length > 5 && (
        <div>
          <p>{`There are ${items.length} items.`}</p>
          <button>View All</button>
        </div>
      )}
    </div>
  );
};
```

</details>

</br>

<details>
<summary>11. You’re given the following code that causes an error. Identify the issue and fix it:</summary>

```jsx
const isVisible = true;
return <div>{isVisible && "Message: " + message}</div>;
```

**Issue**: The `message` variable is not defined, which causes a `ReferenceError`.

**Fix**: Ensure `message` is defined before using it:

```jsx
const isVisible = true;
const message = "Hello, World!";
return <div>{isVisible && "Message: " + message}</div>;
```

</details>

</br>

<details>
<summary>12. A component renders unexpectedly blank when passed certain props. How would you debug issues related to conditional rendering logic using the following?</summary>

**Using `console.log`:**

- Log the values of the props and check if they are as expected.
- Example:
  ```jsx
  console.log("isVisible:", isVisible);
  console.log("message:", message);
  ```

**Using React Developer Tools:**

- Inspect the component's props and state in the React Developer Tools.
- Check if the condition for rendering is being met.

**Using unit tests for rendering:**

- Write tests to assert the output for specific prop values.
- Example:
  ```jsx
  it("renders message when isVisible is true", () => {
    render(<MyComponent isVisible={true} />);
    expect(screen.getByText("Message")).toBeInTheDocument();
  });
  ```
  </details>

</br>

<details>
<summary>13. Given the following conditional rendering using `&&`, why is the `<p>` tag not visible when `isEnabled` is `false`? How can this be fixed?</summary>

```jsx
const isEnabled = false;
return <div>{isEnabled && <p>Enabled</p>}</div>;
```

**Issue**:

- When `isEnabled` is `false`, the `&&` operator short-circuits and prevents rendering of `<p>Enabled</p>`.

**Fix**:

- Use a ternary operator to explicitly handle both conditions:
  ```jsx
  return <div>{isEnabled ? <p>Enabled</p> : null}</div>;
  ```
- Or, use a default fallback for falsy values:
  ```jsx
  return <div>{(isEnabled && <p>Enabled</p>) || <p>Disabled</p>}</div>;
  ```
  </details>

</br>

<details>
<summary>14. In a dashboard, you need to display:</summary>

- A loading spinner if `isLoading` is `true`.
- An error message if `hasError` is `true`.
- The main content otherwise.

**Implementation using nested ternary operators:**

```jsx
return (
  <div>
    {isLoading ? (
      <Spinner />
    ) : hasError ? (
      <p>Error loading content.</p>
    ) : (
      <Dashboard />
    )}
  </div>
);
```

**Discussion on readability trade-offs:**

- While ternary operators keep the code concise, nested ternaries can reduce readability for complex conditions.
- Alternative: Use `if` statements or helper functions to handle conditions before rendering JSX.
</details>

</br>

<details>
<summary>15. How would you implement conditional rendering for a large component tree (e.g., multiple nested children) while ensuring the code remains readable and maintainable?</summary>

**Approach 1: Using helper functions:**

```jsx
const renderContent = () => {
  if (isLoading) return <Spinner />;
  if (hasError) return <Error />;
  return <MainContent />;
};

return <div>{renderContent()}</div>;
```

**Approach 2: Using higher-order components (HOCs):**

```jsx
const withLoadingAndError =
  (Component) =>
  ({ isLoading, hasError, ...props }) => {
    if (isLoading) return <Spinner />;
    if (hasError) return <Error />;
    return <Component {...props} />;
  };

const EnhancedComponent = withLoadingAndError(MainContent);
return <EnhancedComponent isLoading={isLoading} hasError={hasError} />;
```

**Approach 3: Using conditional fragments:**

```jsx
return (
  <div>
    {isLoading && <Spinner />}
    {hasError && !isLoading && <Error />}
    {!isLoading && !hasError && <MainContent />}
  </div>
);
```

</details>

</br>

<details>
<summary>1. Explain the difference between `<BrowserRouter>` and `<HashRouter>`. When would you choose one over the other?</summary>

- `<BrowserRouter>` uses the **HTML5 History API** to manage URLs, which means the URLs are clean and look like standard web URLs (e.g., `https://example.com/about`).
- `<HashRouter>` uses a hash symbol (`#`) in the URL to manage routing (e.g., `https://example.com/#/about`). It does not rely on the server for URL changes.

**When to use:**

- Use `<BrowserRouter>` when you have control over the server and can configure it to serve your application for all routes.
- Use `<HashRouter>` for environments where you cannot control the server behavior (e.g., GitHub Pages) because the hash portion is not sent to the server.

</details>  
</br>

<details>
<summary>2. How does React Router determine which route to render when multiple routes match the URL?</summary>

React Router matches routes using a "best match wins" strategy:

- It evaluates all routes and picks the one with the most specific match.
- A match’s specificity is based on how closely the route’s path aligns with the URL.
- In React Router v6, exact matching is the default, so only paths that fully match are considered.

For example:

```jsx
<Route path="/" element={<Home />} />
<Route path="/about" element={<About />} />
```

- URL `/about` matches `/about` because it’s more specific than `/`.

</details>  
</br>

<details>
<summary>3. What is the purpose of the `exact` prop in earlier versions of React Router? Why was it removed in React Router v6?</summary>

- In earlier versions (v4/v5), `exact` was used to ensure that a route matched the path **exactly** without considering nested paths. Without it, routes like `/` would also match `/about`.

Example:

```jsx
<Route path="/" component={Home} />
<Route path="/about" component={About} />
```

- Visiting `/about` would match both routes unless `exact` was used on `/`.

- In React Router v6, `exact` was removed because exact matching became the default behavior. Routes now only match if the path aligns completely with the URL.

</details>  
</br>

<details>
<summary>4. How does React Router handle query parameters? How can you extract them in a component?</summary>

React Router itself doesn’t parse query parameters. You can use the `useSearchParams` hook (v6+) to manage query parameters easily.

Example:

```jsx
import { useSearchParams } from "react-router-dom";

function MyComponent() {
  const [searchParams, setSearchParams] = useSearchParams();
  const value = searchParams.get("key"); // Get the value of 'key'

  return <div>Query Parameter Value: {value}</div>;
}
```

Alternatively, you can use the `URLSearchParams` API in older versions:

```jsx
const params = new URLSearchParams(location.search);
const value = params.get("key");
```

</details>  
</br>

<details>
<summary>5. What are route guards in React Router? How would you implement them to protect certain routes?</summary>

Route guards restrict access to certain routes based on conditions like user authentication. They are implemented by wrapping the protected routes in a higher-order component or custom logic.

Example:

```jsx
import { Navigate } from 'react-router-dom';

function PrivateRoute({ children }) {
  const isAuthenticated = // Check authentication status
  return isAuthenticated ? children : <Navigate to="/login" />;
}

// Usage
<Route path="/dashboard" element={<PrivateRoute><Dashboard /></PrivateRoute>} />
```

</details>  
</br>

<details>
<summary>6. What are the key differences between `<Link>` and `<NavLink>`? When should you use one over the other?</summary>

- `<Link>`: Used to navigate to a specific route without a page reload. It provides a basic navigation experience.
- `<NavLink>`: Extends `<Link>` by adding styling capabilities for active links. It allows you to apply a specific CSS class to the active link.

Example:

```jsx
<NavLink to="/about" activeClassName="active">
  About
</NavLink>
```

Use `<Link>` for basic navigation and `<NavLink>` when you need to style active links (e.g., for a navigation bar).

</details>  
</br>

<details>
<summary>7. How does React Router handle route changes efficiently compared to traditional full-page reloads?</summary>

React Router uses client-side routing, which means:

- Only the components necessary for the new route are re-rendered, rather than reloading the entire page.
- It leverages the virtual DOM to efficiently update only the parts of the UI that need to change.
- It avoids unnecessary network requests by loading components dynamically when needed (e.g., with lazy loading).

Traditional full-page reloads involve fetching a new HTML document from the server, which is slower and disrupts the user experience by reinitializing the page.

</details>  
</br>

<details>
<summary>8. How do nested routes work in React Router, and why would you use them? Can you provide an example of setting up and using nested routes?</summary>

Nested routes allow you to define child routes inside parent routes, enabling components to render within a shared layout. This is useful for creating layouts like dashboards or shared navigation.

Example:

```jsx
import { Outlet, Link } from "react-router-dom";

function App() {
  return (
    <div>
      <nav>
        <Link to="/dashboard">Dashboard</Link>
        <Link to="/dashboard/reports">Reports</Link>
      </nav>
      <Outlet /> {/* Render nested route components here */}
    </div>
  );
}

function Dashboard() {
  return <h1>Dashboard</h1>;
}

function Reports() {
  return <h1>Reports</h1>;
}

// Routes setup
<Route path="/dashboard" element={<App />}>
  <Route index element={<Dashboard />} />
  <Route path="reports" element={<Reports />} />
</Route>;
```

</details>  
</br>

<details>
<summary>9. Explain the difference between relative and absolute paths in nested routes. Why is this distinction important?</summary>

- **Relative paths**: Defined relative to the parent route's path. They don't start with a `/`.
- **Absolute paths**: Start with a `/` and are evaluated from the root.

Example:

```jsx
<Route path="/dashboard" element={<Dashboard />}>
  <Route path="reports" element={<Reports />} /> {/* Relative path */}
  <Route path="/settings" element={<Settings />} /> {/* Absolute path */}
</Route>
```

**Importance:** Using a relative path ensures the route aligns correctly with the parent structure, while an absolute path may lead to unexpected behavior by overriding the hierarchy.

</details>  
</br>

<details>
<summary>10. In a scenario where you have deeply nested routes, how would you dynamically render child components based on the route parameters?</summary>

You can use `useParams` to extract route parameters and dynamically render components based on them.

Example:

```jsx
import { useParams } from "react-router-dom";

function ProductDetails() {
  const { productId } = useParams();
  return <h1>Product ID: {productId}</h1>;
}

// Route setup
<Route path="/products/:productId" element={<ProductDetails />} />;
```

This approach lets you dynamically fetch and render data or components based on the route parameter.

</details>  
</br>

<details>
<summary>11. How would you manage a layout for a parent route that persists across all its child routes?</summary>

You can use the `<Outlet />` component in React Router to render child components within a shared layout.

Example:

```jsx
function Layout() {
  return (
    <div>
      <header>Header</header>
      <main>
        <Outlet /> {/* Renders child routes */}
      </main>
      <footer>Footer</footer>
    </div>
  );
}

// Routes setup
<Route path="/" element={<Layout />}>
  <Route path="dashboard" element={<Dashboard />} />
  <Route path="profile" element={<Profile />} />
</Route>;
```

</details>  
</br>

<details>
<summary>12. If a parent route and a nested child route both define an `element`, how does React Router decide what to render?</summary>

- React Router renders the parent route’s `element` first and then renders the child route’s `element` inside the parent’s `<Outlet />`.
- If no `<Outlet />` is defined in the parent, only the parent’s `element` will render.

</details>  
</br>

<details>
<summary>13. How would you implement lazy loading for deeply nested routes in React Router? What challenges might you face?</summary>

You can use React's `lazy` and `Suspense` to lazy-load route components.

Example:

```jsx
import { lazy, Suspense } from "react";
const Reports = lazy(() => import("./Reports"));

<Route
  path="reports"
  element={
    <Suspense fallback={<div>Loading...</div>}>
      <Reports />
    </Suspense>
  }
/>;
```

**Challenges:**

- Proper error boundaries are required to handle failures in loading.
- Managing loading states for nested routes can be complex.
- Initial load times may increase slightly due to lazy-loading.

</details>  
</br>

<details>
<summary>14. How can you handle breadcrumb navigation for nested routes dynamically?</summary>

You can use the `useLocation` and `useMatches` hooks to generate breadcrumbs based on the current route.

Example:

```jsx
import { useLocation } from "react-router-dom";

function Breadcrumbs() {
  const location = useLocation();
  const paths = location.pathname.split("/").filter(Boolean);

  return (
    <nav>
      {paths.map((path, index) => (
        <span key={index}>
          {path} {index < paths.length - 1 && " / "}
        </span>
      ))}
    </nav>
  );
}
```

</details>  
</br>

<details>
<summary>15. What is programmatic navigation in React Router, and how does it differ from using `<Link>` or `<NavLink>`?</summary>

Programmatic navigation uses the `useNavigate` hook or `history` API to navigate to routes in code instead of relying on user interaction with `<Link>` or `<NavLink>`.

Example:

```jsx
import { useNavigate } from "react-router-dom";

function GoHomeButton() {
  const navigate = useNavigate();
  return <button onClick={() => navigate("/")}>Go Home</button>;
}
```

</details>  
</br>

<details>
<summary>16. How would you navigate programmatically using `useNavigate` in React Router v6? Provide an example.</summary>

Use the `useNavigate` hook to programmatically navigate to a route:

Example:

```jsx
import { useNavigate } from "react-router-dom";

function Login() {
  const navigate = useNavigate();
  const handleLogin = () => {
    // Perform login logic
    navigate("/dashboard"); // Navigate after login
  };

  return <button onClick={handleLogin}>Login</button>;
}
```

</details>  
</br>

<details>
<summary>17. What are the potential pitfalls of using `window.location` for navigation instead of React Router's programmatic methods?</summary>

Pitfalls include:

- Full-page reloads, leading to loss of state and slower performance.
- Breaking single-page application (SPA) functionality.
- Reduced flexibility to manage navigation-related logic (e.g., passing state).

</details>  
</br>

<details>
<summary>18. How can you pass state or data when navigating programmatically? Provide an example and discuss when you might use this.</summary>

You can pass state using the `state` property of `useNavigate`.

Example:

```jsx
navigate("/profile", { state: { userId: 123 } });

// Access the state in the target component
const location = useLocation();
const { userId } = location.state;
```

Use cases include passing data between routes without exposing it in the URL.

</details>  
</br>

<details>
<summary>19. Explain how you would handle a situation where programmatic navigation is required after a certain asynchronous operation completes.</summary>

You can navigate after an async operation using `useNavigate`.

Example:

```jsx
function SaveButton() {
  const navigate = useNavigate();

  const handleSave = async () => {
    await saveData();
    navigate("/success");
  };

  return <button onClick={handleSave}>Save</button>;
}
```

</details>  
</br>

<details>
<summary>20. How would you handle redirects based on user authentication state in a React Router application programmatically?</summary>

Use conditional logic to navigate based on authentication status.

Example:

```jsx
const navigate = useNavigate();
if (!isAuthenticated) {
  navigate("/login");
}
```

You can also use `PrivateRoute` components for this purpose.

</details>  
</br>

<details>
<summary>21. What issues can arise when programmatically navigating within a single-page application using React Router? How can these be mitigated?</summary>

**Issues:**

- Incorrect route handling if not used with React Router APIs.
- Loss of scroll position on navigation.
- Missing context if navigation skips important setup logic.

**Mitigation:**

- Always use React Router’s `useNavigate` or `<Link>` for navigation.
- Use `scrollRestoration` or handle scroll manually.

</details>  
</br>

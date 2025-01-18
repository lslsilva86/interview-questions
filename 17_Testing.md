<details>

<summary>1. How do you test components that rely on external libraries like `react-router-dom` or `redux`?</summary>

Use testing utilities provided by the libraries:

- For `react-router-dom`, wrap the component with a `MemoryRouter` and provide a route using the `initialEntries` prop.

  ```javascript
  import { render } from "@testing-library/react";
  import { MemoryRouter } from "react-router-dom";

  render(
    <MemoryRouter initialEntries={["/route"]}>
      <YourComponent />
    </MemoryRouter>
  );
  ```

- For `redux`, wrap the component with a `Provider` and pass a mock store.

  ```javascript
  import { Provider } from "react-redux";
  import { render } from "@testing-library/react";
  import { createStore } from "redux";

  const mockStore = createStore(() => ({
    /* mock state */
  }));
  render(
    <Provider store={mockStore}>
      <YourComponent />
    </Provider>
  );
  ```

  </details>

</br>

<details>

<summary>2. What is the difference between unit testing, integration testing, and end-to-end testing? How do you decide which type of test to write?</summary>

- **Unit Testing**: Tests individual components or functions in isolation. Best for testing specific logic or a component’s behavior.
- **Integration Testing**: Tests how components work together (e.g., component interaction with API calls or context). Useful for ensuring combined functionality.
- **End-to-End Testing**: Simulates user interactions and tests the entire application flow. Useful for critical workflows.

**Decision**:

- Write **unit tests** for reusable components or isolated logic.
- Write **integration tests** for features with interdependent components.
- Write **E2E tests** for user-critical flows (e.g., login, checkout).

</details>

</br>

<details>

<summary>3. How would you test a component that consumes a context provided by React’s `useContext`?</summary>

Wrap the component with a custom context provider in the test.

```javascript
import { render } from "@testing-library/react";
import { MyContext } from "../MyContext";

const mockContextValue = { key: "value" };
render(
  <MyContext.Provider value={mockContextValue}>
    <YourComponent />
  </MyContext.Provider>
);
```

Test the behavior based on the mocked context value.

</details>

</br>

<details>

<summary>4. If a component is wrapped in multiple providers (e.g., ThemeProvider, ReduxProvider), how do you isolate it for testing?</summary>

Create a custom render function that wraps the component with all necessary providers.

```javascript
import { render } from "@testing-library/react";
import { ThemeProvider } from "styled-components";
import { Provider } from "react-redux";

const customRender = (ui, { store, theme, ...options }) => {
  return render(
    <Provider store={store}>
      <ThemeProvider theme={theme}>{ui}</ThemeProvider>
    </Provider>,
    options
  );
};
```

</details>

</br>

<details>

<summary>5. How do you test user interactions, such as form submissions or button clicks, that trigger state changes?</summary>

Use `fireEvent` or `userEvent` to simulate user interactions and then assert the expected outcome.

```javascript
import { render, fireEvent } from "@testing-library/react";

const { getByLabelText, getByText } = render(<YourComponent />);
fireEvent.change(getByLabelText(/input label/i), { target: { value: "test" } });
fireEvent.click(getByText(/submit/i));

expect(/* assertion */).toBe(/* expected value */);
```

</details>

</br>

<details>

<summary>6. A dropdown component conditionally renders its items based on the state. How would you test if the correct items are rendered after interacting with the dropdown?</summary>

Simulate the dropdown interaction and assert the rendered items.

```javascript
import { render, fireEvent } from "@testing-library/react";

const { getByTestId, queryByText } = render(<Dropdown />);
fireEvent.click(getByTestId("dropdown-button"));

expect(queryByText(/expected-item/i)).toBeInTheDocument();
```

</details>

</br>

<details>

<summary>7. How would you ensure a component performs optimally under testing conditions?</summary>

- Use `React.Profiler` to measure rendering time.
- Monitor unnecessary re-renders using tools like `why-did-you-render`.
- Test for unnecessary renders:

  ```javascript
  import { render } from "@testing-library/react";

  const mockFunction = jest.fn();
  const { rerender } = render(<Component onRender={mockFunction} />);
  rerender(<Component onRender={mockFunction} />);

  expect(mockFunction).toHaveBeenCalledTimes(1); // Assert no unnecessary re-renders
  ```

</details>

</br>

<details>

<summary>8. How do you test that a component properly implements `React.memo` or `useMemo`?</summary>

Wrap the component in a test case where props/state do not change and assert that it doesn’t re-render unnecessarily.

```javascript
import { render } from "@testing-library/react";
import React from "react";

const mockFunction = jest.fn();
const MemoizedComponent = React.memo(({ onRender }) => {
  onRender();
  return <div>Memoized</div>;
});

const { rerender } = render(<MemoizedComponent onRender={mockFunction} />);
rerender(<MemoizedComponent onRender={mockFunction} />);
expect(mockFunction).toHaveBeenCalledTimes(1);
```

</details>

</br>

<details>

<summary>9. How would you test components wrapped in React's `ErrorBoundary`?</summary>

Mock an error in a child component and assert that the `ErrorBoundary` renders fallback UI.

```javascript
const ErrorProne = () => {
  throw new Error("Test Error");
};

const { getByText } = render(
  <ErrorBoundary fallback={<div>Error Occurred</div>}>
    <ErrorProne />
  </ErrorBoundary>
);

expect(getByText(/error occurred/i)).toBeInTheDocument();
```

</details>

</br>

<details>

<summary>10. How do you test components that use `React.Suspense` for lazy-loaded components?</summary>

Use `act` to wrap tests and test the fallback behavior.

```javascript
import { render, act } from "@testing-library/react";
const LazyComponent = React.lazy(() => import("./LazyComponent"));

const { getByText } = render(
  <React.Suspense fallback={<div>Loading...</div>}>
    <LazyComponent />
  </React.Suspense>
);

expect(getByText(/loading/i)).toBeInTheDocument();
```

</details>

</br>

<details>

<summary>11. How would you test edge cases like empty arrays, null values, or undefined props in a component?</summary>

Write separate test cases for each edge case.  
Mock different props and assert the component’s behavior.

```javascript
const { queryByText } = render(<Component items={[]} />);
expect(queryByText(/no items found/i)).toBeInTheDocument();
```

</details>

</br>

<details>

<summary>12. How do you test a component for accessibility compliance (e.g., using `aria-*` attributes)?</summary>

Use `@testing-library/jest-dom` to assert the presence of `aria-*` attributes.  
You can also use libraries like `axe` or `jest-axe` to test for accessibility issues.

```javascript
import { render } from "@testing-library/react";
import { axe } from "jest-axe";

const { container } = render(<AccessibleComponent />);
const results = await axe(container);

expect(results).toHaveNoViolations();
```

</details>

</br>

<details>

<summary>13. What are the pros and cons of snapshot testing? How do you handle large snapshots that frequently change?</summary>

**Pros**:

- Easy to set up and maintain for static components.
- Quickly identifies UI changes.

**Cons**:

- Large snapshots can become difficult to manage.
- Over-reliance can lead to ignoring meaningful changes.

**Handling Large Snapshots**:

- Break down components into smaller parts and snapshot test only critical sections.
- Use custom matchers to avoid unnecessary rendering in snapshots.

</details>

</br>

<details>

<summary>14. How would you test a component that uses `setTimeout` or `setInterval`?</summary>

Use Jest's timer mocks to control and advance timers in tests.

```javascript
jest.useFakeTimers();

test("delayed action", () => {
  const { getByText } = render(<ComponentWithTimeout />);
  jest.advanceTimersByTime(3000); // Move the timer forward
  expect(getByText(/delayed text/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>15. A component updates its state after an API call with a debounce. How do you test it?</summary>

Mock the debounce and API call, then use Jest timers to simulate delays.

```javascript
jest.useFakeTimers();

test("debounced API call", () => {
  const mockApi = jest.fn();
  render(<ComponentWithDebounce apiCall={mockApi} />);
  fireEvent.change(screen.getByPlaceholderText(/search/i), {
    target: { value: "query" },
  });

  jest.advanceTimersByTime(300); // Advance debounce time
  expect(mockApi).toHaveBeenCalledWith("query");
});
```

</details>

</br>

<details>

<summary>16. What is the difference between `jest.mock`, `jest.spyOn`, and using libraries like `msw` (Mock Service Worker)? When would you use each?</summary>

- **`jest.mock`**: Mocks entire modules or dependencies. Use when you need full control over module behavior.
- **`jest.spyOn`**: Monitors specific methods without overriding the module. Use for partial mocking.
- **`msw`**: Simulates real API behavior in a controlled environment. Use for testing API interactions in integration or E2E tests.

</details>

</br>

<details>

<summary>17. How do you handle API mocks that include authentication tokens or headers?</summary>

- Use Jest to mock `fetch` or `axios`, and provide mock tokens in the headers.

```javascript
jest.mock("axios");
axios.get.mockResolvedValue({
  data: { key: "value" },
});

test("API call with headers", async () => {
  const token = "mock-token";
  await fetchData(token);
  expect(axios.get).toHaveBeenCalledWith("/endpoint", {
    headers: { Authorization: `Bearer ${token}` },
  });
});
```

</details>

</br>

<details>

<summary>18. How do you test a component's behavior when an API call fails with a specific error code (e.g., 404, 500)?</summary>

Mock the API to return the error response and test the error handling behavior.

```javascript
jest.mock("axios");
axios.get.mockRejectedValue({
  response: { status: 404 },
});

test("handles API error", async () => {
  render(<Component />);
  await waitFor(() =>
    expect(screen.getByText(/error message/i)).toBeInTheDocument()
  );
});
```

</details>

</br>

<details>

<summary>19. How would you test retry logic for a failed API call?</summary>

Mock the API to fail initially and succeed on subsequent calls.  
Verify the retry mechanism.

```javascript
let callCount = 0;
jest.mock("axios");
axios.get.mockImplementation(() => {
  callCount += 1;
  if (callCount === 1) {
    return Promise.reject(new Error("Network Error"));
  }
  return Promise.resolve({ data: { key: "value" } });
});

test("retry API call", async () => {
  render(<ComponentWithRetry />);
  expect(await screen.findByText(/value/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>20. How would you test a component that fetches data from multiple APIs simultaneously?</summary>

Mock all API calls and use `Promise.all`.

```javascript
jest.mock("axios");
axios.all.mockResolvedValue([
  { data: { api1: "value1" } },
  { data: { api2: "value2" } },
]);

test("fetches multiple APIs", async () => {
  render(<Component />);
  expect(await screen.findByText(/value1/i)).toBeInTheDocument();
  expect(await screen.findByText(/value2/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>21. A component performs an API call after the user stops typing for 300ms. How would you mock and test this scenario?</summary>

Use Jest timers to mock the debounce delay.

```javascript
jest.useFakeTimers();

test("debounced API call", async () => {
  const mockApi = jest.fn();
  render(<SearchComponent apiCall={mockApi} />);

  fireEvent.change(screen.getByPlaceholderText(/search/i), {
    target: { value: "query" },
  });

  jest.advanceTimersByTime(300); // Simulate debounce time
  expect(mockApi).toHaveBeenCalledWith("query");
});
```

</details>

</br>

<details>

<summary>22. How would you mock and test a dynamic API request whose URL depends on a user’s input?</summary>

Mock the API dynamically based on input values.

```javascript
jest.mock("axios");

test("dynamic API request", async () => {
  render(<Component />);
  fireEvent.change(screen.getByPlaceholderText(/enter id/i), {
    target: { value: "123" },
  });

  expect(axios.get).toHaveBeenCalledWith("/endpoint/123");
});
```

</details>

</br>

<details>

<summary>23. A component conditionally fetches data based on a flag from props. How do you test both conditions?</summary>

Render the component with different props and assert API behavior.

```javascript
jest.mock("axios");

test("fetch data conditionally", async () => {
  const { rerender } = render(<Component fetchFlag={true} />);
  expect(axios.get).toHaveBeenCalled();

  rerender(<Component fetchFlag={false} />);
  expect(axios.get).not.toHaveBeenCalled();
});
```

</details>

</br>

<details>

<summary>24. How do you test API calls within a Redux action or Redux Thunk?</summary>

Mock the API and use a mock store to test the dispatched actions.

```javascript
jest.mock("axios");

test("Redux Thunk API call", async () => {
  const mockStore = configureMockStore([thunk]);
  const store = mockStore();
  axios.get.mockResolvedValue({ data: { key: "value" } });

  await store.dispatch(fetchDataThunk());
  expect(store.getActions()).toContainEqual({
    type: "DATA_SUCCESS",
    payload: "value",
  });
});
```

</details>

</br>

<details>

<summary>25. What is your approach to testing a component that triggers API calls indirectly through a `useEffect`?</summary>

Mock the API and wait for the effect to complete.

```javascript
jest.mock("axios");

test("useEffect API call", async () => {
  axios.get.mockResolvedValue({ data: { key: "value" } });
  render(<Component />);
  expect(await screen.findByText(/value/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>26. How would you test a component implementing infinite scroll that fetches more data as the user scrolls down?</summary>

Mock the API and simulate the scroll event.

```javascript
jest.mock("axios");

test("infinite scroll", async () => {
  render(<InfiniteScrollComponent />);
  fireEvent.scroll(window, { target: { scrollY: 1000 } });

  expect(await screen.findByText(/new data/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>27. A component paginates API results based on a page number. How would you mock and test the paginated API?</summary>

Mock the API and test for each page's data.

```javascript
jest.mock("axios");
axios.get.mockResolvedValueOnce({ data: { items: ["page1"] } });
axios.get.mockResolvedValueOnce({ data: { items: ["page2"] } });

test("paginated API", async () => {
  render(<PaginationComponent />);
  fireEvent.click(screen.getByText(/next/i));
  expect(await screen.findByText(/page2/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>28. How would you test a component that transforms API response data before rendering it?</summary>

Mock the API response and assert the transformed data.

```javascript
jest.mock("axios");

test("transformed data", async () => {
  axios.get.mockResolvedValue({ data: { rawData: 10 } });
  render(<Component />);
  expect(await screen.findByText(/transformed: 20/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>29. An API returns inconsistent data formats. How would you mock and test this scenario?</summary>

Mock multiple responses and test component behavior for each case.

```javascript
jest.mock("axios");
axios.get
  .mockResolvedValueOnce({ data: { format1: "value1" } })
  .mockResolvedValueOnce({ data: { format2: "value2" } });

test("inconsistent data", async () => {
  render(<Component />);
  expect(await screen.findByText(/value1/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>30. How would you test a component that interacts with third-party APIs like Google Maps or a payment gateway?</summary>

Mock the third-party library’s methods.

```javascript
jest.mock("google-maps");

test("Google Maps API", () => {
  render(<MapComponent />);
  expect(google.maps.Map).toHaveBeenCalled();
});
```

</details>

</br>

<details>

<summary>31. How do you handle rate-limiting scenarios when mocking third-party APIs?</summary>

Mock the API response with rate-limit errors and test retry or fallback logic.

```javascript
jest.mock("axios");
axios.get.mockRejectedValue({ response: { status: 429 } });

test("rate-limited API", async () => {
  render(<Component />);
  expect(await screen.findByText(/retry message/i)).toBeInTheDocument();
});
```

</details>

</br>

<details>

<summary>32. How do you debug a test that passes locally but fails in the CI/CD pipeline?</summary>

- Check for differences in environments (e.g., Node.js versions, dependencies).
- Use verbose logs in the CI pipeline to get more details.
- Add delays or mock network requests to isolate timing issues.

</details>

</br>

<details>

<summary>33. How do you handle race conditions in tests, especially when mocking asynchronous functions?</summary>

- Use `async/await` and `waitFor` to ensure proper sequencing.
- Mock functions to resolve in a controlled order to avoid conflicts.

```javascript
jest.mock("axios");
axios.get
  .mockResolvedValueOnce({ data: "first" })
  .mockResolvedValueOnce({ data: "second" });

test("race condition", async () => {
  render(<Component />);
  expect(await screen.findByText(/first/i)).toBeInTheDocument();
});
```

</details>

</br>

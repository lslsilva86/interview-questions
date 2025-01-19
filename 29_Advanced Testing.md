<details>
<summary>1. Cypress vs. Playwright</summary>

**Key Differences:**

- **Architecture:**
  - Cypress operates within the browser itself, providing native access to the DOM. Playwright operates outside the browser, controlling browsers using WebSocket or CDP protocols.
- **Cross-browser Testing:**
  - Playwright supports multiple browsers (Chromium, WebKit, Firefox) out of the box. Cypress only supports Chromium-based browsers (with limited support for WebKit since v10).
- **Parallelization:**
  - Playwright supports native parallelization across processes. Cypress needs a CI setup or paid Dashboard Service for parallelization.
- **Network Interception:**
  - Playwright has a robust API for request/response interception. Cypress has limited support for intercepting and modifying requests.
- **API Testing:**
  - Playwright includes native tools for API testing. Cypress requires plugins or custom configurations.

**When to Choose:**

- Use Cypress when debugging, ease of setup, and a rich browser-focused testing experience is needed.
- Use Playwright for cross-browser testing, handling complex web apps, and scenarios requiring flexible test orchestration.

</details>

</br>

<details>
<summary>2. E2E Testing Challenges</summary>

To ensure stability and resilience:

- **Avoid Using Flaky Selectors:** Prefer unique `data-testid` attributes or semantic selectors over class names or IDs.
- **Use Retry Logic:** Both tools support retrying failed assertions automatically. Enable retries for unstable tests.
- **Mock/Intercept Network Calls:** Avoid reliance on live API endpoints by intercepting requests with mock data.
- **Control Test Environment:** Use test environments with static data to minimize unpredictability.
- **Wait for Stable States:** Use assertions to ensure the UI is ready before interacting with it (e.g., waiting for loaders to disappear).

</details>

</br>

<details>
<summary>3. Test Isolation</summary>

**Handling Test Isolation:**

- **Cypress:**
  - Use `cy.visit()` to start each test with a clean slate.
  - Clear cookies, local storage, and session storage (`cy.clearCookies()` and `cy.clearLocalStorage()`).
  - Stub network requests to control data between tests.
- **Playwright:**
  - Launch a new browser context for each test using `browser.newContext()` to ensure isolated states.
  - Use `context.clearCookies()` or `context.storageState()` to reset sessions between tests.

</details>

</br>

<details>
<summary>4. Cross-Browser Testing</summary>

Playwright achieves cross-browser testing by:

- Supporting multiple rendering engines (Chromium, WebKit, and Firefox).
- Using browser-specific configurations, allowing tests to run seamlessly across different engines.

**Challenges:**

- **Rendering Variations:** Some elements might render differently across browsers (e.g., fonts, CSS styles).
- **Feature Parity:** Browser-specific quirks might require conditional test logic.
- **Performance Differences:** Test execution speeds may vary depending on the browser engine.

</details>

</br>

<details>
<summary>5. Flaky Tests</summary>

Strategies to identify and resolve flaky tests:

- **Analyze Failure Patterns:** Review CI logs to identify recurring issues (e.g., timing, environment instability).
- **Add Smart Waits:** Replace arbitrary `wait()` with condition-based waits (`cy.waitUntil()`, `page.waitForSelector()`).
- **Reduce Dependencies:** Use mocks to decouple tests from external APIs or databases.
- **Run Tests in Isolation:** Identify interdependent tests by running tests individually and in parallel.
- **CI Debugging Tools:** Use video or screenshot capture (supported in both Cypress and Playwright) to analyze test failures.

</details>

</br>

<details>
<summary>6. Test Authentication Flows</summary>

**Playwright (OAuth):**

```javascript
test("OAuth login", async ({ page }) => {
  await page.goto("https://example.com/login");
  await page.fill("#username", "testuser");
  await page.fill("#password", "password123");
  await page.click('button[type="submit"]');
  await page.waitForURL("https://example.com/dashboard"); // Validate redirection
  expect(page.url()).toBe("https://example.com/dashboard");
});
```

**Cypress (Session Persistence):**

```javascript
Cypress.Commands.add("login", () => {
  cy.request("POST", "/api/login", {
    username: "testuser",
    password: "password123",
  }).then((response) => {
    window.localStorage.setItem("token", response.body.token);
  });
});

describe("Login test", () => {
  before(() => cy.login());
  it("Visits dashboard after login", () => {
    cy.visit("/dashboard");
    cy.contains("Welcome, testuser");
  });
});
```

</details>

</br>

<details>
<summary>7. Dynamic Data Handling</summary>

**Playwright Example:**

```javascript
test("Interact with dynamic dropdown", async ({ page }) => {
  await page.goto("https://example.com");
  await page.click("#dropdown"); // Open dropdown
  await page.waitForSelector(".dropdown-item"); // Wait for items to load
  const options = await page.$$eval(".dropdown-item", (items) =>
    items.map((item) => item.textContent)
  );
  console.log(options); // Verify dynamic data
  await page.click(`.dropdown-item:has-text("Option 1")`); // Select an option
});
```

</details>

</br>

<details>
<summary>8. Performance Testing with E2E Tools</summary>

**Measuring Page Load Time in Playwright:**

```javascript
test("Measure performance", async ({ page }) => {
  const start = Date.now();
  await page.goto("https://example.com");
  const loadTime = Date.now() - start;
  console.log(`Page load time: ${loadTime}ms`);
  expect(loadTime).toBeLessThan(2000); // Assert performance threshold
});
```

**Cypress Performance Plugin:** Use plugins like `cypress-performance` to capture metrics.

</details>

</br>

<details>
<summary>9. Testing Real-Time Features</summary>

**Cypress Real-Time Update Test:**

```javascript
describe("Real-time updates", () => {
  it("Validates live chat updates", () => {
    cy.visit("/chat");
    cy.intercept("POST", "/api/sendMessage").as("sendMessage");
    cy.get("#messageInput").type("Hello, World!");
    cy.get("#sendButton").click();
    cy.wait("@sendMessage");
    cy.contains(".message", "Hello, World!"); // Assert message appears
  });
});
```

</details>

</br>

<details>
<summary>10. Custom Commands</summary>

**Cypress Custom Command Example:**

```javascript
Cypress.Commands.add("fillAndValidateForm", (formData) => {
  cy.get("#name").type(formData.name);
  cy.get("#email").type(formData.email);
  cy.get("#password").type(formData.password);
  cy.get("#submit").click();
  cy.contains("Thank you for submitting!").should("exist");
});

// Usage in a test
describe("Form Submission Test", () => {
  it("fills and validates the form", () => {
    cy.visit("/form");
    cy.fillAndValidateForm({
      name: "John Doe",
      email: "john.doe@example.com",
      password: "password123",
    });
  });
});
```

**Playwright Alternative:**
In Playwright, you can achieve a similar effect by creating utility functions that can be reused across tests.

</details>

</br>

<details>
<summary>11. Debugging E2E Tests</summary>

**Debugging in Cypress:**

- Use `cy.pause()` to pause the test execution for manual debugging.
- Add `cy.debug()` to trigger interactive debugging.
- Use the Cypress Test Runner to visually trace each step of the test.

**Debugging in Playwright:**

- Use `page.pause()` to open the Playwright Inspector for debugging.
- Use the `--debug` CLI flag to launch tests with debugging enabled.
- Add `console.log()` statements in your tests to inspect variables and states.

**General Tips:**

- Review screenshots, videos, and logs generated during test failures.
- Reproduce failing tests locally using the same environment and configurations as CI.

</details>

</br>

<details>
<summary>12. Purpose of Snapshot Testing</summary>

**Purpose:**
Snapshot testing captures a "snapshot" of a component’s rendered output at a specific time. It ensures that changes to the UI are intentional and controlled.

**When to Avoid:**

- Dynamic content (e.g., timestamps, random IDs) can result in frequent snapshot updates.
- Components with high variability or low value in snapshot validation.

</details>

</br>

<details>
<summary>13. Best Practices</summary>

**Best Practices for Snapshot Testing:**

- Use `data-testid` or predictable selectors to avoid test failures due to DOM changes.
- Keep snapshots small and focused. Large snapshots are harder to review.
- Use snapshot testing as a complement to, not a replacement for, other tests (e.g., unit tests).
- Always review and verify snapshot changes in pull requests.

</details>

</br>

<details>
<summary>14. Snapshot Updates</summary>

**When to Update:**

- Update snapshots only after verifying that the changes are expected and do not break functionality.

**Risks of Blind Updates:**

- Masking unintended changes or bugs.
- Reducing test reliability and making it harder to catch regressions.

</details>

</br>

<details>
<summary>15. Integration with State Management</summary>

To test components reliant on external state (e.g., Redux):

- **Mock the State:** Use libraries like `redux-mock-store` or pass a mock Redux store as a prop.
- **Snapshot Examples:**

```javascript
import { Provider } from "react-redux";
import renderer from "react-test-renderer";

const mockStore = configureStore([]);
const store = mockStore({ key: "value" });

it("matches snapshot with Redux state", () => {
  const tree = renderer
    .create(
      <Provider store={store}>
        <MyComponent />
      </Provider>
    )
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```

</details>

</br>

<details>
<summary>16. Large Snapshots</summary>

**Handling Large Snapshots:**

- Break components into smaller subcomponents and test them individually.
- Use custom serializers to remove unnecessary data (e.g., timestamps or dynamic IDs).
- Focus on critical elements instead of snapshotting the entire component.

</details>

</br>

<details>
<summary>17. Dynamic Content Testing</summary>

**Ensuring Stable Snapshots with Dynamic Data:**

- Use mocking libraries (e.g., `jest.mock()`) to mock data and remove randomness.
- Use custom serializers to exclude fields like timestamps or random IDs.

```javascript
expect.addSnapshotSerializer({
  test: (val) => typeof val === "string" && /\d{4}-\d{2}-\d{2}/.test(val), // Check for dates
  print: () => "<<DATE>>",
});
```

</details>

</br>

<details>
<summary>18. Snapshot Testing with Styled Components</summary>

**Styled-Components Example:**

```javascript
import renderer from "react-test-renderer";
import { ThemeProvider } from "styled-components";
import MyComponent from "./MyComponent";

const theme = { primary: "blue", secondary: "red" };

it("matches snapshot with theming", () => {
  const tree = renderer
    .create(
      <ThemeProvider theme={theme}>
        <MyComponent />
      </ThemeProvider>
    )
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```

</details>

</br>

<details>
<summary>19. Mocking API Data</summary>

**Snapshot Test with Mocked Data:**

```javascript
jest.mock("./api", () => ({
  fetchData: jest.fn(() => Promise.resolve(["Item 1", "Item 2"])),
}));

it("matches snapshot with mocked data", async () => {
  const component = renderer.create(<MyComponent />);
  await act(async () => component.update());
  expect(component.toJSON()).toMatchSnapshot();
});
```

</details>

</br>

<details>
<summary>20. Custom Serializers</summary>

**Creating a Jest Custom Serializer:**

```javascript
expect.addSnapshotSerializer({
  test: (val) => typeof val === "object" && val.dynamicField,
  print: (val) => `{ id: ${val.id}, otherFields: ... }`,
});
```

</details>

</br>

<details>
<summary>21. Testing with Props</summary>

**Snapshot Testing with Varying Props:**

```javascript
it("matches snapshot with different props", () => {
  const component = renderer.create(<MyComponent title="Title 1" />);
  expect(component.toJSON()).toMatchSnapshot();

  component.update(<MyComponent title="Title 2" />);
  expect(component.toJSON()).toMatchSnapshot();
});
```

</details>

</br>

<details>
<summary>22. Snapshot and RTL Integration</summary>

**Combining Snapshot and RTL:**

```javascript
import { render } from "@testing-library/react";

it("matches snapshot with RTL", () => {
  const { container } = render(<MyComponent />);
  expect(container).toMatchSnapshot();
});
```

</details>

</br>

<details>
<summary>23. Test Strategy Design</summary>

**E2E and Snapshot Division:**

- Use snapshots for component-level validation and E2E for integration and user-flow tests.
- Example:
  - Snapshot Test: Verify visual rendering of a button.
  - E2E Test: Validate the button’s behavior in a workflow (e.g., login).

</details>

</br>

<details>
<summary>24. Performance Concerns</summary>

**Addressing Snapshot Bottlenecks:**

- Remove unnecessary snapshots or focus on smaller components.
- Parallelize tests using Jest’s `--maxWorkers` flag.
- Use selective test execution (`.only` or dynamic test filtering).

</details>

</br>

<details>
<summary>25. CI/CD Integration</summary>

**Steps for Integration:**

- Run snapshot tests during the CI build phase with strict failure checks.
- Run E2E tests in separate pipelines to optimize execution time.
- Use dashboards (e.g., Cypress Dashboard) to monitor test performance and results.

</details>

</br>

<details>
<summary>26. Debugging Failures</summary>

**Debugging Snapshot Test Failures:**

- Use Jest’s `--watch` mode to interactively debug failing snapshots.
- Compare snapshots visually in the PR review process.
- Re-run the test locally with mocked data to ensure consistency.

</details>

</br>

<details>
<summary>27. Test Scalability</summary>

**Ensuring Scalability:**

- Prioritize and group critical tests to reduce the execution burden.
- Use parallel test execution across CI/CD nodes.
- Implement a test tagging system to run specific suites when necessary.

</details>

</br>

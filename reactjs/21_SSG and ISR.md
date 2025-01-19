<details>

<summary>1. SSG vs. SSR vs. CSR</summary>

- **SSG (Static Site Generation)**: Pre-renders pages at **build time**. The output is static HTML files that are served to users. Great for SEO, fast performance, and sites with content that doesn’t change frequently.

  - **Trade-offs**: Cannot handle real-time updates; requires a rebuild for content changes unless using ISR.

- **SSR (Server-Side Rendering)**: Renders pages **on demand** at each request, providing the latest data. Useful for dynamic, user-specific content or frequently changing data.

  - **Trade-offs**: Slower than SSG due to server-side computation at runtime; higher infrastructure cost.

- **CSR (Client-Side Rendering)**: Renders content **entirely in the browser**, typically with JavaScript frameworks like React. Data is fetched and rendered after the initial page load.
  - **Trade-offs**: Worse SEO and slower initial page load compared to SSG and SSR.

</details>  
</br>

<details>

<summary>2. ISR Use Case</summary>

Use **ISR** when:

- The content changes infrequently but needs to reflect updates without a full rebuild.
- Example: A blog where posts are edited occasionally. You can set a `revalidate` interval of 10 minutes to regenerate the page in the background when a request is made after the interval.

</details>  
</br>

<details>

<summary>3. ISR Limitations</summary>

- **Limited to Serverless**: ISR relies on serverless functions or edge functions, limiting deployment to platforms that support this (e.g., Vercel, AWS Lambda).
- **Revalidation Delays**: Changes are not instant as revalidation occurs on the next request after the interval.
- **Concurrency Issues**: If multiple users request a regenerating page, they might receive stale content during revalidation.
- **Error Handling**: If revalidation fails, stale content continues to be served until fixed.

</details>  
</br>

<details>

<summary>4. SSG and Dynamic Data</summary>

Dynamic data in SSG can be handled by:

- **Client-Side Fetching**: Fetch dynamic data in the browser after the page has been statically generated.
  - Example: Use `fetch` or `axios` to retrieve data in a `useEffect` hook.
- **ISR**: If the dynamic data is semi-static, set a `revalidate` interval in `getStaticProps` to update the data periodically.

</details>  
</br>

<details>

<summary>5. Stale-While-Revalidate</summary>

- ISR uses the **stale-while-revalidate** strategy:
  - A statically generated page is served immediately (stale content).
  - Behind the scenes, Next.js sends a revalidation request to update the page.
  - Once revalidated, subsequent requests serve the updated content.

**Potential Risks**:

- Users might briefly see outdated content if the revalidation process is delayed or fails.
- Inconsistent behavior across multiple users during revalidation.

</details>  
</br>

<details>

<summary>6. getStaticProps</summary>

- `getStaticProps` is a Next.js function that runs at **build time** to fetch data and generate static pages.

```javascript
export async function getStaticProps() {
  const res = await fetch("https://api.example.com/data");
  const data = await res.json();

  if (!data) {
    return { notFound: true }; // Handles errors
  }

  return {
    props: { data }, // Pass data to the page
    revalidate: 10, // Enable ISR for updates
  };
}
```

**Failure during build time**:

- If `getStaticProps` fails during a build, the build itself will fail.
- Use `try-catch` to handle errors gracefully, or return `{ notFound: true }` to generate a 404 page.

</details>  
</br>

<details>

<summary>7. Dynamic Routes in SSG</summary>

Dynamic routing is handled using `getStaticPaths` with `getStaticProps`.  
For a site with thousands of pages:

- **Predefine a subset** of pages during build time using `fallback: "blocking"` or `fallback: true`.
  - Example for an e-commerce platform:

```javascript
export async function getStaticPaths() {
  const res = await fetch("https://api.example.com/products");
  const products = await res.json();

  const paths = products.slice(0, 1000).map((product) => ({
    params: { id: product.id.toString() },
  }));

  return { paths, fallback: "blocking" }; // Serve others on-demand
}
```

- Use a pagination strategy to limit the number of static pages pre-rendered.

</details>  
</br>

<details>

<summary>8. Revalidation in ISR</summary>

- **How `revalidate` works**:
  - You specify a time interval in seconds (e.g., `revalidate: 10`) in `getStaticProps`.
  - When a request is made after the interval, the page is served immediately (stale content) while a background process revalidates and updates the page.
- **What happens during a revalidation cycle**:
  - Next.js fetches the data again by rerunning `getStaticProps`.
  - If successful, the updated page replaces the stale one in the cache.
  - If the fetch fails, the old page continues to be served.

</details>  
</br>

<details>

<summary>9. Fallback Modes</summary>

- **`fallback: false`**:

  - All possible paths are pre-rendered during build time.
  - Requests to non-existent paths return a 404 page.
  - **Use Case**: When all pages are known at build time and no additional pages are expected.

- **`fallback: true`**:

  - Non-pre-rendered pages are served as a fallback (loading page) while being generated on the server. Once generated, the new page is cached and served for subsequent requests.
  - **Use Case**: Large sites where generating all paths during build time is impractical.

- **`fallback: "blocking"`**:
  - Non-pre-rendered pages wait to be generated on the server before being served to the user (no loading screen).
  - **Use Case**: When you don’t want users to see a fallback UI and prefer to delay the response until the page is ready.

</details>  
</br>

<details>

<summary>10. Environment Variables</summary>

To manage sensitive data when pre-rendering pages:

- Use `.env` files and access variables using `process.env`.
  - Example in `getStaticProps`:
    ```javascript
    export async function getStaticProps() {
      const apiKey = process.env.API_KEY;
      const res = await fetch(`https://api.example.com/data?key=${apiKey}`);
      const data = await res.json();
      return { props: { data } };
    }
    ```
- **Security Concerns**:
  - Ensure that sensitive variables (e.g., API keys) are not exposed in the browser. Only expose variables prefixed with `NEXT_PUBLIC_`.

</details>  
</br>

<details>

<summary>11. Cache Invalidation in ISR</summary>

- **How Cache Invalidation Works**:

  - ISR serves the existing static page (from cache) while revalidation updates the cache in the background.
  - If revalidation fails, the cached page remains until a successful revalidation occurs.

- **Risks**:
  - If revalidation fails repeatedly, stale content might be served for longer than expected.
  - To mitigate this, you can implement fallback error-handling logic to handle failed revalidations.

</details>  
</br>

<details>

<summary>12. Concurrency in ISR</summary>

- If multiple users request the same page while ISR is regenerating:
  - The first user gets the stale content.
  - Other concurrent requests wait until the regeneration process finishes.
  - Once the page is updated, all subsequent users receive the new content.
- **Concurrency Handling**: ISR ensures only one revalidation request is processed at a time for a given page, avoiding redundant re-generation.

</details>  
</br>

<details>

<summary>13. Custom Headers in ISR</summary>

- You can add custom headers by configuring the `next.config.js` file:
  ```javascript
  module.exports = {
    async headers() {
      return [
        {
          source: "/path",
          headers: [{ key: "Custom-Header", value: "HeaderValue" }],
        },
      ];
    },
  };
  ```
- Middleware can also inject headers dynamically during runtime if ISR is used for dynamic pages.

</details>  
</br>

<details>

<summary>14. Static Export</summary>

- **`next export`**: Generates a fully static site with no serverless functions or ISR capabilities. Pages are purely HTML and served from a CDN.
- **When Preferred**:
  - When you don’t need ISR or dynamic routes.
  - For ultra-fast performance with limited backend interaction (e.g., a marketing site).

</details>  
</br>

<details>

<summary>15. Handling Webhooks in ISR</summary>

- Implement an API route to trigger revalidation:
  ```javascript
  export default async function handler(req, res) {
    const { path } = req.body; // Path to revalidate
    await res.revalidate(path); // Trigger ISR
    res.status(200).json({ message: "Revalidated" });
  }
  ```
- **Security Measures**:
  - Verify the webhook source using a shared secret or HMAC signature.
  - Example:
    ```javascript
    if (req.headers["x-webhook-secret"] !== process.env.WEBHOOK_SECRET) {
      return res.status(401).json({ message: "Unauthorized" });
    }
    ```

</details>  
</br>

<details>

<summary>16. Scaling ISR</summary>

- **Optimize `getStaticPaths`**: Pre-render only the most popular or critical pages during build time.
- **Batch Data Fetching**: Use pagination or data chunking to limit the number of pages being generated.
- Use **fallback modes** to generate pages dynamically on-demand.

</details>  
</br>

<details>

<summary>17. Incremental Deployment</summary>

- ISR enables incremental deployment by regenerating pages individually instead of rebuilding the entire app.
- **Partial Failures**:
  - Pages that fail to regenerate might serve stale content until the next revalidation succeeds.
  - Logs and monitoring are essential to track such failures.

</details>  
</br>

<details>

<summary>18. SEO Challenges in ISR</summary>

- **Challenges**:
  - If the page is stale during a revalidation cycle, search engines might index outdated content.
  - Large intervals in the `revalidate` setting might delay content updates.
- **Mitigation**:
  - Use smaller `revalidate` intervals for SEO-critical pages.
  - Submit updated sitemaps to search engines when pages are regenerated.

</details>  
</br>

<details>

<summary>19. Real-Time Analytics</summary>

- Fetch analytics data client-side after the page is statically generated.
  - Example using `useEffect`:
    ```javascript
    useEffect(() => {
      fetch("/api/analytics")
        .then((res) => res.json())
        .then(setAnalytics);
    }, []);
    ```
- Alternatively, embed real-time analytics via services like Google Analytics or Segment.

</details>  
</br>

<details>

<summary>20. Middleware with ISR</summary>

- Use middleware to dynamically add authentication or authorization logic:
  ```javascript
  export function middleware(req) {
    const authToken = req.cookies["auth-token"];
    if (!authToken) {
      return new Response("Unauthorized", { status: 401 });
    }
  }
  ```
- **Pitfalls**:
  - Middleware can add latency to requests.
  - Needs careful configuration to avoid interfering with ISR regeneration logic.

</details>  
</br>

<details>

<summary>21. Dynamic Pagination</summary>

- Example code for paginated pages:

  ```javascript
  export async function getStaticPaths() {
    return { paths: [], fallback: "blocking" }; // ISR handles page generation
  }

  export async function getStaticProps({ params }) {
    const page = params.page || 1;
    const data = await fetch(`https://api.example.com/items?page=${page}`).then(
      (res) => res.json()
    );
    return { props: { data }, revalidate: 10 };
  }
  ```

- Use dynamic routes like `/page/[page]`.

</details>  
</br>

<details>

<summary>22. Large Dataset Handling</summary>

- **Optimization Tips**:
  - Pre-fetch only high-priority data during build time.
  - Use pagination in `getStaticPaths`:
    ```javascript
    export async function getStaticPaths() {
      const totalPages = 100; // Total pages of data
      const paths = Array.from({ length: totalPages }, (_, i) => ({
        params: { page: (i + 1).toString() },
      }));
      return { paths, fallback: "blocking" };
    }
    ```

</details>  
</br>

<details>

<summary>23. Custom Revalidation Trigger</summary>

- Example API route:
  ```javascript
  export default async function handler(req, res) {
    await res.revalidate("/path-to-revalidate");
    res.status(200).json({ message: "Revalidation triggered" });
  }
  ```
- Use webhooks or external events to call this API.

</details>  
</br>

<details>

<summary>24. Conditional Revalidation</summary>

- Example:
  ```javascript
  export async function getStaticProps() {
    const shouldUpdate = await checkDataFreshness();
    if (!shouldUpdate) {
      return { props: {}, revalidate: 60 };
    }
    const data = await fetchData();
    return { props: { data }, revalidate: 10 };
  }
  ```

</details>  
</br>

<details>

<summary>25. Fallback UI Implementation</summary>

- Example with all fallback modes:

  ```javascript
  export async function getStaticPaths() {
    return { paths: [], fallback: "blocking" };
  }

  export async function getStaticProps({ params }) {
    const data = await fetchData(params.id);
    return { props: { data }, revalidate: 10 };
  }
  ```

- Use React components to handle loading and error states for `fallback: true`.

</details>  
</br>

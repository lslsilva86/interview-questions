<details>

<summary>1. Explain the difference between REST and GraphQL in terms of data fetching and state management in a client application.</summary>

- **REST** relies on multiple endpoints, each returning a fixed data structure. Over-fetching or under-fetching can occur as you fetch either too much data or lack necessary fields.
- **GraphQL** uses a single endpoint and allows clients to request precisely the data they need using queries, avoiding over-fetching or under-fetching.
- **State Management**: REST often requires more custom logic to handle data normalization across multiple endpoints. GraphQL clients like Apollo and Relay simplify this by normalizing data into a client-side cache, making state management more efficient.

</details>

</br>

<details>

<summary>2. How does GraphQL handle over-fetching and under-fetching issues? Provide examples of these scenarios in the context of Apollo Client or Relay.</summary>

- **Over-fetching**: In REST, fetching a `User` endpoint might return extra fields like `address` or `phone` even when only `name` and `email` are required. GraphQL solves this by letting clients specify exactly what fields to fetch:
  ```graphql
  query GetUser {
    user {
      name
      email
    }
  }
  ```
- **Under-fetching**: REST might require multiple endpoints for related data (e.g., `/users` and `/users/{id}/posts`). In GraphQL, a single query fetches nested data:
  ```graphql
  query GetUserWithPosts {
    user {
      name
      email
      posts {
        title
      }
    }
  }
  ```
- **In Apollo Client or Relay**: These tools automatically normalize and cache responses, making subsequent queries efficient without refetching unnecessary data.

</details>

</br>

<details>

<summary>3. What are GraphQL fragments, and why are they useful when fetching data with Apollo Client or Relay?</summary>

- **GraphQL fragments** are reusable units of query logic that encapsulate field selection. They help prevent redundancy and maintain consistency in queries.
- Example:

  ```graphql
  fragment UserFields on User {
    id
    name
    email
  }

  query GetUser {
    user {
      ...UserFields
    }
  }
  ```

- **Why Useful**:
  - Promote reusability across queries and components.
  - Simplify maintenance by centralizing shared logic.
  - In Apollo and Relay, fragments improve performance by leveraging cache normalization.

</details>

</br>

<details>

<summary>4. What are common security considerations when integrating GraphQL APIs into a frontend application? How would you mitigate risks?</summary>

- **Security Considerations**:

  - **Excessive Queries**: Clients might query large amounts of data.
  - **Sensitive Data Exposure**: Leaks may occur if access control is weak.
  - **Introspection Misuse**: Attackers can discover the schema.
  - **Injection Attacks**: Malicious input in variables or queries.
  - **DDoS Attacks**: Complex queries can overwhelm the server.

- **Mitigation**:
  - Limit query depth and complexity using libraries like `graphql-depth-limit` or `graphql-query-complexity`.
  - Use schema-level authorization (e.g., `@auth` directives) and ensure role-based access control (RBAC).
  - Disable introspection in production environments.
  - Sanitize and validate all input, including variables.
  - Rate-limit queries and use caching layers like Apollo Gateway.

</details>

</br>

<details>

<summary>5. What is the difference between `useQuery` and `useLazyQuery` in Apollo Client? When would you choose one over the other?</summary>

- **`useQuery`**: Runs immediately when the component mounts or when its dependencies change.
  - Use when data is required on load.
  - Example:
    ```javascript
    const { data, loading, error } = useQuery(GET_USERS);
    ```
- **`useLazyQuery`**: Returns a trigger function to execute the query manually.
  - Use when you need to fetch data based on user interaction or specific conditions.
  - Example:
    ```javascript
    const [getUsers, { data, loading, error }] = useLazyQuery(GET_USERS);
    // Trigger manually
    getUsers();
    ```

</details>

</br>

<details>

<summary>6. How does Apollo Client handle caching? Explain the different caching strategies (`cache-first`, `cache-and-network`, etc.) and when to use them.</summary>

- Apollo Client uses a normalized **in-memory cache** to store query results, identified by unique keys (`id` or `__typename`).
- **Caching Strategies**:
  - **`cache-first`**: Default strategy; retrieves data from cache first, falls back to the network only if not available.
    - Use: To prioritize performance when data rarely changes.
  - **`network-only`**: Fetches data from the server every time, ignoring the cache.
    - Use: When cache staleness is unacceptable.
  - **`cache-and-network`**: Retrieves data from cache, then updates it with a network response.
    - Use: When quick responses are needed, but fresh data is preferred.
  - **`no-cache`**: Bypasses the cache entirely.
    - Use: For non-cacheable, sensitive, or transient data.

</details>

</br>

<details>

<summary>7. How would you normalize the Apollo Client cache to handle related and nested data structures effectively?</summary>

- **Normalization**: Apollo Client normalizes cache by default using unique identifiers (`__typename` and `id`/`_id`).
- **Custom Normalization**:
  - Specify custom `keyFields` in the Apollo cache configuration for non-default identifiers.
    ```javascript
    new InMemoryCache({
      typePolicies: {
        Book: {
          keyFields: ["isbn"], // Custom key for normalization
        },
      },
    });
    ```
- **Nested Data**: Use GraphQL fragments to normalize and update nested structures efficiently:
  ```graphql
  fragment AuthorFields on Author {
    id
    name
  }
  query GetBooks {
    books {
      id
      title
      author {
        ...AuthorFields
      }
    }
  }
  ```

</details>

</br>

<details>

<summary>8. Describe the steps for integrating Apollo Client with a React application. How would you handle authentication for secured GraphQL queries?</summary>

- **Steps**:

  1. Install Apollo Client:
     ```bash
     npm install @apollo/client graphql
     ```
  2. Configure Apollo Client:

     ```javascript
     import {
       ApolloClient,
       InMemoryCache,
       ApolloProvider,
     } from "@apollo/client";

     const client = new ApolloClient({
       uri: "https://your-graphql-endpoint.com/graphql",
       cache: new InMemoryCache(),
     });
     ```

  3. Wrap the React app with `ApolloProvider`:
     ```javascript
     <ApolloProvider client={client}>
       <App />
     </ApolloProvider>
     ```
  4. Use hooks (`useQuery`, `useMutation`) to fetch or modify data.

- **Handling Authentication**:

  - Add an authentication token to the `Authorization` header in `ApolloClient`'s `setContext`:

    ```javascript
    import {
      ApolloClient,
      InMemoryCache,
      createHttpLink,
    } from "@apollo/client";
    import { setContext } from "@apollo/client/link/context";

    const httpLink = createHttpLink({
      uri: "https://your-graphql-endpoint.com/graphql",
    });

    const authLink = setContext((_, { headers }) => {
      const token = localStorage.getItem("authToken");
      return {
        headers: {
          ...headers,
          authorization: token ? `Bearer ${token}` : "",
        },
      };
    });

    const client = new ApolloClient({
      link: authLink.concat(httpLink),
      cache: new InMemoryCache(),
    });
    ```

</details>

</br>

<details>

<summary>9. How do you handle pagination with Apollo Client? Provide examples of both `offset-based` and `cursor-based` pagination using Apollo.</summary>

- **Offset-based Pagination**: Use an `offset` and `limit` to fetch a specific range of data. Suitable for lists where order is stable.
  Example:

  ```graphql
  query GetUsers($offset: Int, $limit: Int) {
    users(offset: $offset, limit: $limit) {
      id
      name
    }
  }
  ```

  - Fetch more data in Apollo:

    ```javascript
    const { data, fetchMore } = useQuery(GET_USERS, {
      variables: { offset: 0, limit: 10 },
    });

    const loadMore = () => {
      fetchMore({
        variables: { offset: data.users.length, limit: 10 },
      });
    };
    ```

- **Cursor-based Pagination**: Uses `after` or `before` cursors for dynamic data.
  Example:

  ```graphql
  query GetUsers($after: String) {
    usersConnection(after: $after) {
      edges {
        node {
          id
          name
        }
      }
      pageInfo {
        endCursor
        hasNextPage
      }
    }
  }
  ```

  - Apollo Example:

    ```javascript
    const { data, fetchMore } = useQuery(GET_USERS, {
      variables: { after: null },
    });

    const loadMore = () => {
      if (data.usersConnection.pageInfo.hasNextPage) {
        fetchMore({
          variables: { after: data.usersConnection.pageInfo.endCursor },
        });
      }
    };
    ```

</details>

</br>

<details>

<summary>10. What are Apollo Client `directives`, and how can they be used to control the behavior of a query (e.g., `@include`, `@skip`)?</summary>

- **Apollo Client Directives** are used to dynamically control the execution of queries based on conditions.

  - **`@include`**: Includes a field only if the condition is `true`.
  - **`@skip`**: Skips a field if the condition is `true`.

- Example:

  ```graphql
  query GetUser($includeEmail: Boolean!) {
    user {
      id
      name
      email @include(if: $includeEmail)
    }
  }
  ```

- Apollo usage:
  ```javascript
  const { data } = useQuery(GET_USER, {
    variables: { includeEmail: true },
  });
  ```

</details>

</br>

<details>

<summary>11. What is Relay's approach to data normalization, and how does it differ from Apollo Client?</summary>

- **Relay's Normalization**:

  - Relay normalizes all data based on unique identifiers (`__id`) and stores it in a flat structure.
  - It enforces strict schema adherence and compiles queries into optimized artifacts at build time.

- **Differences from Apollo**:
  - **Apollo** dynamically parses queries at runtime and allows more flexibility in schema design.
  - **Relay** prioritizes performance and type safety but requires a more rigid setup.

</details>

</br>

<details>

<summary>12. How does Relay handle fragments differently than Apollo Client, and why is this significant for large-scale applications?</summary>

- **Relay Fragments**:

  - Relay enforces colocated fragments, meaning fragments are defined and used directly in the components that consume them.
  - Ensures components only request the data they need, making queries modular and reusable.

- **Significance**:
  - Reduces the risk of over-fetching or under-fetching.
  - Simplifies maintenance by ensuring components can define their own data requirements independently.

</details>

</br>

<details>

<summary>13. Explain the Relay Modern store structure and how it supports optimistic UI updates.</summary>

- **Store Structure**:

  - Relay Modern stores data in a normalized format, mapping unique IDs to fields and relationships.
  - Updates to the store propagate automatically to components using the data.

- **Optimistic Updates**:
  - Relay allows temporary updates to the store before a mutation completes, ensuring a seamless user experience.
  - Example:
    ```javascript
    commitMutation(environment, {
      mutation: AddTodoMutation,
      variables: { text },
      optimisticResponse: {
        addTodo: {
          id: "temp-id",
          text,
        },
      },
    });
    ```

</details>

</br>

<details>

<summary>14. What are `connections` in Relay? How would you implement pagination using Relay's `connection` specifications?</summary>

- **Connections**:

  - Relay uses `connections` as a standardized way to model lists with pagination and metadata.
  - Provides `edges` (list items) and `pageInfo` (pagination details).

- **Implementation**:
  - Query Example:
    ```graphql
    query GetTodos($first: Int, $after: String) {
      todosConnection(first: $first, after: $after) {
        edges {
          node {
            id
            text
          }
        }
        pageInfo {
          hasNextPage
          endCursor
        }
      }
    }
    ```
  - Use Relay's `usePaginationFragment` hook for seamless pagination.

</details>

</br>

<details>

<summary>15. What is the purpose of `Relay Environment`, and how do you configure it for fetching data in a React application?</summary>

- **Relay Environment**:

  - Central configuration for managing GraphQL queries, mutations, and network operations.
  - Stores and normalizes fetched data.

- **Configuration**:

  ```javascript
  import { Environment, Network, RecordSource, Store } from "relay-runtime";

  const environment = new Environment({
    network: Network.create((operation, variables) => {
      return fetch("/graphql", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({ query: operation.text, variables }),
      }).then((response) => response.json());
    }),
    store: new Store(new RecordSource()),
  });
  ```

</details>

</br>

<details>

<summary>16. How would you debug a GraphQL query that fails to resolve data correctly using Apollo Client? Explain your approach to identifying the root cause.</summary>

1. **Verify Query Syntax**: Check for typos or structural issues in the query.
2. **Inspect Network Requests**: Use browser developer tools to verify if the query reaches the server and examine the response.
3. **Check GraphQL Playground**: Test the query directly in tools like Apollo Sandbox or GraphiQL to isolate server-side issues.
4. **Validate Cache**: Ensure the data isn’t incorrectly cached by using `fetchPolicy: 'network-only'`.
5. **Enable Apollo Logging**: Use `apollo-link-logger` or inspect Apollo DevTools to trace errors.

</details>

</br>

<details>

<summary>17. Describe a scenario where a GraphQL query with Apollo or Relay could lead to excessive client-side computation. How would you optimize it?</summary>

- **Scenario**:

  - Fetching deeply nested or large datasets without filtering or pagination.
  - Example: Querying thousands of records without a limit, leading to slow rendering and memory overhead.

- **Optimization**:
  - Use pagination (`first`, `last`, `offset`) to fetch data incrementally.
  - Apply filtering on the server to reduce the payload size.
  - Use GraphQL fragments to fetch only the required fields.

</details>

</br>

<details>

<summary>18. What challenges can arise when using Apollo Client with SSR (Server-Side Rendering), and how would you overcome them?</summary>

- **Challenges**:

  - Mismatched content between server and client if the cache isn’t properly hydrated.
  - Increased complexity in setting up SSR-compatible data fetching.

- **Solution**:
  - Use `getDataFromTree` to preload Apollo queries during SSR.
  - Hydrate the cache on the client using `ApolloProvider` and `cache.restore()`.

</details>

</br>

<details>

<summary>19. Explain how you would implement a subscription feature with Apollo Client for real-time updates in a React application.</summary>

1. Use a GraphQL server that supports subscriptions (e.g., Apollo Server with WebSocket).
2. Configure `WebSocketLink` in Apollo Client:

   ```javascript
   import {
     ApolloClient,
     InMemoryCache,
     split,
     HttpLink,
   } from "@apollo/client";
   import { WebSocketLink } from "@apollo/client/link/ws";
   import { getMainDefinition } from "@apollo/client/utilities";

   const httpLink = new HttpLink({ uri: "/graphql" });
   const wsLink = new WebSocketLink({ uri: "ws://localhost:4000/graphql" });

   const link = split(
     ({ query }) => {
       const definition = getMainDefinition(query);
       return (
         definition.kind === "OperationDefinition" &&
         definition.operation === "subscription"
       );
     },
     wsLink,
     httpLink
   );

   const client = new ApolloClient({ link, cache: new InMemoryCache() });
   ```

3. Use `useSubscription` to subscribe to real-time data:
   ```javascript
   const { data } = useSubscription(NEW_MESSAGE_SUBSCRIPTION);
   ```

</details>

</br>

<details>

<summary>20. How would you ensure type safety when using Apollo Client with TypeScript in a large-scale project?</summary>

- Use GraphQL code generators (e.g., `graphql-codegen`) to auto-generate TypeScript types:
  ```bash
  npx graphql-codegen init
  ```
- Use type-safe hooks in Apollo, like:
  ```typescript
  const { data } = useQuery<GetUserData>(GET_USER);
  ```

</details>

</br>

<details>

<summary>21. You’re working on a dashboard that makes multiple GraphQL queries simultaneously. How would you optimize the performance of these queries using Apollo Client or Relay?</summary>

- **Optimization Approaches**:
  1. **Batching Queries**:
     - Use `ApolloLinkBatchHttp` to combine multiple queries into a single network request.
     ```javascript
     import { BatchHttpLink } from "@apollo/client/link/batch-http";
     const link = new BatchHttpLink({ uri: "/graphql" });
     ```
  2. **Parallel Fetching**:
     - Use Apollo’s `useQuery` hooks to fetch multiple queries concurrently.
     ```javascript
     const { data: userData } = useQuery(GET_USER);
     const { data: productData } = useQuery(GET_PRODUCTS);
     ```
  3. **Fragment Matching**:
     - Use fragments to minimize repeated data fetching across queries.
  4. **Cache Optimization**:
     - Enable normalized caching so shared data is reused across queries.

</details>

</br>

<details>

<summary>22. How would you handle error states for a GraphQL query in Apollo Client? Provide examples of displaying fallback UI, retry mechanisms, and logging.</summary>

- **Error Handling**:
  1. **Display Fallback UI**:
     ```javascript
     const { data, loading, error } = useQuery(GET_USER);
     if (loading) return <LoadingSpinner />;
     if (error) return <ErrorFallback message={error.message} />;
     ```
  2. **Retry Mechanisms**:
     - Use Apollo Client’s retry link to handle transient errors:
     ```javascript
     import { RetryLink } from "@apollo/client/link/retry";
     const link = new RetryLink({
       delay: { initial: 300 },
       attempts: { max: 5 },
     });
     ```
  3. **Logging**:
     - Log errors using `onError` from `apollo-link-error`:
     ```javascript
     import { onError } from "@apollo/client/link/error";
     const errorLink = onError(({ graphQLErrors, networkError }) => {
       if (graphQLErrors) console.error(graphQLErrors);
       if (networkError) console.error(networkError);
     });
     ```

</details>

</br>

<details>

<summary>23. In a collaborative environment, your backend team frequently changes the GraphQL schema. How would you ensure your Apollo/Relay-based application adapts seamlessly to these changes?</summary>

- **Approach**:
  1. **Code Generation**:
     - Use tools like `graphql-codegen` to regenerate TypeScript types whenever the schema changes.
  2. **Schema Validation**:
     - Use schema validation tools like `graphql-inspector` to detect breaking changes.
  3. **Modular Fragments**:
     - Use fragments in queries so only affected parts of the schema need updates.
  4. **Communication**:
     - Maintain clear versioning or backward-compatible changes in the schema to avoid breaking the client.

</details>

</br>

<details>

<summary>24. Explain how you would handle partial data from a GraphQL query, ensuring the UI remains user-friendly.</summary>

- **Handling Partial Data**:
  1. **Loading Placeholders**:
     - Show placeholders or skeleton loaders for missing fields:
     ```javascript
     const { data, loading } = useQuery(GET_USER);
     return loading ? <Skeleton /> : <UserDetails data={data.user} />;
     ```
  2. **Conditional Rendering**:
     - Render UI only when critical data is available:
     ```javascript
     if (!data?.user) return <ErrorFallback />;
     return <UserDetails user={data.user} />;
     ```
  3. **GraphQL `@defer` Directive**:
     - Fetch non-critical data lazily (if supported by the server).

</details>

</br>

<details>

<summary>25. What steps would you take to migrate a large application from REST to GraphQL using Apollo Client or Relay without disrupting the user experience?</summary>

- **Migration Steps**:
  1. **Incremental Migration**:
     - Use a hybrid approach where both REST and GraphQL coexist during the transition.
  2. **Start with Core Features**:
     - Migrate critical endpoints first, then extend to less-used ones.
  3. **Schema Design**:
     - Collaborate with backend teams to design a scalable and efficient GraphQL schema.
  4. **Apollo Client Setup**:
     - Integrate Apollo Client and gradually replace REST API calls with GraphQL queries.
  5. **Testing**:
     - Write unit and integration tests to validate GraphQL queries and mutations during the migration.

</details>

</br>

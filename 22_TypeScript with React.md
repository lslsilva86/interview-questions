<details>

<summary>1. How do you type-check functional components' props in TypeScript? Provide examples using `interface` and `type`.</summary>

To type-check functional components' props, you can use either an `interface` or `type`.

**Using `interface`:**

```tsx
interface MyComponentProps {
  name: string;
  age: number;
}

const MyComponent: React.FC<MyComponentProps> = ({ name, age }) => (
  <div>
    <p>Name: {name}</p>
    <p>Age: {age}</p>
  </div>
);
```

**Using `type`:**

```tsx
type MyComponentProps = {
  name: string;
  age: number;
};

const MyComponent: React.FC<MyComponentProps> = ({ name, age }) => (
  <div>
    <p>Name: {name}</p>
    <p>Age: {age}</p>
  </div>
);
```

</details>  
</br>

<details>

<summary>2. What are the differences between using `interface` and `type` for defining props? Which one would you choose for React props, and why?</summary>

**Key Differences:**

- **`interface`:** Supports declaration merging, meaning you can extend it by re-declaring it multiple times.
- **`type`:** Does not support declaration merging but is more flexible for defining complex types (e.g., unions, intersections).

**Which to use for React props?**

- Use `interface` if you want to leverage inheritance and extendibility.
- Use `type` if you need union or intersection types or more advanced features.

For most React components, both work similarly, so the choice is often subjective.

</details>  
</br>

<details>

<summary>3. How do you handle deeply nested objects as props in TypeScript? Provide an example.</summary>

You can define the structure of deeply nested objects using `interface` or `type`.

```tsx
interface Address {
  street: string;
  city: string;
}

interface User {
  id: number;
  name: string;
  address: Address;
}

type UserProps = {
  user: User;
};

const UserComponent: React.FC<UserProps> = ({ user }) => (
  <div>
    <p>ID: {user.id}</p>
    <p>Name: {user.name}</p>
    <p>City: {user.address.city}</p>
  </div>
);
```

</details>  
</br>

<details>

<summary>4. How would you type-check a prop that accepts a union of multiple types, such as strings or objects?</summary>

Use a union type for the prop.

```tsx
type MyComponentProps = {
  data: string | { id: number; name: string };
};

const MyComponent: React.FC<MyComponentProps> = ({ data }) => (
  <div>
    {typeof data === "string" ? (
      <p>{data}</p>
    ) : (
      <p>
        {data.name} (ID: {data.id})
      </p>
    )}
  </div>
);
```

</details>  
</br>

<details>

<summary>5. How do you define default props in a functional component with TypeScript?</summary>

Use default parameter values or define `defaultProps`.

**Default parameter values:**

```tsx
interface MyComponentProps {
  name?: string;
}

const MyComponent: React.FC<MyComponentProps> = ({ name = "Guest" }) => (
  <p>Hello, {name}!</p>
);
```

**With `defaultProps`:**

```tsx
interface MyComponentProps {
  name: string;
}

const MyComponent: React.FC<MyComponentProps> = ({ name }) => (
  <p>Hello, {name}!</p>
);

MyComponent.defaultProps = {
  name: "Guest",
};
```

</details>  
</br>

<details>

<summary>6. How does the `Partial` utility type affect type-checking in default props?</summary>

The `Partial` utility makes all properties optional. It is helpful for defining default props when you don't want all props to be mandatory.

```tsx
interface MyComponentProps {
  name: string;
  age: number;
}

const MyComponent: React.FC<Partial<MyComponentProps>> = ({
  name = "Guest",
  age = 25,
}) => (
  <p>
    {name} is {age} years old.
  </p>
);
```

</details>  
</br>

<details>

<summary>7. How do you type the `children` prop in TypeScript? Provide examples for different children types (e.g., string, JSX elements, render props).</summary>

**Typing `children` as a string:**

```tsx
interface MyComponentProps {
  children: string;
}

const MyComponent: React.FC<MyComponentProps> = ({ children }) => (
  <p>{children}</p>
);
```

**Typing `children` as JSX:**

```tsx
interface MyComponentProps {
  children: React.ReactNode;
}

const MyComponent: React.FC<MyComponentProps> = ({ children }) => (
  <div>{children}</div>
);
```

**Typing render props:**

```tsx
interface MyComponentProps {
  children: (name: string) => JSX.Element;
}

const MyComponent: React.FC<MyComponentProps> = ({ children }) =>
  children("React");
```

</details>  
</br>

<details>

<summary>8. How do you type-check the state in a class component? Explain with an example.</summary>

Define a separate `State` interface and use it in the class definition.

```tsx
interface MyState {
  count: number;
}

class Counter extends React.Component<{}, MyState> {
  state: MyState = { count: 0 };

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return <button onClick={this.increment}>Count: {this.state.count}</button>;
  }
}
```

</details>  
</br>

<details>

<summary>9. In functional components, how do you manage state with dynamic keys or values that can be of multiple types?</summary>

Use a mapped type to type the state.

```tsx
type State = {
  [key: string]: string | number | boolean;
};

const MyComponent: React.FC = () => {
  const [state, setState] = React.useState<State>({});

  const updateState = (key: string, value: string | number | boolean) => {
    setState((prev) => ({ ...prev, [key]: value }));
  };

  return <button onClick={() => updateState("isActive", true)}>Click</button>;
};
```

</details>  
</br>

<details>

<summary>10. If you have a component that receives a function as a prop, how do you ensure it has the correct signature using TypeScript?</summary>

Define the function's signature in the prop type.

```tsx
type MyComponentProps = {
  onClick: (id: number) => void;
};

const MyComponent: React.FC<MyComponentProps> = ({ onClick }) => (
  <button onClick={() => onClick(42)}>Click Me</button>
);
```

</details>  
</br>

<details>

<summary>11. What are the trade-offs of using `PropTypes` versus TypeScript for props validation in a React project?</summary>

| Feature             | TypeScript                                   | PropTypes                                   |
| ------------------- | -------------------------------------------- | ------------------------------------------- |
| **Compile-time**    | Static type-checking during development.     | No compile-time checks; runtime validation. |
| **Runtime checks**  | None (requires libraries like `zod`).        | Validates props at runtime.                 |
| **Tooling support** | Strong IDE support (autocompletion, errors). | Limited tooling support.                    |
| **Performance**     | No runtime overhead.                         | Slight runtime overhead.                    |

**Trade-offs:**

- **TypeScript** provides better developer experience, static checking, and scalability.
- **PropTypes** is useful for runtime validation but redundant if TypeScript is used.

</details>  
</br>

<details>

<summary>12. How do you type the state returned by the `useState` hook? Provide examples for primitive types, objects, and arrays.</summary>

To type the state returned by `useState`, you can use a generic type parameter.

**Primitive type:**

```tsx
const [count, setCount] = React.useState<number>(0);
```

**Object:**

```tsx
interface User {
  id: number;
  name: string;
}

const [user, setUser] = React.useState<User | null>(null);
```

**Array:**

```tsx
const [items, setItems] = React.useState<string[]>([]);
```

</details>  
</br>

<details>

<summary>13. Explain how you handle typing in a `useState` hook where the type needs to change dynamically (e.g., toggling between string and number).</summary>

Use a union type to define the possible state types.

```tsx
const [value, setValue] = React.useState<string | number>("initial");

setValue(42); // valid
setValue("new value"); // valid
```

For more complex scenarios, consider using a discriminated union with custom logic.

</details>  
</br>

<details>

<summary>14. How do you create a custom hook that takes arguments and returns a typed object? Provide an example.</summary>

```tsx
function useCustomHook<T>(initialValue: T) {
  const [state, setState] = React.useState<T>(initialValue);

  const updateState = (value: T) => setState(value);

  return { state, updateState };
}

// Usage:
const { state, updateState } = useCustomHook<string>("initial");
```

</details>  
</br>

<details>

<summary>15. Discuss how to type a custom hook that uses a generic type parameter.</summary>

```tsx
function useGenericHook<T>(items: T[]) {
  const [selectedItem, setSelectedItem] = React.useState<T | null>(null);

  const selectItem = (item: T) => setSelectedItem(item);

  return { selectedItem, selectItem };
}

// Usage:
const { selectedItem, selectItem } = useGenericHook<number>([1, 2, 3]);
```

Generics allow the hook to adapt to various types dynamically.

</details>  
</br>

<details>

<summary>16. How do you type the `state`, `action`, and `dispatch` when using the `useReducer` hook?</summary>

```tsx
interface State {
  count: number;
}

type Action = { type: "increment" } | { type: "decrement" };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error("Unknown action");
  }
}

const [state, dispatch] = React.useReducer(reducer, { count: 0 });
```

</details>  
</br>

<details>

<summary>17. Provide an example of a complex reducer that uses a discriminated union for the `action` type.</summary>

```tsx
type Action =
  | { type: "add"; payload: number }
  | { type: "subtract"; payload: number }
  | { type: "reset" };

function reducer(state: number, action: Action): number {
  switch (action.type) {
    case "add":
      return state + action.payload;
    case "subtract":
      return state - action.payload;
    case "reset":
      return 0;
    default:
      throw new Error("Invalid action type");
  }
}

const [state, dispatch] = React.useReducer(reducer, 0);
```

</details>  
</br>

<details>

<summary>18. How do you type-check a `useRef` that holds a mutable DOM element reference?</summary>

```tsx
const inputRef = React.useRef<HTMLInputElement>(null);

// Access:
<input ref={inputRef} />;
```

</details>  
</br>

<details>

<summary>19. Explain how to handle typing for `useRef` with a mutable value that can be `null` initially.</summary>

Use a union type:

```tsx
const ref = React.useRef<string | null>(null);

// Update:
ref.current = "value";
```

</details>  
</br>

<details>

<summary>20. How do you type the `useContext` hook for a React context with TypeScript?</summary>

```tsx
interface AppContextType {
  user: string;
  isLoggedIn: boolean;
}

const AppContext = React.createContext<AppContextType | null>(null);

const useAppContext = () => {
  const context = React.useContext(AppContext);
  if (!context)
    throw new Error("useAppContext must be used within AppProvider");
  return context;
};
```

</details>  
</br>

<details>

<summary>21. Provide an example of using TypeScript to enforce strict type-checking in a context with default values.</summary>

```tsx
const AppContext = React.createContext({ user: "", isLoggedIn: false });

const useAppContext = () => React.useContext(AppContext);

// Usage:
const { user, isLoggedIn } = useAppContext();
```

</details>  
</br>

<details>

<summary>22. How do you ensure that the dependency array of a `useEffect` hook is correctly typed?</summary>

TypeScript infers the types from the variables used in the dependency array, so ensure the variables have proper types.

```tsx
const [count, setCount] = React.useState<number>(0);

React.useEffect(() => {
  console.log(count);
}, [count]); // Type-safe dependency array
```

</details>  
</br>

<details>

<summary>23. Provide an example of using a custom hook with `useEffect` that handles asynchronous data fetching, ensuring type safety.</summary>

```tsx
function useFetch<T>(url: string) {
  const [data, setData] = React.useState<T | null>(null);

  React.useEffect(() => {
    const fetchData = async () => {
      const response = await fetch(url);
      const result = await response.json();
      setData(result);
    };
    fetchData();
  }, [url]);

  return data;
}

// Usage:
const data = useFetch<{ id: number; name: string }>("https://api.example.com");
```

</details>  
</br>

<details>

<summary>24. Suppose you have a component that accepts props with dynamic keys and values of varying types (e.g., string or number). How would you define and validate the types for this component?</summary>

```tsx
type MyComponentProps = {
  [key: string]: string | number;
};

const MyComponent: React.FC<MyComponentProps> = (props) => (
  <div>
    {Object.entries(props).map(([key, value]) => (
      <p key={key}>
        {key}: {value}
      </p>
    ))}
  </div>
);
```

</details>  
</br>

<details>

<summary>25. How do you handle type-checking for a context that provides optional values? What challenges arise when consuming the context in child components?</summary>

Use a union type for the context:

```tsx
interface ContextType {
  value?: string;
}

const MyContext = React.createContext<ContextType>({});
```

**Challenge:** Ensure consumers handle the optional value properly, e.g., with null-checking.

</details>  
</br>

<details>

<summary>26. How do you enforce type safety in higher-order components (HOCs) that wrap other components?</summary>

```tsx
function withLogger<T>(Component: React.ComponentType<T>): React.FC<T> {
  return (props) => {
    console.log("Props:", props);
    return <Component {...props} />;
  };
}

// Usage:
const EnhancedComponent = withLogger(MyComponent);
```

</details>  
</br>

<details>

<summary>27. Create a generic React component that renders a list of items and ensures type safety for the list items and their event handlers.</summary>

```tsx
interface ListProps<T> {
  items: T[];
  onClick: (item: T) => void;
}

function List<T>({ items, onClick }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index} onClick={() => onClick(item)}>
          {String(item)}
        </li>
      ))}
    </ul>
  );
}

// Usage:
<List items={[1, 2, 3]} onClick={(item) => console.log(item)} />;
```

</details>  
</br>

<details>

<summary>28. Explain how you would use utility types such as `Pick`, `Omit`, and `Record` in TypeScript to manage props for a React component.</summary>

- **Pick:** Select specific props.

```tsx
type PickedProps = Pick<ComponentProps, "id" | "name">;
```

- **Omit:** Exclude specific props.

```tsx
type OmittedProps = Omit<ComponentProps, "password">;
```

- **Record:** Map a key type to a value type.

```tsx
type Dictionary = Record<string, number>;
```

</details>  
</br>

<details>

<summary>29. How do you combine `Partial` and `Required` to enforce conditional type-checking on props?</summary>

```tsx
type CombinedProps = Partial<MyProps> & Required<Pick<MyProps, "id">>;
```

</details>  
</br>

<details>

<summary>30. Write a React component with the following requirements:
- Accepts props containing a list of items of type `T` (generic).
- Renders each item with a custom render function passed as a prop.
- Ensures type safety for both the items and the render function.</summary>

```tsx
interface ListProps<T> {
  items: T[];
  render: (item: T) => JSX.Element;
}

function List<T>({ items, render }: ListProps<T>) {
  return <div>{items.map((item, index) => render(item))}</div>;
}

// Usage:
<List items={[1, 2, 3]} render={(item) => <p>{item}</p>} />;
```

</details>  
</br>

<details>

<summary>31. Create a custom hook for managing a form's state in React with TypeScript.</summary>

```tsx
function useForm<T>(initialState: T) {
  const [form, setForm] = React.useState<T>(initialState);

  const handleChange = (key: keyof T, value: T[keyof T]) => {
    setForm((prev) => ({ ...prev, [key]: value }));
  };

  return { form, handleChange };
}

// Usage:
const { form, handleChange } = useForm({ name: "", email: "" });
```

</details>  
</br>

<details>

<summary>32. Implement a `useDebounce` custom hook in React with TypeScript.</summary>

```tsx
function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = React.useState(value);

  React.useEffect(() => {
    const handler = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
}

// Usage:
const debouncedSearch = useDebounce(searchTerm, 300);
```

</details>  
</br>

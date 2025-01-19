<details>

<summary>1. What is the bridge in React Native, and why is it important?</summary>

The React Native bridge is a key part of its architecture that facilitates communication between JavaScript and native modules. The bridge enables the execution of JavaScript code on one thread and native code on another, translating JSON messages back and forth. This allows React Native to leverage native components for a truly native look and feel, while keeping the logic in JavaScript.

</details>

</br>

<details>

<summary>2. How does React Native enable cross-platform app development? What are its limitations compared to truly native apps?</summary>

**How it enables cross-platform development:**

- React Native uses a single codebase written in JavaScript to target both iOS and Android platforms.
- Core components like `Text` or `View` map to native platform-specific components.
- Shared APIs abstract platform-specific logic (e.g., Camera, Geolocation).

**Limitations:**

- **Performance**: While close to native, React Native apps might not match the performance of fully native apps, especially for complex UIs or animations.
- **Native Module Dependence**: Custom native modules may need to be written if features are not available in the React Native ecosystem.
- **Platform-Specific Adjustments**: Even with shared code, there’s often a need for platform-specific tweaks in styling or logic.

</details>

</br>

<details>

<summary>3. Explain the role of the `JavaScriptCore` engine in React Native.</summary>

The `JavaScriptCore` (JSC) engine is used to execute JavaScript code in React Native.

- On iOS, JSC is bundled directly with the app.
- On Android, JSC comes with React Native and runs JavaScript within the app.
- JSC enables the execution of JavaScript code outside a web browser, allowing communication with native modules through the bridge. It ensures that React Native apps have a runtime environment for JavaScript execution.

</details>

</br>

<details>

<summary>4. What are the differences between React Native’s architecture and that of Flutter or Xamarin?</summary>

- **React Native**:
  - Uses a bridge to communicate between JavaScript and native components.
  - UI components are rendered using native views.
- **Flutter**:
  - Doesn’t rely on a bridge. Instead, it uses a custom rendering engine (Skia) to draw UI directly to the screen.
  - Provides high performance with its own rendering pipeline.
- **Xamarin**:
  - Uses Mono runtime to run C# code across platforms.
  - Provides access to native APIs via bindings and compiles code to native binaries.

</details>

</br>

<details>

<summary>5. What are the differences between `ScrollView` and `FlatList` in React Native? When would you use each?</summary>

- **`ScrollView`**:
  - Renders all child components at once.
  - Ideal for small lists or content with a fixed size.
  - May cause performance issues for large datasets due to memory overhead.
- **`FlatList`**:
  - Efficiently renders large lists by using lazy loading and virtualization.
  - Only renders items currently visible on the screen and unloads others to save memory.
  - Use for dynamic or large datasets.

</details>

</br>

<details>

<summary>6. How does `StyleSheet` in React Native improve performance compared to inline styling?</summary>

- **Performance Improvements:**
  - Styles defined using `StyleSheet.create` are compiled and optimized during runtime, reducing the overhead of recreating style objects during each render.
  - Inline styles create a new object every time, causing unnecessary re-renders as React Native cannot memoize them effectively.

</details>

</br>

<details>

<summary>7. What are the main differences between controlled and uncontrolled components in React Native forms?</summary>

- **Controlled Components**:
  - State is managed in React using `useState` or similar methods.
  - The `value` of the input is set explicitly from the component state.
  - Example:
    ```jsx
    const [text, setText] = useState("");
    <TextInput value={text} onChangeText={setText} />;
    ```
- **Uncontrolled Components**:
  - State is managed by the DOM or native system, not React.
  - Use a `ref` to directly access the input’s current value.
  - Example:
    ```jsx
    const inputRef = useRef();
    <TextInput ref={inputRef} />;
    ```

</details>

</br>

<details>

<summary>8. Explain how the `Touchable` components work in React Native. What is the difference between `TouchableOpacity` and `TouchableWithoutFeedback`?</summary>

- **Touchable Components**:
  - Handle user interactions like taps or presses.
  - Common `Touchable` components: `TouchableOpacity`, `TouchableWithoutFeedback`, `TouchableHighlight`, and `TouchableNativeFeedback`.
- **`TouchableOpacity`**:
  - Provides feedback by reducing the opacity of the child view when pressed.
  - Often used for buttons or interactive elements.
- **`TouchableWithoutFeedback`**:
  - Doesn’t provide any visual feedback on press.
  - Useful for dismissing the keyboard or handling interactions where visual feedback isn’t needed.

</details>

</br>

<details>

<summary>9. Describe the purpose and use of the `SafeAreaView` component. Why is it important for cross-platform consistency?</summary>

- **Purpose**:
  - Ensures content doesn’t overlap with areas of the screen that are obstructed by hardware features like notches, status bars, or rounded corners.
- **Use**:
  - Wraps top-level or critical components to automatically apply safe margins or padding.
  - Example:
    ```jsx
    import { SafeAreaView } from "react-native";
    <SafeAreaView>
      <Text>Hello World!</Text>
    </SafeAreaView>;
    ```
- **Importance**:
  - Provides consistent layout behavior across different devices and platforms, especially with varying screen designs.

</details>

</br>

<details>

<summary>10. Explain the difference between using `useState` and `useReducer` for state management in a React Native app.</summary>

- **`useState`**:

  - Best for managing simple, local state.
  - Provides a straightforward API for state updates.
  - Example:
    ```jsx
    const [count, setCount] = useState(0);
    setCount(count + 1);
    ```

- **`useReducer`**:
  - Suitable for complex state logic or when multiple sub-values are updated.
  - Follows a reducer pattern, making it easier to manage intricate state updates.
  - Example:
    ```jsx
    const [state, dispatch] = useReducer(reducer, initialState);
    dispatch({ type: "increment" });
    ```

</details>

</br>

<details>

<summary>11. What are the common patterns for managing global state in React Native applications?</summary>

- **Context API**: Useful for lightweight global state management without additional libraries.
- **Redux**: Ideal for large-scale applications requiring predictable state and middleware like Redux Thunk or Redux Saga.
- **MobX**: Focuses on observables for reactive programming, providing simplicity and performance.
- **Recoil or Zustand**: Lightweight state management solutions gaining popularity for modern apps.

</details>

</br>

<details>

<summary>12. How do you decide between using Redux, Context API, or other libraries like MobX for state management in React Native?</summary>

- **Redux**:

  - Use when you need predictable state management, middleware, or tools like Redux DevTools.
  - Ideal for apps with large, complex state.

- **Context API**:

  - Use for lightweight state-sharing scenarios without requiring middleware or tools.
  - Avoid for deeply nested components with frequent updates (performance issues).

- **MobX**:

  - Use for simple, reactive state management with minimal boilerplate.

- **Recoil or Zustand**:
  - Consider when you need flexibility and modern approaches to state management.

</details>

</br>

<details>

<summary>13. How does the Flexbox layout system differ between React Native and CSS for the web?</summary>

- **Key differences**:
  - The default `flexDirection` in React Native is `column`, whereas in CSS it’s `row`.
  - Percentages are not supported for width/height in React Native; you use absolute values or `flex`.

</details>

</br>

<details>

<summary>14. What are the performance implications of using `position: absolute` in React Native?</summary>

- Using `position: absolute` can increase reflow and repaint operations, especially if elements frequently update.
- It's suitable for static elements or overlays but should be minimized in dynamic UIs.

</details>

</br>

<details>

<summary>15. Explain how to implement a responsive design in React Native. What tools or libraries can assist with responsiveness?</summary>

- **Techniques**:
  - Use `Dimensions` API for screen width/height.
  - Use `flexbox` for layout.
  - Use percentage-based or relative dimensions.
- **Libraries**:
  - `react-native-responsive-dimensions`
  - `react-native-device-info`
  - `react-native-size-matters`

</details>

</br>

<details>

<summary>16. What are the key differences between `React Navigation` and `React Native Navigation`?</summary>

- **React Navigation**:

  - Fully JavaScript-based.
  - Easier to set up and customize.

- **React Native Navigation**:
  - Uses native views for navigation, resulting in better performance.
  - Requires native code setup.

</details>

</br>

<details>

<summary>17. Explain how stack, tab, and drawer navigators are implemented in React Navigation.</summary>

- **Stack Navigator**: Provides a navigation stack (e.g., back button).
- **Tab Navigator**: Implements bottom or top tabs for navigation.
- **Drawer Navigator**: Creates a sidebar navigation menu.

Example:

```jsx
const Stack = createStackNavigator();
<NavigationContainer>
  <Stack.Navigator>
    <Stack.Screen name="Home" component={HomeScreen} />
  </Stack.Navigator>
</NavigationContainer>;
```

</details>

</br>

<details>

<summary>18. What challenges might arise when managing navigation in a complex app, and how would you address them?</summary>

- **Challenges**:

  - Nested navigators complexity.
  - Managing navigation state across modules.
  - Handling deep linking.

- **Solutions**:
  - Use `navigationRef` for programmatic navigation.
  - Centralize navigation logic.
  - Test deep linking thoroughly.

</details>

</br>

<details>

<summary>19. How do you handle platform-specific differences (iOS vs. Android) in React Native?</summary>

- Use `Platform` API:

  ```jsx
  import { Platform } from "react-native";
  const marginTop = Platform.OS === "ios" ? 20 : 0;
  ```

- Conditional imports for components or styles.

</details>

</br>

<details>

<summary>20. What are the key differences between using `PermissionsAndroid` and `react-native-permissions`?</summary>

- **`PermissionsAndroid`**:

  - Android-only permissions API.
  - Handles runtime permissions directly.

- **`react-native-permissions`**:
  - Cross-platform API.
  - Simplifies handling iOS and Android permissions.

</details>

</br>

<details>

<summary>21. How do you handle platform-specific styling and component behavior in a React Native app?</summary>

- Use `Platform` API for conditional logic.
- Use `Platform.OS` for styling or components.
- Example:
  ```jsx
  const styles = StyleSheet.create({
    text: {
      fontSize: Platform.OS === "ios" ? 20 : 18,
    },
  });
  ```

</details>

</br>

<details>

<summary>22. What are native modules in React Native? Can you walk through how to create one?</summary>

- **Native modules** allow JavaScript to interact with platform-specific code.
- **Steps to create**:
  - Write native code in Java/Kotlin (Android) or Swift/Objective-C (iOS).
  - Register it with React Native.
  - Expose it to JavaScript.

</details>

</br>

<details>

<summary>23. How do you optimize a React Native app for performance on low-end devices?</summary>

- Avoid heavy animations and excessive re-renders.
- Use FlatList or SectionList for large lists.
- Optimize images using `react-native-fast-image`.
- Minimize the JavaScript thread workload.

</details>

</br>

<details>

<summary>24. What strategies would you use to reduce the JavaScript thread load in a React Native app?</summary>

- Debounce/throttle expensive operations.
- Offload heavy computations to native modules.
- Use libraries like `reanimated` for animations.

</details>

</br>

<details>

<summary>25. How do you debug performance issues in React Native?</summary>

- Use `react-devtools` for inspecting component trees.
- Use `Profiler` to track performance bottlenecks.
- Monitor network requests and memory usage.

</details>

</br>

<details>

<summary>26. What tools do you use for logging and monitoring issues in production React Native apps?</summary>

- **Tools**:
  - `Sentry` for error tracking.
  - `Reactotron` for debugging.
  - `Flipper` for inspecting React Native apps.

</details>

</br>

<details>

<summary>27. What’s the difference between using Enzyme and React Native Testing Library for testing components?</summary>

- **Enzyme**:

  - Focuses on shallow rendering.
  - Requires setup and is better suited for legacy codebases.

- **React Native Testing Library**:
  - Encourages testing behavior over implementation details.
  - Works well with modern React components.

</details>

</br>

<details>

<summary>28. What are the best practices for handling API requests and managing data in React Native?</summary>

- Use `axios` or `fetch` for API requests.
- Use caching for repeated data.
- Use `redux-thunk` or `redux-saga` for async logic.

</details>

</br>

<details>

<summary>29. How do you handle large JSON data in React Native without freezing the UI?</summary>

- Use pagination or infinite scrolling.
- Offload processing to a Web Worker or native thread.

</details>

</br>

<details>

<summary>30. What challenges might you face when implementing WebSockets or SSE in a React Native app?</summary>

- Handling reconnections and retries.
- Managing the lifecycle of WebSocket connections.

</details>

</br>

<details>

<summary>31. How would you implement offline capabilities in a React Native app?</summary>

- Use `AsyncStorage` or libraries like `redux-persist`.
- Cache API data and sync when online.

</details>

</br>

<details>

<summary>32. What are the steps to integrate device features like GPS, camera, or push notifications in a React Native app?</summary>

- Use libraries like `react-native-geolocation` or `react-native-camera`.
- Add necessary permissions to `Info.plist` (iOS) or `AndroidManifest.xml` (Android).

</details>

</br>

<details>

<summary>33. Explain how to handle app lifecycle events in React Native.</summary>

- Use `AppState` API:
  ```jsx
  import { AppState } from "react-native";
  AppState.addEventListener("change", handleAppStateChange);
  ```

</details>

</br>

<details>

<summary>34. What are the differences between a Hybrid App, a Progressive Web App (PWA), and a React Native App?</summary>

- **Hybrid App**: Uses a WebView; less performant.
- **PWA**: Web-based; limited access to device APIs.
- **React Native App**: Fully native components; better performance.

</details>

</br>

<details>

<summary>35. Explain the re-rendering behavior in React Native and how to optimize components to prevent unnecessary re-renders.</summary>

- Avoid anonymous functions and inline objects in `render`.
- Use `React.memo` or `useMemo`.

</details>

</br>

<details>

<summary>36. How would you approach migrating a large legacy React Native codebase to the latest version?</summary>

- Upgrade dependencies incrementally.
- Use the `react-native upgrade` command.
- Test thoroughly after each update.

</details>

</br>

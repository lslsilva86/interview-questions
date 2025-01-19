<details> 
<summary>1. Difference between handling events in React and vanilla JavaScript.</summary>

In React, event handling is done using a `SyntheticEvent` system, an abstraction that provides a consistent API across all browsers. Unlike vanilla JavaScript, where event handlers attach directly to DOM elements, React uses event delegation by attaching a single event listener to the root element and propagating events as needed. React also uses JSX for attaching handlers, e.g., `onClick={handleClick}` instead of `onclick="handleClick()"`.

</details>  
</br>

<details> 
<summary>2. What are Synthetic Events in React?</summary>

Synthetic Events are wrappers around native events that normalize behavior across browsers. These events are pooled for performance, meaning the event object is reused. Accessing the event asynchronously requires calling `event.persist()` to prevent it from being recycled.

</details>  
</br>

<details> 
<summary>3. React compatibility across browsers.</summary>

React ensures compatibility by using its SyntheticEvent abstraction, which normalizes event properties (e.g., `target`, `preventDefault`) and behavior across browsers. This helps developers write consistent code without worrying about browser-specific issues.

</details>  
</br>

<details> 
<summary>4. Handling high-frequency events (e.g., scroll, mousemove).</summary>

Use **throttling** (limits execution to a fixed interval) or **debouncing** (delays execution until a certain time has passed) to optimize performance. For example, you can use libraries like Lodash (`_.throttle`, `_.debounce`) or custom implementations to control the event handler execution frequency.

</details>  
</br>

<details> 
<summary>5. Event propagation (bubbling and capturing) in React.</summary>

By default, events bubble up in React. You can stop propagation using `event.stopPropagation()` and control capturing by setting `onEventCapture`. React’s synthetic events still follow the W3C model of bubbling and capturing.

</details>  
</br>

<details> 
<summary>6. React’s event delegation mechanism.</summary>

React attaches a single event listener to the root of the DOM tree. All events are intercepted here and delegated to the appropriate React components. This improves performance and memory usage compared to attaching individual listeners to each DOM node.

</details>  
</br>

<details> 
<summary>7. Common pitfalls in event handling.</summary>

- Forgetting to call `event.preventDefault()` or `event.stopPropagation()` where needed.
- Not using `event.persist()` when accessing an event asynchronously, leading to errors due to recycled event objects.

</details>  
</br>

<details> 
<summary>8. Different ways to bind event handlers.</summary>

- **Constructor binding:**
  ```javascript
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  ```
- **Arrow function in class fields:**
  ```javascript
  handleClick = () => {
    console.log(this);
  };
  ```
- **Arrow function in render (not recommended):**
  ```javascript
  <button onClick={() => this.handleClick()} />
  ```

</details>  
</br>

<details> 
<summary>9. Why bind event handlers in class components?</summary>

In JavaScript, `this` depends on how the function is called. Without explicit binding, `this` inside a class method may be `undefined` when passed as an event handler because `this` isn’t automatically bound in ES6 classes.

</details>  
</br>

<details> 
<summary>10. Arrow functions solving this binding issue.</summary>

Arrow functions don’t have their own `this`. Instead, they inherit the `this` value from their lexical scope. When defined as class fields, they automatically bind `this` to the class instance.

</details>  
</br>

<details> 
<summary>11. Efficient approach for binding handlers.</summary>

Using arrow functions in class fields is generally the most efficient and clean approach. Constructor binding works well but adds boilerplate code. Avoid binding in render as it creates a new function on every render, affecting performance.

</details>  
</br>

<details> 
<summary>12. Problems with binding in the render method.</summary>

Binding in render creates a new function instance on every render, causing unnecessary re-renders in child components if passed as props.

</details>  
</br>

<details> 
<summary>13. Passing handlers to children without rebinding.</summary>

Use pre-bound methods or arrow functions in the parent class to pass event handlers without rebinding.

```javascript
<ChildComponent onClick={this.handleClick} />
```

</details>  
</br>

<details> 
<summary>14. Handling this in functional components.</summary>

Functional components don’t need `this` binding. Instead, use hooks like `useState` and `useEffect`.

</details>  
</br>

<details> 
<summary>15. Debugging memory leaks with event handlers.</summary>

Ensure all listeners are removed in `componentWillUnmount`.

```javascript
componentDidMount() {
  window.addEventListener('resize', this.handleResize);
}

componentWillUnmount() {
  window.removeEventListener('resize', this.handleResize);
}
```

</details>  
</br>

<details> 
<summary>16. Using bind to pass arguments.</summary>

```javascript
handleClick(arg) {
  console.log(arg);
}
<button onClick={this.handleClick.bind(this, 'argument')} />
```

</details>  
</br>

<details> 
<summary>17. Parent-child event handling in class components.</summary>

Pass a bound parent method as a prop to the child, and call it inside the child.

```javascript
handleParentClick = () => {
  console.log("Parent clicked");
};
<Child onClick={this.handleParentClick} />;
```

</details>  
</br>

<details> 
<summary>18. Button logging timestamp.</summary>

```javascript
class MyComponent extends React.Component {
  handleClick = () => {
    console.log(new Date().toISOString());
  };

  render() {
    return <button onClick={this.handleClick}>Log Timestamp</button>;
  }
}
```

</details>  
</br>

<details> 
<summary>19. Keyboard event handling.</summary>

```javascript
componentDidMount() {
  window.addEventListener('keydown', this.handleKeyDown);
}

componentWillUnmount() {
  window.removeEventListener('keydown', this.handleKeyDown);
}

handleKeyDown = (e) => {
  if (e.key === 'Enter') {
    console.log('Enter key pressed');
  }
};
```

</details>  
</br>

<details> 
<summary>20. Fixing setInterval memory leak.</summary>

```javascript
componentDidMount() {
  this.intervalId = setInterval(() => {
    this.setState({ time: Date.now() });
  }, 1000);
}

componentWillUnmount() {
  clearInterval(this.intervalId);
}
```

</details>  
</br>

<details> 
<summary>21. Propagating events in parent-child.</summary>

```javascript
class Parent extends React.Component {
  handleChildClick = () => {
    console.log("Child clicked!");
  };

  render() {
    return <Child onClick={this.handleChildClick} />;
  }
}

const Child = ({ onClick }) => <button onClick={onClick}>Click me!</button>;
```

</details>  
</br>

<details> 
<summary>22. Reusable HOC for event binding.</summary>

```javascript
const withEventHandlers = (WrappedComponent) => {
  return class extends React.Component {
    handleEvent = () => {
      console.log("Event handled");
    };

    render() {
      return <WrappedComponent {...this.props} onEvent={this.handleEvent} />;
    }
  };
};
```

</details>  
</br>

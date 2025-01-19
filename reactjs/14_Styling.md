<details>

<summary>1. How do CSS Modules work under the hood, and how do they ensure scoped styles?</summary>

CSS Modules work by generating unique class names for each CSS rule defined in a module file. When you import styles from a module (`import styles from './styles.module.css'`), the class names are locally scoped to the component. Under the hood, tools like webpack process the CSS file and append a hash or unique identifier to class names, ensuring no name conflicts in the global scope.

Example:

```css
/* styles.module.css */
.button {
  color: red;
}
```

```javascript
import styles from "./styles.module.css";
console.log(styles.button); // Outputs something like "button__3dfg4"
```

</details>  
</br>

<details>

<summary>2. How would you handle global styles in a project using CSS Modules?</summary>

Global styles can be handled in CSS Modules using the `:global` selector, which ensures that certain styles are not scoped. These styles will behave like regular global CSS.

Example:

```css
/* styles.module.css */
:global(body) {
  margin: 0;
  font-family: Arial, sans-serif;
}
```

Alternatively, global styles can be maintained in a separate CSS file (e.g., `global.css`) and imported into the root component.

</details>  
</br>

<details>

<summary>3. What are the advantages and disadvantages of using CSS Modules compared to traditional CSS or other styling methods like Styled-components?</summary>

**Advantages:**

- Scoped styles prevent class name conflicts.
- Simpler to adopt if you’re transitioning from traditional CSS.
- Static CSS files mean better performance (fewer runtime computations).
- Works well with existing CSS preprocessors like Sass.

**Disadvantages:**

- Less dynamic compared to CSS-in-JS solutions like Styled-components.
- Managing global styles can be cumbersome.
- Requires a build step to process the CSS.

</details>  
</br>

<details>

<summary>4. Explain the difference between using the `:local` and `:global` selectors in CSS Modules. Provide an example of when to use each.</summary>

By default, all styles in CSS Modules are locally scoped. The `:local` selector explicitly marks styles as local, while `:global` marks them as global.

**Example:**

```css
/* styles.module.css */
:local(.button) {
  color: red; /* Scoped to the component */
}

:global(.global-button) {
  color: blue; /* Affects the entire application */
}
```

**Use Case:**

- `:local` for component-specific styles.
- `:global` for applying global resets or third-party library integrations.

</details>  
</br>

<details>

<summary>5. How would you configure a webpack setup to support CSS Modules?</summary>

You need to configure the `css-loader` to enable CSS Modules. Here's an example configuration:

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          "style-loader",
          {
            loader: "css-loader",
            options: {
              modules: {
                localIdentName: "[name]__[local]___[hash:base64:5]",
              },
            },
          },
        ],
      },
    ],
  },
};
```

</details>  
</br>

<details>

<summary>6. How does Styled-components achieve dynamic styling in React?</summary>

Styled-components use tagged template literals to create styled elements. Dynamic styling is achieved by embedding JavaScript expressions in the template literals, allowing for conditional or computed styles based on component props or state.

Example:

```javascript
import styled from "styled-components";

const Button = styled.button`
  color: ${(props) => (props.primary ? "white" : "black")};
  background: ${(props) => (props.primary ? "blue" : "transparent")};
`;

<Button primary>Primary Button</Button>;
<Button>Default Button</Button>;
```

</details>  
</br>

<details>

<summary>7. What are some performance concerns with Styled-components, and how would you mitigate them in a large application?</summary>

**Concerns:**

- Runtime performance due to generating class names dynamically.
- Increased bundle size with heavy usage.
- Difficulty in caching styles since they are embedded in JavaScript.

**Mitigations:**

- Use the `babel-plugin-styled-components` to optimize performance (e.g., minification, dead code elimination).
- Leverage server-side rendering (SSR) to pre-render styles.
- Avoid excessive dynamic styles by reusing styled components or static styles where possible.

</details>  
</br>

<details>

<summary>8. How do Styled-components handle theming? Write a small example to demonstrate the use of themes.</summary>

Styled-components provide a `<ThemeProvider>` component to pass theme objects down the component tree. You can access the theme using the `props.theme` object.

Example:

```javascript
import styled, { ThemeProvider } from "styled-components";

const theme = {
  primary: "blue",
  secondary: "green",
};

const Button = styled.button`
  background: ${(props) => props.theme.primary};
  color: white;
`;

<ThemeProvider theme={theme}>
  <Button>Theme Button</Button>
</ThemeProvider>;
```

</details>  
</br>

<details>

<summary>9. Explain how Styled-components generate unique class names and why this is important for avoiding conflicts.</summary>

Styled-components generate unique class names by creating a hash based on the component name, styles, and content. This ensures that the styles are scoped to the component and do not clash with other components or global styles.

Example:

```javascript
const Button = styled.button`
  color: red;
`;
```

Generated class name: `.Button-sc-1a2b3c4 { color: red; }`

**Importance:**

- Avoids unintentional style overrides in large applications.
- Makes debugging easier by associating styles with components explicitly.

</details>  
</br>

<details>

<summary>10. What is the difference between extending a Styled-component and creating a new one? When should you use one approach over the other?</summary>

**Extending a Styled-component:**  
You can extend an existing Styled-component by creating a new component that inherits its styles, then adding or overriding styles.

Example:

```javascript
const Button = styled.button`
  background: blue;
  color: white;
`;

const LargeButton = styled(Button)`
  padding: 20px;
  font-size: 18px;
`;
```

**Creating a new Styled-component:**  
You define an entirely new Styled-component with its own styles, without inheriting from an existing one.

**When to Use:**

- **Extend** when you want to build upon an existing component’s styles and maintain a consistent design system.
- **Create** a new component when the styles are completely unrelated or when reuse isn't required.

</details>  
</br>

<details>

<summary>11. Discuss the impact of server-side rendering (SSR) on Styled-components. How do you ensure proper hydration of styles on the client?</summary>

Styled-components support SSR by generating the required CSS on the server and injecting it into the HTML sent to the client. This ensures styles are available immediately without waiting for JavaScript.

To ensure proper hydration of styles on the client, use the `ServerStyleSheet` API.

Example:

```javascript
import { ServerStyleSheet } from "styled-components";

const sheet = new ServerStyleSheet();
const html = renderToString(sheet.collectStyles(<App />));
const styles = sheet.getStyleTags(); // Generate the <style> tags
```

Without proper SSR handling, styles might flicker or mismatch during the initial load.

</details>  
</br>

<details>

<summary>12. What are the key differences between Emotion and Styled-components, and how do you decide which one to use?</summary>

**Key Differences:**

1. **Performance:** Emotion generally has better performance due to smaller runtime overhead.
2. **Styling Options:** Emotion supports both object and string styles (`css` prop and `styled` API), whereas Styled-components uses tagged template literals exclusively.
3. **Theming:** Both support theming, but Emotion's theming is more lightweight.
4. **Bundle Size:** Emotion has a smaller bundle size compared to Styled-components.

**When to Use:**

- Use **Emotion** for flexibility and performance-critical applications.
- Use **Styled-components** if you prefer template literals and a focus on developer ergonomics.

</details>  
</br>

<details>

<summary>13. Explain how Emotion's `css` function works and how it differs from `styled`. Provide examples.</summary>

The `css` function in Emotion allows you to define styles as objects or strings, and then apply them directly using the `className` prop.

Example with `css`:

```javascript
import { css } from "@emotion/react";

const buttonStyle = css`
  color: white;
  background: blue;
`;

<button className={buttonStyle}>Button</button>;
```

The `styled` function creates a Styled-component, similar to Styled-components.

Example with `styled`:

```javascript
import styled from "@emotion/styled";

const Button = styled.button`
  color: white;
  background: blue;
`;

<Button>Button</Button>;
```

**Difference:**

- `css` is more flexible, allowing inline application of styles.
- `styled` is used for creating reusable, styled components.

</details>  
</br>

<details>

<summary>14. How would you use Emotion's `Global` component to apply global styles to an application?</summary>

Emotion's `Global` component is used to define global styles that apply across the entire application.

Example:

```javascript
import { Global, css } from "@emotion/react";

<Global
  styles={css`
    body {
      margin: 0;
      font-family: Arial, sans-serif;
    }
  `}
/>;
```

This applies the defined styles globally, similar to a global CSS file.

</details>  
</br>

<details>

<summary>15. What is the difference between Emotion’s `css` and `keyframes` utilities? Write examples to showcase their usage.</summary>

- **`css`:** Used to define regular styles as objects or strings.
- **`keyframes`:** Used to define animations.

Examples:

```javascript
import { css, keyframes } from "@emotion/react";

// css usage
const buttonStyle = css`
  color: white;
  background: blue;
`;

// keyframes usage
const fadeIn = keyframes`
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
`;

const animationStyle = css`
  animation: ${fadeIn} 2s ease-in-out;
`;
```

</details>  
</br>

<details>

<summary>16. What are the trade-offs between using Emotion in object styles (`css` prop) versus template literal styles (`styled`)?</summary>

**Object Styles (`css` prop):**

- **Advantages:**
  - Great for dynamic styles and conditional logic.
  - Can be applied directly to the `className` prop without creating a separate component.
- **Disadvantages:**
  - Can become verbose for large styles.

**Template Literals (`styled`):**

- **Advantages:**
  - More readable and concise for reusable components.
  - Supports a more declarative API.
- **Disadvantages:**
  - Less flexible for one-off or highly dynamic styles.

Use **object styles** for inline, dynamic cases and **template literals** for reusable components.

</details>  
</br>

<details>

<summary>17. How does Emotion integrate with CSS-in-JS and TypeScript? Provide a code example demonstrating type safety in styled components.</summary>

Emotion integrates with TypeScript by providing types for props in styled components.

Example:

```typescript
import styled from "@emotion/styled";

type ButtonProps = {
  primary?: boolean;
};

const Button = styled.button<ButtonProps>`
  background: ${(props) => (props.primary ? "blue" : "gray")};
  color: white;
`;

<Button primary>Primary Button</Button>;
```

This ensures type safety for `primary`, preventing invalid prop usage.

</details>  
</br>

<details>

<summary>18. What are the main limitations of inline styles in React, and how can they be overcome?</summary>

**Limitations:**

- No support for pseudo-classes (`:hover`, `:focus`) or media queries.
- Vendor prefixes must be handled manually.
- Styles cannot be shared or reused easily.

**Solutions:**

- Use libraries like Radium or Emotion for pseudo-classes and media queries.
- Avoid inline styles for reusable components; prefer CSS-in-JS or CSS Modules.

</details>  
</br>

<details>

<summary>19. How would you handle dynamic pseudo-classes (e.g., `:hover`, `:focus`) and media queries with inline styles?</summary>

Dynamic pseudo-classes and media queries are not directly supported with inline styles. Use a library like **Radium** or **Emotion** to handle these:

Example with Emotion:

```javascript
import styled from "@emotion/styled";

const Button = styled.button`
  &:hover {
    color: red;
  }

  @media (max-width: 600px) {
    font-size: 14px;
  }
`;
```

</details>  
</br>

<details>

<summary>20. Explain the performance implications of using inline styles in React compared to other styling methods.</summary>

**Performance Implications:**

- Inline styles bypass the CSSOM, reducing the overhead of selector matching.
- However, they can increase memory usage as styles are applied directly to each DOM node.
- Lack of caching for shared styles leads to duplication and inefficiency.
- Debugging inline styles is harder compared to other methods.

Prefer CSS Modules or CSS-in-JS for shared and reusable styles.

</details>  
</br>

<details>

<summary>21. How would you implement theming with inline styles in a large application?</summary>

You can use a context provider to pass theme information down the component tree, then dynamically apply inline styles based on the theme.

Example:

```javascript
const ThemeContext = React.createContext({ color: "blue" });

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return <button style={{ color: theme.color }}>Themed Button</button>;
}
```

</details>  
</br>

<details>

<summary>22. Why are CSS properties with vendor prefixes problematic in inline styles, and how do you handle them?</summary>

**Problem:** Inline styles require camelCase for CSS properties, but vendor prefixes are not automatically handled. For example, `-webkit-transform` must be written as `WebkitTransform`.

**Solution:** Use libraries like **Autoprefixer** or CSS-in-JS solutions (e.g., Emotion, Styled-components), which handle prefixes automatically.

</details>  
</br>

<details>

<summary>23. How would you migrate an existing project from CSS Modules to Styled-components or Emotion? What challenges would you anticipate?</summary>

**Steps for Migration:**

1. Identify reusable styles and convert them into Styled-components or Emotion components.
2. Replace CSS Module imports with Styled-component or Emotion imports.
3. Migrate global styles using `createGlobalStyle` (Styled-components) or `Global` (Emotion).
4. Test components thoroughly after migration.

**Challenges:**

- Rewriting complex styles.
- Ensuring consistent theme usage.
- Debugging potential style conflicts during the transition.

</details>  
</br>

<details>

<summary>24. Discuss a scenario where CSS Modules would be more beneficial than Styled-components or Emotion, and vice versa.</summary>

**CSS Modules:**

- Best for projects transitioning from traditional CSS or using CSS preprocessors.
- Works well when a build pipeline already supports static CSS files.

**Styled-components/Emotion:**

- More suitable for highly dynamic applications requiring prop-based styling.
- Ideal for large React projects where scoped, reusable components are necessary.

</details>  
</br>

<details>

<summary>25. Write a component that uses CSS Modules, Styled-components, and Emotion for the same styling purpose. Compare the implementation, performance, and ease of use.</summary>

Example omitted for brevity but includes styling a button component using all three methods.

**Comparison:**

- **CSS Modules:** Easier for static styles but lacks runtime flexibility.
- **Styled-components/Emotion:** Better for dynamic styles and reusable components, but runtime cost is higher.

</details>  
</br>

<details>

<summary>26. How would you debug conflicting styles when using multiple styling techniques in a project?</summary>

**Debugging Approach:**

1. Use browser DevTools to inspect styles and determine the source of the conflict.
2. Identify and remove conflicting global styles or overrides.
3. Refactor styles to ensure better scoping (e.g., modularizing styles).
4. Use naming conventions to distinguish between CSS Modules and CSS-in-JS.

</details>  
</br>

<details>

<summary>27. Explain how to integrate a third-party UI library with global styles into a project using Styled-components or Emotion.</summary>

Wrap the application in a `ThemeProvider` to inject global styles while customizing the library’s styles using Styled-components or Emotion.

Example with Emotion:

```javascript
import { Global, css } from "@emotion/react";

<Global
  styles={css`
    body {
      margin: 0;
    }
    .ui-library-class {
      background: red;
    }
  `}
/>;
```

</details>  
</br>

<details>

<summary>1. Can you explain the difference between **WCAG Levels (A, AA, AAA)**? Why is AA compliance typically the standard for most projects?</summary>

- **WCAG Levels:**

  - **Level A**: The minimum level of accessibility. Addresses the most basic barriers that prevent users from accessing content.
  - **Level AA**: Builds on Level A by focusing on improving accessibility for a wider range of users, including those with moderate disabilities. It includes guidelines for color contrast, navigation, and readability.
  - **Level AAA**: The highest and most strict level, designed to make content accessible to all users. Not always achievable due to the complexity of requirements like avoiding all jargon.

- **Why AA compliance is standard**:
  - It balances feasibility and accessibility.
  - Ensures usability for a broad audience, including people with disabilities.
  - Meets legal requirements in many countries (e.g., ADA, Section 508 in the US).

</details>  
</br>

<details>

<summary>2. What are ARIA roles, and how do they improve accessibility? Provide examples of when using ARIA roles is necessary and when it might be harmful.</summary>

- **ARIA Roles**: Accessible Rich Internet Applications (ARIA) roles define the purpose of elements for assistive technologies like screen readers.

- **How they improve accessibility**:

  - Add context to non-semantic elements (e.g., `<div>` or `<span>`).
  - Help manage complex interactive widgets (e.g., modals, carousels).

- **When necessary**:

  - Custom components (e.g., a custom dropdown implemented using `<div>` elements can use `role="menu"` and `aria-expanded`).

- **When harmful**:
  - Overusing ARIA where semantic HTML suffices (e.g., using `role="button"` on a `<button>` element is redundant and can break functionality).

</details>  
</br>

<details>

<summary>3. How would you handle providing accessible navigation for a single-page application (SPA)?</summary>

- Use semantic HTML: Properly structure headings, landmarks (`<nav>`, `<main>`).
- Ensure dynamic updates are announced: Use `aria-live` regions for content changes.
- Implement skip links: Allow users to skip directly to main content.
- Manage focus: Update focus programmatically using `focus()` when navigating between views.
- Keyboard navigation: Ensure all navigation can be done via the keyboard (e.g., `tab` key).

</details>  
</br>

<details>

<summary>4. What are semantic HTML elements, and why are they critical for accessibility? Provide examples of improper semantic usage and how to fix it.</summary>

- **Semantic HTML elements**: Elements that convey meaning (e.g., `<header>`, `<footer>`, `<main>`, `<article>`).

- **Why critical**:

  - Inform screen readers and assistive technologies about the structure of content.
  - Improve usability by clearly defining the purpose of elements.

- **Examples of improper usage**:
  - Using `<div>` instead of `<button>` for clickable elements. Fix: Replace `<div>` with `<button>` and add proper styling.
  - Using `<h1>` repeatedly without proper hierarchy. Fix: Ensure headings follow logical levels (`<h1>`, `<h2>`, etc.).

</details>  
</br>

<details>

<summary>5. How would you ensure proper color contrast in your UI? What tools or techniques would you use to validate it?</summary>

- **Steps**:

  - Follow WCAG guidelines: Minimum contrast ratio of 4.5:1 for text and 3:1 for large text.
  - Use accessible color palettes.

- **Tools**:
  - WebAIM Contrast Checker.
  - Browser dev tools (Lighthouse or Axe).
  - Design tools like Figma/Sketch with contrast plugins.

</details>  
</br>

<details>

<summary>6. A client wants a visually appealing UI with custom-styled checkboxes and radio buttons. How would you implement these while ensuring they are accessible?</summary>

- **Steps**:
  1. Use `<input type="checkbox">` and `<input type="radio">` for functionality.
  2. Visually hide the native inputs (`opacity: 0; position: absolute;`).
  3. Style the custom UI using labels.
  4. Ensure inputs remain focusable and accessible via the keyboard (`tabindex`).
  5. Add `aria-checked` for dynamic states if required.

</details>  
</br>

<details>

<summary>7. How would you make a complex interactive component, like a carousel, fully accessible?</summary>

- **Steps**:
  1. Use ARIA roles and attributes (`role="region"`, `aria-roledescription="carousel"`, `aria-live`).
  2. Implement keyboard navigation: Allow `arrow keys` for navigation and `tab` key for focusable items.
  3. Announce active slide: Use `aria-label` or visually hidden text to indicate the current slide.
  4. Provide pause/play controls for autoplay.

</details>  
</br>

<details>

<summary>8. Imagine you’re tasked with making a form accessible for screen readers. What steps would you take to ensure this?</summary>

- **Steps**:
  1. Use semantic elements: `<label>` for inputs and associate them with `for` attributes.
  2. Add accessible error messages using `aria-live`.
  3. Provide placeholder text sparingly; always use labels.
  4. Ensure proper focus management for validation errors and next steps.
  5. Use `fieldset` and `<legend>` for grouping related fields.

</details>  
</br>

<details>

<summary>9. A client’s website must support users with motor disabilities. What specific considerations would you recommend or implement?</summary>

- **Considerations**:
  1. **Keyboard accessibility**: Ensure all functionality is usable without a mouse.
  2. **Focus management**: Provide clear and visible focus indicators.
  3. **Avoid time-sensitive interactions**: Allow users extra time to interact with content.
  4. **Touch target sizes**: Ensure buttons and links are large enough (at least 48x48 pixels).
  5. **Voice recognition support**: Use semantic HTML and proper labels for compatibility with voice control tools.

</details>  
</br>

<details>

<summary>10. How would you test for accessibility in a project with multiple dynamic modals and ensure they do not interfere with screen readers?</summary>

- **Steps**:
  1. Ensure only one modal is active at a time and other content is hidden using `aria-hidden="true"` on the main page content.
  2. Add focus trapping inside the modal so that keyboard navigation does not leave the modal.
  3. Use `aria-labelledby` and `aria-describedby` to associate the modal's title and content with its container.
  4. Test with a screen reader to ensure content is announced when the modal opens.
  5. Validate using tools like Axe, Lighthouse, or manual testing with screen readers and keyboard navigation.

</details>  
</br>

<details>

<summary>11. Tell me about a time when you discovered an accessibility issue late in the development process. How did you address it?</summary>

- **Example Response**:
  - During a project, we realized our custom dropdowns were inaccessible for keyboard and screen reader users.
  - **Steps taken**:
    - Audited the code and added ARIA roles like `aria-expanded`, `aria-haspopup`, and `aria-controls`.
    - Implemented keyboard navigation using `onKeyDown` handlers.
    - Used `aria-live` to notify changes dynamically.
    - Retested the dropdowns with screen readers (NVDA and VoiceOver) and keyboard-only navigation to ensure compliance.

</details>  
</br>

<details>

<summary>12. Have you ever worked on a project where accessibility was deprioritized? How did you advocate for its importance?</summary>

- **Example Response**:
  - On a tight-deadline project, accessibility was initially deprioritized. I highlighted:
    - Legal implications of non-compliance (e.g., lawsuits under ADA).
    - Business benefits: broader audience reach, better SEO, and usability for all users.
    - I proposed incremental changes (e.g., semantic HTML first, ARIA roles later) and demonstrated the ease of integration with existing tools.

</details>  
</br>

<details>

<summary>13. What is the purpose of `react-aria`? How does it differ from using raw ARIA attributes directly in your React components?</summary>

- **Purpose of `react-aria`**:

  - Provides a collection of React hooks for building accessible UI components.
  - Handles ARIA roles, focus management, and keyboard navigation automatically.

- **Difference**:
  - **Raw ARIA attributes** require manual handling, which increases the risk of misconfiguration.
  - **`react-aria`** encapsulates best practices, reduces boilerplate, and ensures consistency across components.

</details>  
</br>

<details>

<summary>14. Explain the key features of `react-a11y`. How does it help in identifying and addressing accessibility issues?</summary>

- **Key Features**:

  - Lints React code to identify accessibility issues like missing ARIA roles or improper semantics.
  - Provides real-time feedback during development.

- **How it helps**:
  - Highlights potential problems early in development, reducing the cost of fixing them later.
  - Encourages adherence to WCAG guidelines.

</details>  
</br>

<details>

<summary>15. How does `react-aria` handle focus management in components? Provide a specific example where focus management improves accessibility.</summary>

- **Focus Management in `react-aria`**:

  - Automatically moves focus to relevant elements during dynamic updates (e.g., when opening modals).
  - Provides hooks like `useFocusRing` and `useFocusManager` for custom focus behavior.

- **Example**:
  - In a dialog, `react-aria` ensures focus moves to the first interactive element and traps focus within the dialog. When closed, focus returns to the triggering element.

</details>  
</br>

<details>

<summary>16. What are the limitations of `react-a11y`? Are there scenarios where you would choose other tools over it?</summary>

- **Limitations**:

  - Limited to development-time linting; cannot detect runtime issues.
  - May not cover all edge cases for dynamic content.

- **Alternatives**:
  - Use Axe or Lighthouse for runtime testing and more comprehensive issue detection.
  - Use `react-aria` for building accessible components from scratch.

</details>  
</br>

<details>

<summary>17. Can you describe how `useVisuallyHidden` from `react-aria` works and when you would use it?</summary>

- **How it works**:

  - Provides a style that visually hides an element but keeps it accessible to screen readers.

- **Use Case**:
  - For providing additional context to screen readers, such as instructions or hidden labels.

</details>  
</br>

<details>

<summary>18. Using `react-aria`, how would you create an accessible dropdown menu? Explain how focus and keyboard navigation are managed.</summary>

- **Steps**:
  1. Use `useListBox` for the dropdown structure and `useOption` for items.
  2. Manage focus with `useFocusManager` to move between options using arrow keys.
  3. Use `aria-expanded` and `aria-labelledby` to associate the menu with the triggering button.
  4. Handle keyboard shortcuts like `Enter` to select items.

</details>  
</br>

<details>

<summary>19. Write a code snippet to implement a custom toggle button with accessible states using `react-aria`.</summary>

```jsx
import React, { useState } from "react";
import { useToggleButton } from "react-aria";

function ToggleButton() {
  const [isSelected, setSelected] = useState(false);
  const { buttonProps } = useToggleButton(
    {
      isPressed: isSelected,
      onPress: () => setSelected(!isSelected),
    },
    null
  );

  return (
    <button {...buttonProps} aria-pressed={isSelected}>
      {isSelected ? "On" : "Off"}
    </button>
  );
}

export default ToggleButton;
```

</details>  
</br>

<details>

<summary>20. How would you handle accessible error messages in a form using `react-aria` or other tools? Provide an example.</summary>

- **Steps**:

  1. Use `aria-live` to announce error messages.
  2. Associate error messages with inputs using `aria-describedby`.

- **Example**:

```jsx
<label htmlFor="email">Email:</label>
<input id="email" aria-describedby="emailError" />
<div id="emailError" role="alert">
  Please enter a valid email address.
</div>
```

</details>  
</br>

<details>

<summary>21. Explain how you would ensure a custom modal dialog is fully accessible using `react-aria`. What challenges might you encounter?</summary>

- **Steps**:

  1. Use `useDialog` for modal structure and focus management.
  2. Implement focus trapping with `useFocusManager`.
  3. Ensure ARIA attributes like `aria-labelledby` and `aria-describedby` are properly set.

- **Challenges**:
  - Managing focus for dynamically rendered modals.
  - Ensuring proper keyboard navigation.

</details>  
</br>

<details>

<summary>22. How do you test components built with `react-aria` to ensure they comply with WCAG standards?</summary>

- **Testing Steps**:
  1. Use automated tools like Axe or Lighthouse for quick compliance checks.
  2. Manually test with screen readers (e.g., NVDA, VoiceOver).
  3. Validate keyboard navigation.

</details>  
</br>

<details>

<summary>23. What are the most common accessibility issues that `react-a11y` highlights? How do you resolve them?</summary>

- **Common Issues**:

  - Missing ARIA roles or attributes.
  - Improper semantics.
  - Missing alternative text for images.

- **Resolution**:
  - Address issues during development by adhering to semantic HTML and ARIA best practices.

</details>  
</br>

<details>

<summary>24. If a screen reader is not correctly announcing the content of a dynamically updated component, how would you debug and fix the issue using `react-aria`?</summary>

- **Steps**:
  1. Ensure `aria-live` is set on the container for dynamic updates.
  2. Verify ARIA roles and attributes are correctly applied.
  3. Test with multiple screen readers to identify inconsistencies.

</details>  
</br>

<details>

<summary>25. How do you integrate tools like `react-aria` with testing frameworks (e.g., Jest, RTL) to write unit and integration tests for accessibility?</summary>

- **Steps**:
  1. Use Jest and React Testing Library to simulate user interactions.
  2. Assert accessibility attributes (e.g., `aria-label`, `aria-expanded`).
  3. Use Axe for automated testing during unit test runs.

</details>  
</br>

<details>

<summary>26. Implement an accessible autocomplete component that adheres to the ARIA Authoring Practices. It should support:</summary>

- **Keyboard navigation.**
- **Screen reader announcements.**
- **Focus management.**

```jsx
// Example too detailed for summary; implemented using `react-aria` hooks like `useComboBox`.
```

</details>  
</br>

<details>

<summary>27. You’ve inherited a legacy React application with poor accessibility. What steps would you take to audit the codebase and incrementally improve its accessibility?</summary>

- **Steps**:
  1. Perform an accessibility audit using tools like Axe or Lighthouse.
  2. Address semantic issues (e.g., replacing `<div>` with `<button>` where appropriate).
  3. Incrementally add ARIA roles and attributes.
  4. Implement automated accessibility tests to catch regressions.

</details>  
</br>

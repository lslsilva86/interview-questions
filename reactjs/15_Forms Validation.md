<details>

<summary>1. Explain how Formik optimizes performance compared to handling forms manually in React. How does it reduce the need for multiple `useState` calls?</summary>

Formik optimizes performance by:

- **Centralizing Form State**: Instead of managing individual states for each input, Formik centralizes all form-related state (values, errors, touched fields) in a single `useFormik` hook or `Formik` component. This avoids unnecessary re-renders.
- **Field-Level Updates**: Formik uses its `Field` component or `useField` hook to update only the specific field being changed, preventing the entire form from re-rendering.
- **Debounced Validations**: Formik allows debounced or delayed validation, avoiding validation on every keystroke unless explicitly required.

</details>  
</br>

<details>

<summary>2. Why is Formik often chosen over React's built-in state management for form handling?</summary>

Formik is preferred because:

- **Ease of Validation**: It integrates seamlessly with validation libraries like Yup for schema-based validation.
- **Error Management**: Formik simplifies error tracking and displays for individual fields.
- **Performance**: With features like field-level validation and re-render control, Formik is more efficient for large forms.
- **Reusable Components**: Prebuilt components like `Field`, `ErrorMessage`, and `Formik` simplify repetitive tasks.
- **Scalability**: It simplifies handling nested and dynamic fields, making it easier to scale forms in complex applications.

</details>  
</br>

<details>

<summary>3. Describe how Yup's schema-based validation differs from traditional JavaScript validation methods.</summary>

Yup's schema-based validation differs in these ways:

- **Declarative**: Validation rules are defined as a schema object, making them easy to read, maintain, and reuse.
- **Composability**: Yup allows combining and chaining validations, enabling modular rules.
- **Error Handling**: It provides detailed, structured error messages for each validation failure.
- **Asynchronous Support**: Yup supports asynchronous validation, unlike traditional synchronous methods.
- **Built-In Rules**: It has built-in validators (e.g., `string().email()`, `number().positive()`) that reduce boilerplate code.

</details>  
</br>

<details>

<summary>4. How would you handle conditional validation with Yup? Provide an example.</summary>

You can handle conditional validation in Yup using `.when` or `.test`. Here's an example:

```javascript
import * as Yup from "yup";

const schema = Yup.object().shape({
  age: Yup.number().required("Age is required"),
  guardianName: Yup.string().when("age", {
    is: (age) => age < 18,
    then: Yup.string().required("Guardian name is required"),
    otherwise: Yup.string().notRequired(),
  }),
});

// Example data validation
schema
  .validate({ age: 16, guardianName: "" })
  .catch((err) => console.log(err.errors));
// Output: ['Guardian name is required']
```

</details>  
</br>

<details>

<summary>5. How do you handle dynamic form fields (e.g., adding/removing fields) in Formik? What challenges might arise with validation?</summary>

**Handling:**

- Use Formik’s `FieldArray` component to manage dynamic fields.
- Dynamically update the `initialValues` and `validationSchema` to reflect the added/removed fields.

Example:

```javascript
import { FieldArray, Formik, Form, Field } from "formik";
import * as Yup from "yup";

const DynamicForm = () => (
  <Formik
    initialValues={{ names: [""] }}
    validationSchema={Yup.object({
      names: Yup.array().of(Yup.string().required("Name is required")),
    })}
    onSubmit={(values) => console.log(values)}
  >
    {({ values }) => (
      <Form>
        <FieldArray
          name="names"
          render={(arrayHelpers) => (
            <div>
              {values.names.map((_, index) => (
                <div key={index}>
                  <Field name={`names[${index}]`} />
                  <button
                    type="button"
                    onClick={() => arrayHelpers.remove(index)}
                  >
                    Remove
                  </button>
                </div>
              ))}
              <button type="button" onClick={() => arrayHelpers.push("")}>
                Add Name
              </button>
            </div>
          )}
        />
        <button type="submit">Submit</button>
      </Form>
    )}
  </Formik>
);
```

**Challenges:**

- Updating validation dynamically for added/removed fields.
- Managing focus and user experience for added fields.
- Performance issues with large arrays of fields.

</details>  
</br>

<details>

<summary>6. How does Formik handle validation strategies, such as validation on blur, change, or submit? What are the trade-offs between these modes?</summary>

Formik offers three validation strategies:

1. **On Blur** (`validateOnBlur`): Validates fields when the user leaves the input field.

   - **Pros**: Reduces validation calls; user-friendly for simple forms.
   - **Cons**: Errors are delayed, and users might miss errors until they blur the field.

2. **On Change** (`validateOnChange`): Validates fields as the user types.

   - **Pros**: Real-time feedback improves the user experience for small forms.
   - **Cons**: Performance issues in large or complex forms.

3. **On Submit** (`validateOnSubmit`): Validates fields only when the form is submitted.
   - **Pros**: Ideal for forms where immediate feedback isn’t necessary.
   - **Cons**: Users might find it frustrating to see multiple errors only after submitting.

</details>  
</br>

<details>

<summary>7. How do you integrate third-party components (e.g., custom dropdowns or date pickers) into Formik and ensure proper validation?</summary>

Formik allows you to wrap third-party components using its `Field` or `useField` API. Example with a custom dropdown:

```javascript
const CustomDropdown = ({ field, form, ...props }) => (
  <select {...field} {...props}>
    <option value="option1">Option 1</option>
    <option value="option2">Option 2</option>
  </select>
);

const FormWithDropdown = () => (
  <Formik
    initialValues={{ dropdown: "" }}
    validationSchema={Yup.object({
      dropdown: Yup.string().required("Selection is required"),
    })}
    onSubmit={(values) => console.log(values)}
  >
    <Form>
      <Field name="dropdown" component={CustomDropdown} />
      <ErrorMessage name="dropdown" />
      <button type="submit">Submit</button>
    </Form>
  </Formik>
);
```

**Steps for validation:**

1. Use the `field` prop for value and change handling.
2. Ensure third-party events trigger Formik's `setFieldValue`.

</details>  
</br>

<details>

<summary>8. What strategies can you use in Formik to manage and display validation errors efficiently, especially in large forms with multiple sections?</summary>

**Strategies:**

1. **ErrorMessage Component**: Use `ErrorMessage` to display errors for individual fields.
2. **Field-Level Validation**: Validate fields individually using `validate` at the field level to isolate errors.
3. **Section-Level Error Summaries**: Aggregate and display errors for sections instead of individual fields.
4. **Highlight Invalid Fields**: Use `touched` and `errors` to add visual cues (e.g., red borders) to invalid fields.
5. **Debounce Validation**: Debounce validation for real-time feedback without overwhelming the UI.
6. **Custom Error Display**: Use a summary or tooltip to show errors in a centralized or context-aware manner.

Example:

```javascript
const renderErrors = (errors, touched) =>
  Object.keys(errors).map((field) =>
    touched[field] && errors[field] ? <p key={field}>{errors[field]}</p> : null
  );
```

</details>  
</br>

<details>

<summary>9. Write a simple login form using Formik and Yup to validate email and password fields. The email must be valid, and the password must have at least 8 characters.</summary>

```javascript
import React from "react";
import { Formik, Form, Field, ErrorMessage } from "formik";
import * as Yup from "yup";

const LoginForm = () => {
  const validationSchema = Yup.object({
    email: Yup.string().email("Invalid email address").required("Required"),
    password: Yup.string()
      .min(8, "Password must be at least 8 characters")
      .required("Required"),
  });

  return (
    <Formik
      initialValues={{ email: "", password: "" }}
      validationSchema={validationSchema}
      onSubmit={(values) => console.log(values)}
    >
      {() => (
        <Form>
          <div>
            <label htmlFor="email">Email</label>
            <Field type="email" name="email" />
            <ErrorMessage name="email" />
          </div>
          <div>
            <label htmlFor="password">Password</label>
            <Field type="password" name="password" />
            <ErrorMessage name="password" />
          </div>
          <button type="submit">Submit</button>
        </Form>
      )}
    </Formik>
  );
};

export default LoginForm;
```

</details>  
</br>

<details>

<summary>10. Implement a custom validation function in Yup for a password field that checks for at least one uppercase letter, one number, and one special character.</summary>

```javascript
import * as Yup from "yup";

const passwordValidationSchema = Yup.object().shape({
  password: Yup.string()
    .required("Password is required")
    .test(
      "is-strong-password",
      "Password must contain at least one uppercase letter, one number, and one special character",
      (value) =>
        /[A-Z]/.test(value) && /[0-9]/.test(value) && /[@$!%*?&#]/.test(value)
    ),
});
```

</details>  
</br>

<details>

<summary>11. Create a form where the number of fields is determined by user input (e.g., a dynamic list of names). Use Formik and Yup to ensure all fields are non-empty.</summary>

```javascript
import { Formik, Form, FieldArray, Field } from "formik";
import * as Yup from "yup";

const DynamicFieldsForm = () => {
  const validationSchema = Yup.object({
    names: Yup.array().of(Yup.string().required("Name is required")),
  });

  return (
    <Formik
      initialValues={{ names: [""] }}
      validationSchema={validationSchema}
      onSubmit={(values) => console.log(values)}
    >
      {({ values }) => (
        <Form>
          <FieldArray name="names">
            {({ push, remove }) => (
              <div>
                {values.names.map((_, index) => (
                  <div key={index}>
                    <Field name={`names[${index}]`} placeholder="Enter name" />
                    <button type="button" onClick={() => remove(index)}>
                      Remove
                    </button>
                  </div>
                ))}
                <button type="button" onClick={() => push("")}>
                  Add Name
                </button>
              </div>
            )}
          </FieldArray>
          <button type="submit">Submit</button>
        </Form>
      )}
    </Formik>
  );
};

export default DynamicFieldsForm;
```

</details>  
</br>

<details>

<summary>12. Build a form with fields "Age" and "Guardian Name." If age is less than 18, "Guardian Name" becomes a required field. Use Formik and Yup to implement this.</summary>

```javascript
import React from "react";
import { Formik, Form, Field, ErrorMessage } from "formik";
import * as Yup from "yup";

const ConditionalValidationForm = () => {
  const validationSchema = Yup.object().shape({
    age: Yup.number().required("Age is required").positive().integer(),
    guardianName: Yup.string().when("age", {
      is: (age) => age < 18,
      then: Yup.string().required("Guardian name is required"),
      otherwise: Yup.string().notRequired(),
    }),
  });

  return (
    <Formik
      initialValues={{ age: "", guardianName: "" }}
      validationSchema={validationSchema}
      onSubmit={(values) => console.log(values)}
    >
      <Form>
        <div>
          <label>Age</label>
          <Field name="age" type="number" />
          <ErrorMessage name="age" />
        </div>
        <div>
          <label>Guardian Name</label>
          <Field name="guardianName" type="text" />
          <ErrorMessage name="guardianName" />
        </div>
        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
};

export default ConditionalValidationForm;
```

</details>  
</br>

<details>

<summary>13. Write a form with a username field where you check the availability of the username through an API. Use Formik’s async validation for this.</summary>

```javascript
import React from "react";
import { Formik, Form, Field, ErrorMessage } from "formik";
import * as Yup from "yup";

const checkUsernameAvailability = async (username) => {
  // Mock API request
  const availableUsernames = ["john_doe", "jane_doe"];
  return new Promise((resolve) => {
    setTimeout(() => resolve(!availableUsernames.includes(username)), 500);
  });
};

const AsyncValidationForm = () => {
  const validationSchema = Yup.object({
    username: Yup.string()
      .required("Username is required")
      .test("is-available", "Username is not available", async (value) => {
        if (!value) return false;
        return await checkUsernameAvailability(value);
      }),
  });

  return (
    <Formik
      initialValues={{ username: "" }}
      validationSchema={validationSchema}
      onSubmit={(values) => console.log(values)}
    >
      <Form>
        <div>
          <label>Username</label>
          <Field name="username" />
          <ErrorMessage name="username" />
        </div>
        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
};

export default AsyncValidationForm;
```

</details>  
</br>

<details>

<summary>14. Create a form with "Password" and "Confirm Password" fields. Use Yup to validate that "Confirm Password" matches "Password."</summary>

```javascript
import React from "react";
import { Formik, Form, Field, ErrorMessage } from "formik";
import * as Yup from "yup";

const PasswordValidationForm = () => {
  const validationSchema = Yup.object({
    password: Yup.string().required("Password is required"),
    confirmPassword: Yup.string()
      .oneOf([Yup.ref("password")], "Passwords must match")
      .required("Confirm Password is required"),
  });

  return (
    <Formik
      initialValues={{ password: "", confirmPassword: "" }}
      validationSchema={validationSchema}
      onSubmit={(values) => console.log(values)}
    >
      <Form>
        <div>
          <label>Password</label>
          <Field type="password" name="password" />
          <ErrorMessage name="password" />
        </div>
        <div>
          <label>Confirm Password</label>
          <Field type="password" name="confirmPassword" />
          <ErrorMessage name="confirmPassword" />
        </div>
        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
};

export default PasswordValidationForm;
```

</details>  
</br>

<details>

<summary>15. Implement a custom Formik field component for a date picker (e.g., using `react-datepicker`) and integrate it with Yup validation.</summary>

```javascript
import React from "react";
import { Formik, Form, useField } from "formik";
import * as Yup from "yup";
import DatePicker from "react-datepicker";
import "react-datepicker/dist/react-datepicker.css";

const CustomDatePicker = ({ label, ...props }) => {
  const [field, meta, helpers] = useField(props);

  return (
    <div>
      <label>{label}</label>
      <DatePicker
        {...field}
        selected={field.value}
        onChange={(value) => helpers.setValue(value)}
      />
      {meta.touched && meta.error && <div>{meta.error}</div>}
    </div>
  );
};

const DatePickerForm = () => {
  const validationSchema = Yup.object({
    date: Yup.date().required("Date is required"),
  });

  return (
    <Formik
      initialValues={{ date: null }}
      validationSchema={validationSchema}
      onSubmit={(values) => console.log(values)}
    >
      <Form>
        <CustomDatePicker name="date" label="Pick a date" />
        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
};

export default DatePickerForm;
```

</details>  
</br>

<details>

<summary>16. Build a multi-step form using Formik and Yup. Ensure that each step validates independently before proceeding to the next.</summary>

```javascript
import React, { useState } from "react";
import { Formik, Form, Field, ErrorMessage } from "formik";
import * as Yup from "yup";

const StepOneSchema = Yup.object({
  name: Yup.string().required("Name is required"),
});

const StepTwoSchema = Yup.object({
  email: Yup.string().email("Invalid email").required("Email is required"),
});

const MultiStepForm = () => {
  const [step, setStep] = useState(1);

  const handleNext = () => setStep((prevStep) => prevStep + 1);
  const handleBack = () => setStep((prevStep) => prevStep - 1);

  return (
    <Formik
      initialValues={{ name: "", email: "" }}
      validationSchema={step === 1 ? StepOneSchema : StepTwoSchema}
      onSubmit={(values) => console.log(values)}
    >
      {({ isValid }) => (
        <Form>
          {step === 1 && (
            <>
              <div>
                <label>Name</label>
                <Field name="name" />
                <ErrorMessage name="name" />
              </div>
              <button type="button" onClick={handleNext} disabled={!isValid}>
                Next
              </button>
            </>
          )}
          {step === 2 && (
            <>
              <div>
                <label>Email</label>
                <Field name="email" />
                <ErrorMessage name="email" />
              </div>
              <button type="button" onClick={handleBack}>
                Back
              </button>
              <button type="submit" disabled={!isValid}>
                Submit
              </button>
            </>
          )}
        </Form>
      )}
    </Formik>
  );
};

export default MultiStepForm;
```

</details>  
</br>

<details>

<summary>17. Demonstrate how you can localize validation error messages in Yup for multi-language support.</summary>

```javascript
import * as Yup from "yup";
import { setLocale } from "yup";

setLocale({
  string: {
    required: "Este campo es obligatorio", // Spanish translation for "This field is required"
    email: "Correo electrónico no válido", // Spanish translation for "Invalid email"
  },
  number: {
    min: "El número debe ser mayor o igual a ${min}", // Spanish translation for "The number must be greater than or equal to"
  },
});

const schema = Yup.object({
  email: Yup.string().email().required(),
  age: Yup.number().min(18),
});

// Example validation
schema.validate({ email: "", age: 16 }).catch((err) => console.log(err.errors));
// Output: ['Este campo es obligatorio', 'El número debe ser mayor o igual a 18']
```

</details>  
</br>

<details>

<summary>18. In a large form with 50+ fields, explain and demonstrate how to optimize performance using Formik's `useFormikContext` or other techniques.</summary>

**Strategies for optimization:**

1. **Use `useFormikContext`:** Extract form state and methods to minimize re-renders.
2. **Field-Level Updates:** Use `Field` or `useField` for isolated updates to specific fields.
3. **Lazy Loading:** Dynamically load parts of the form based on user interaction.
4. **Memoization:** Use `React.memo` for components that don’t depend on frequent updates.

**Example:**

```javascript
import React from "react";
import { useFormikContext, Field } from "formik";

const OptimizedField = React.memo(({ name }) => {
  const { values, errors, touched } = useFormikContext();
  return (
    <div>
      <label>{name}</label>
      <Field name={name} />
      {touched[name] && errors[name] && <div>{errors[name]}</div>}
    </div>
  );
});

export default OptimizedField;
```

</details>  
</br>

<details>

<summary>19. You have a form with nested fields (e.g., address with street, city, zip). How would you structure the Yup schema and display errors for each field in a user-friendly way?</summary>

**Yup Schema:**

```javascript
import * as Yup from "yup";

const schema = Yup.object().shape({
  address: Yup.object().shape({
    street: Yup.string().required("Street is required"),
    city: Yup.string().required("City is required"),
    zip: Yup.string().required("Zip code is required"),
  }),
});
```

**Formik Form:**

```javascript
import { Formik, Form, Field, ErrorMessage } from "formik";

const NestedFieldsForm = () => {
  return (
    <Formik
      initialValues={{
        address: { street: "", city: "", zip: "" },
      }}
      validationSchema={schema}
      onSubmit={(values) => console.log(values)}
    >
      <Form>
        <div>
          <label>Street</label>
          <Field name="address.street" />
          <ErrorMessage name="address.street" />
        </div>
        <div>
          <label>City</label>
          <Field name="address.city" />
          <ErrorMessage name="address.city" />
        </div>
        <div>
          <label>Zip</label>
          <Field name="address.zip" />
          <ErrorMessage name="address.zip" />
        </div>
        <button type="submit">Submit</button>
      </Form>
    </Formik>
  );
};

export default NestedFieldsForm;
```

</details>  
</br>

<details>

<summary>20. How would you handle a situation where the validation schema changes based on user input (e.g., a toggle between individual and business user forms)?</summary>

**Solution:**
Use conditional logic to dynamically switch the schema based on user input.

```javascript
import React from "react";
import { Formik, Form, Field } from "formik";
import * as Yup from "yup";

const getSchema = (isBusiness) => {
  return Yup.object().shape({
    name: Yup.string().required("Name is required"),
    ...(isBusiness
      ? { companyName: Yup.string().required("Company name is required") }
      : { age: Yup.number().required("Age is required") }),
  });
};

const DynamicSchemaForm = () => {
  const [isBusiness, setIsBusiness] = React.useState(false);

  return (
    <Formik
      initialValues={{ name: "", companyName: "", age: "" }}
      validationSchema={getSchema(isBusiness)}
      onSubmit={(values) => console.log(values)}
    >
      {({ setFieldValue }) => (
        <Form>
          <div>
            <label>
              <input
                type="checkbox"
                onChange={(e) => {
                  setIsBusiness(e.target.checked);
                  setFieldValue("companyName", "");
                  setFieldValue("age", "");
                }}
              />
              Business User
            </label>
          </div>
          <div>
            <label>Name</label>
            <Field name="name" />
          </div>
          {isBusiness ? (
            <div>
              <label>Company Name</label>
              <Field name="companyName" />
            </div>
          ) : (
            <div>
              <label>Age</label>
              <Field name="age" type="number" />
            </div>
          )}
          <button type="submit">Submit</button>
        </Form>
      )}
    </Formik>
  );
};

export default DynamicSchemaForm;
```

</details>  
</br>

<details>

<summary>21. What challenges do you face when performing real-time validation (on every keystroke) for forms with heavy business logic? How do you mitigate them?</summary>

**Challenges:**

- **Performance Issues:** Frequent validations on every keystroke can slow down the UI, especially in large forms.
- **API Overhead:** If validation depends on server-side APIs, multiple requests may overwhelm the server.
- **User Experience:** Too much feedback can confuse users if errors flicker or update constantly.

**Mitigation Strategies:**

1. **Debounce Validation:** Use a debounce mechanism to delay validation until the user stops typing.
2. **Field-Level Validation:** Isolate validation to individual fields instead of the entire form.
3. **Server-Side Throttling:** Implement throttling or caching for API-dependent validation.
4. **Enhanced UX:** Clearly indicate when validation is happening (e.g., loading spinners).

</details>  
</br>

<details>

<summary>22. If a form has errors in multiple fields, how can you aggregate these errors into a summary displayed at the top of the form?</summary>

**Solution:**
Use Formik's `errors` object to generate a summary of all errors.

```javascript
const ErrorSummary = ({ errors }) => {
  return (
    <div>
      <h4>Error Summary:</h4>
      <ul>
        {Object.entries(errors).map(([key, value]) => (
          <li key={key}>{value}</li>
        ))}
      </ul>
    </div>
  );
};

<Formik
  initialValues={{ email: "", password: "" }}
  validationSchema={Yup.object({
    email: Yup.string().email().required("Email is required"),
    password: Yup.string().required("Password is required"),
  })}
  onSubmit={(values) => console.log(values)}
>
  {({ errors }) => (
    <Form>
      <ErrorSummary errors={errors} />
      <Field name="email" />
      <Field name="password" />
      <button type="submit">Submit</button>
    </Form>
  )}
</Formik>;
```

</details>  
</br>

<details>

<summary>23. How would you handle validation for a complex form with both required and optional fields that need to dynamically change validation logic based on backend responses?</summary>

**Solution:**

1. Fetch the backend configuration and dynamically build the Yup schema.
2. Use Formik’s `setFieldValue` and `setFieldTouched` methods to update the form dynamically.

**Example:**

```javascript
const getDynamicSchema = (config) => {
  const schemaFields = {};
  config.forEach((field) => {
    schemaFields[field.name] = field.required
      ? Yup.string().required(`${field.name} is required`)
      : Yup.string();
  });
  return Yup.object().shape(schemaFields);
};
```

</details>  
</br>

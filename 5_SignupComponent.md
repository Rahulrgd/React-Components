### Documentation for React Components

---

#### 1. **ApiClient.js**

**Purpose**: This file sets up an Axios instance for making API requests.

```javascript
import axios from "axios";

// Creating an Axios instance with the base URL for local API or production API
export const apiClient = axios.create({ baseURL: "http://localhost:5000" });
// For production, uncomment the below line and replace with the actual URL
// export const apiClient = axios.create({ baseURL: "http://job-tracker-2-version-env.eba-rimccpcb.eu-north-1.elasticbeanstalk.com" });
```

- **Key Points**:
  - The `apiClient` is an instance of Axios.
  - The base URL can be changed depending on the environment (local or production).
  - This Axios instance will be used throughout the app for making HTTP requests.

---

#### 2. **AuthenticationApiServices.js**

**Purpose**: This file contains functions to interact with authentication-related API endpoints.

```javascript
import { apiClient } from "./ApiClient";

// Function to handle user sign-up
export const signUpServicesApi = async (body) => { 
  await apiClient.post("/sign-up/", body);
}

// Function to handle JWT authentication
export const jwtAuthenticationServiceApi = async (request) => {
  return await apiClient.post("/authenticate", request);
};
```

- **Key Points**:
  - `signUpServicesApi`: Sends a POST request to the `/sign-up/` endpoint with user details (`body`).
  - `jwtAuthenticationServiceApi`: Sends a POST request to the `/authenticate` endpoint to authenticate the user using JWT.

---

#### 3. **AuthContext.js**

**Purpose**: This file provides API functions related to authentication, similar to `AuthenticationApiServices.js`.

```javascript
import { apiClient } from "./ApiClient";

// Repeated function for sign-up
export const signUpServicesApi = async (body) => { 
  await apiClient.post("/sign-up/", body);
}

// Repeated function for JWT authentication
export const jwtAuthenticationServiceApi = async (request) => {
  return await apiClient.post("/authenticate", request);
};
```

- **Key Points**:
  - The functions are the same as in `AuthenticationApiServices.js`. If this is a separate file, it should be used for managing authentication context or global state related to user sessions.

---

#### 4. **SignUpComponent.jsx**

**Purpose**: This is the signup form component where users can register for an account.

**Code Explanation**:

### Part 1: Initial Setup and State Management

This part of the code handles the setup of state variables that are needed to manage form values and track form submission success.

---

#### 1. **State Initialization**

We use React's `useState` to manage the state of the form fields (`fullName`, `email`, `password`, and `confirmPassword`). The initial state is set to empty strings.

```javascript
const [formValues, setFormValues] = useState({
  fullName: "",
  email: "",
  password: "",
  confirmPassword: "",
});
```

- **Explanation**:
  - `formValues` holds the current values of the form fields.
  - `setFormValues` is used to update the state when the user changes any input.

---

#### 2. **Success Message State**

We also define another state variable `successMessage` to track whether the form submission was successful, and a success message should be shown.

```javascript
const [successMessage, setSuccessMessage] = useState(false);
```

- **Explanation**:
  - This state is toggled when the form is successfully submitted, allowing the success message to be shown.

---

### Part 2: Form Validation Logic

The `validate` function performs the validation for each field in the form. It checks if the `fullName` is at least 3 characters long, if the `password` is at least 8 characters long, and if the `password` and `confirmPassword` match.

---

#### 1. **Validation Function**

```javascript
const validate = (values) => {
  const errors = {};
  if (values.fullName.length < 3) {
    errors.fullName = "FullName can not have less than 3 characters.";
  }
  if (values.password.length < 8) {
    errors.password = "Password can not have less than 8 characters.";
  }
  if (values.password !== values.confirmPassword) {
    errors.confirmPassword = "Password and Confirm Password are not same.";
  }
  return errors;
};
```

- **Explanation**:
  - The `validate` function takes the `values` (form data) as input and returns an `errors` object if any validation rules are violated.
  - Each error message corresponds to a specific field (e.g., `fullName`, `password`, and `confirmPassword`).

---

### Part 3: Form Submission Logic

Here, we handle the form submission, which includes sending data to an API and resetting the form values upon successful submission.

---

#### 1. **Form Submission Logic**

The `onSubmit` function is triggered when the user submits the form. It sends the form data to the `signUpServicesApi` and handles success or error responses.

```javascript
const onSubmit = async (values) => {
  const user = {
    fullName: values.fullName,
    email: values.email,
    password: values.password,
  };

  try {
    const response = await signUpServicesApi(user);
    clearValues();
    setSuccessMessage(true);
    setTimeout(() => setSuccessMessage(false), 3000);
  } catch (error) {
    console.log(error);
  }
};
```

- **Explanation**:
  - The `onSubmit` function is called when the user submits the form. It first formats the user data and sends it to `signUpServicesApi`.
  - If the API call is successful, the form is cleared, and a success message is displayed.
  - The success message is hidden after 3 seconds using `setTimeout`.

---

### Part 4: Formik Integration

In this part, we integrate Formik to handle form validation, submission, and state management automatically.

---

#### 1. **Formik Component**

We use Formik to manage the form's state, validation, and submission. The `initialValues`, `validate`, and `onSubmit` props are passed to Formik.

```javascript
<Formik
  initialValues={formValues}
  enableReinitialize={true}
  validate={validate}
  validateOnBlur={false}
  validateOnChange={false}
  onSubmit={onSubmit}
>
  {(props) => (
    <Form>
      {/* Form fields and components */}
    </Form>
  )}
</Formik>
```

- **Explanation**:
  - `initialValues` is set to the `formValues` state, which ensures the form starts with empty fields.
  - `validate` is passed the `validate` function to handle form field validation.
  - `onSubmit` is the form submission handler.

---

### Part 5: Rendering the Form Fields and Error Messages

This section contains the JSX to render the form fields, error messages, and the success notification.

---

#### 1. **Success Message**

If the form is successfully submitted, a success alert is displayed using the `Alert` component from React Bootstrap.

```javascript
{successMessage && (
  <Alert key="success" variant="success">
    Account Created Successfully!
  </Alert>
)}
```

- **Explanation**:
  - This conditional rendering shows the success message when the `successMessage` state is `true`.

---

#### 2. **Error Messages**

We display any validation errors below the respective form fields using the `ErrorMessage` component from Formik.

```javascript
<ErrorMessage name="fullName" component="div" className="alert alert-warning" />
<ErrorMessage name="password" component="div" className="alert alert-warning" />
<ErrorMessage name="confirmPassword" component="div" className="alert alert-warning" />
```

- **Explanation**:
  - The `ErrorMessage` component takes the name of the field and the CSS class to display error messages in a styled way.

---

#### 3. **Form Fields**

Each form field (`fullName`, `email`, `password`, `confirmPassword`) is rendered using Formik’s `Field` component. The `name` prop connects the form field to the Formik state.

```javascript
<fieldset className="form-group m-3">
  <label className="label m-1">Full Name</label>
  <Field type="text" className="form-control" name="fullName" />
</fieldset>
<fieldset className="form-group m-3">
  <label className="label m-1">Email</label>
  <Field type="email" className="form-control" name="email" />
</fieldset>
<fieldset className="form-group m-3">
  <label className="label m-1">Password</label>
  <Field type="password" className="form-control" name="password" />
</fieldset>
<fieldset className="form-group m-3">
  <label className="label m-1">Confirm Password</label>
  <Field type="password" className="form-control" name="confirmPassword" />
</fieldset>
```

- **Explanation**:
  - `Field` components bind each input field to Formik's internal state, making it easier to manage form data and validation.

---

### Part 6: Submit Button and Login Link

The form includes a submit button to trigger the form submission, and a link to navigate to the login page.

---

#### 1. **Submit Button**

The button is styled with Bootstrap’s `btn btn-success` class, and it submits the form when clicked.

```javascript
<button className="btn btn-success" type="submit">
  Sign Up
</button>
```

- **Explanation**:
  - This button triggers Formik’s `onSubmit` function when clicked.

---

#### 2. **Link to Login Page**

We provide a link to the login page using the `Link` component from `react-router-dom`.

```javascript
<div className="pt-5 text-muted d-flex justify-content-center">
  <Link to="/login">Login</Link>
</div>
```

- **Explanation**:
  - The `Link` component allows navigation to the login page without reloading the page.


---

**Key Parts:**

- **Formik Setup**: Manages form state and validation for user registration.
- **Validation**: Ensures that the password is at least 8 characters and that passwords match.
- **onSubmit**: Handles the user sign-up request by calling the `signUpServicesApi` function.
- **ErrorMessage**: Displays validation error messages dynamically.
- **Success Message**: Displays a success message when the sign-up is successful.
- **Link to Login**: A `Link` component from `react-router-dom` to navigate to the login page.

---

#### To be continued in the next part...
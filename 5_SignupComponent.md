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

```javascript
import { useState } from "react";
import { ErrorMessage, Field, Form, Formik } from "formik";
import { Alert } from "react-bootstrap";
import { signUpServicesApi } from "../api/AuthenticationApiServices";
import { Link } from "react-router-dom";

function SignupComponent() {
  const [formValues, setFormValues] = useState({
    fullName: "",
    email: "",
    password: "",
    confirmPassword: "",
  });

  // Form validation logic
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

  // Clear form values after submission
  const clearValues = () => {
    setFormValues({
      fullName: "",
      email: "",
      password: "",
      confirmPassword: "",
    });
  };

  const [successMessage, setSuccessMessage] = useState(false);

  // Form submission logic
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

  return (
    <div className="container mt-5">
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
            {/* Success Message after successful sign-up */}
            {successMessage && (
              <Alert key="success" variant="success">
                Account Created Successfully!
              </Alert>
            )}
            <div className="d-flex justify-content-center">
              <h1>Sign Up Page</h1>
            </div>
            {/* Error Messages */}
            <ErrorMessage name="fullName" component="div" className="alert alert-warning" />
            <ErrorMessage name="password" component="div" className="alert alert-warning" />
            <ErrorMessage name="confirmPassword" component="div" className="alert alert-warning" />
            {/* Form Fields */}
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
            {/* Submit Button */}
            <div className="d-flex justify-content-end">
              <button className="btn btn-success" type="submit">
                Sign Up
              </button>
            </div>
            {/* Link to Login */}
            <div className="pt-5 text-muted d-flex justify-content-center">
              <Link to="/login">Login</Link>
            </div>
          </Form>
        )}
      </Formik>
    </div>
  );
}

export default SignupComponent;
```

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
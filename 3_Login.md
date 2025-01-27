### Part 1: **AppClient.js**

---

#### Description:  
`AppClient.js` is responsible for setting up a reusable `axios` instance. This ensures all API requests share a common base URL, which can easily be updated for different environments (development, staging, or production).

#### Key Features:  
- **Reusable API Client**: Simplifies API requests by avoiding repetitive base URL definitions.  
- **Environment Flexibility**: Switch between local and production environments by modifying or uncommenting the `baseURL`.

#### Code Snippet:  
```javascript
import axios from "axios";

// Local development base URL
export const apiClient = axios.create({ baseURL: "http://localhost:5000" });

// Production environment (commented out for now)
// export const apiClient = axios.create({ 
//   baseURL: "http://job-tracker-2-version-env.eba-rimccpcb.eu-north-1.elasticbeanstalk.com" 
// });
```

---

---

### Part 2: **AuthenticationApiServices.js**

---

#### Description:  
This file contains API service functions for user authentication. These functions interact with specific endpoints using the `apiClient`.

#### Functions:  

1. **`signUpServicesApi`**  
   - Sends a `POST` request to the `/sign-up/` endpoint with the user data.  

   **Code Snippet:**  
   ```javascript
   export const signUpServicesApi = async (body) => {
     await apiClient.post("/sign-up/", body);
   };
   ```

2. **`jwtAuthenticationServiceApi`**  
   - Sends a `POST` request to `/authenticate` with login credentials.  

   **Code Snippet:**  
   ```javascript
   export const jwtAuthenticationServiceApi = async (request) => 
     await apiClient.post("/authenticate", request);
   ```

#### Usage:  
Both functions rely on the `apiClient` for consistent API calls.

---

---

### Part 3: **AuthContext.js**

---

#### Description:  
The `AuthContext.js` file handles global state management for authentication. It uses the Context API and React hooks to manage and expose authentication-related logic and states to the app.

---

#### Key Components:

1. **Authentication State:**  
   - `isAuthenticated`: Tracks the user's login status.  
   - `token`: Stores the JWT token after login.  

2. **Login Function:**  
   Authenticates the user using the `jwtAuthenticationServiceApi`. Upon success:  
   - Saves the JWT token to the state.  
   - Configures an `axios` interceptor to attach the `Authorization` header for future requests.  

   **Code Snippet:**  
   ```javascript
   async function login(request) {
     try {
       const response = await jwtAuthenticationServiceApi(request);
       setToken(response.data.jwtToken);
       setAuthenticated(true);

       // Set up Authorization header for future requests
       apiClient.interceptors.request.use((config) => {
         config.headers.Authorization = `Bearer ${response.data.jwtToken}`;
         return config;
       });
       return true;
     } catch (error) {
       console.log(error);
       return false;
     }
   }
   ```

3. **Logout Function:**  
   - Resets authentication state and clears the token.  
   - Reloads the page to clear session-related data.  

   **Code Snippet:**  
   ```javascript
   function logout() {
     setAuthenticated(false);
     setToken(null);
     setTimeout(() => window.location.reload(), 1);
   }
   ```

4. **Auth Context Provider:**  
   Provides the authentication methods and state to the component tree.  

   **Code Snippet:**  
   ```javascript
   return (
     <AuthContext.Provider value={{ isAuthenticated, setAuthenticated, login, logout, token }}>
       {children}
     </AuthContext.Provider>
   );
   ```

#### Usage:  
Import and wrap your app in the `AuthProvider` to make authentication logic available globally.

---

---

### Part 4: **LoginComponent.jsx**

---

#### Description:  
This file implements the login page UI and logic. It integrates with `AuthContext` for authentication and uses `Formik` for form management.

---

#### Key Features:  

1. **State Management:**  
   - `formValues`: Manages email and password input.  
   - `invalidCredentialsMessage`: Tracks and displays invalid login attempts.  

2. **Validation:**  
   - Ensures passwords have at least 8 characters.  
   - Displays appropriate error messages.  

   **Code Snippet:**  
   ```javascript
   const validate = (values) => {
     const errors = {};
     if (values.password.length < 8) {
       errors.password = "Password can not have less than 8 characters";
     }
     return errors;
   };
   ```

3. **Login Logic:**  
   - Submits credentials to the `login` function from `AuthContext`.  
   - Redirects users to `/all-job-posts` on successful login.  
   - Displays an error message for invalid credentials.  

   **Code Snippet:**  
   ```javascript
   const onSubmit = async (values) => {
     const body = {
       email: values.email,
       password: values.password,
     };
     setFormValues(values);
     const authentication = await authContext.login(body);
     if (authentication) {
       navigate("/all-job-posts");
     } else {
       setInvalidCredentialsMessage(true);
       setTimeout(() => setInvalidCredentialsMessage(false), 3000);
     }
   };
   ```

4. **Formik Integration:**  
   Simplifies form handling and validation with declarative APIs.  

   **Code Snippet:**  
   ```javascript
   <Formik
     initialValues={formValues}
     enableReinitialize={true}
     validate={validate}
     onSubmit={onSubmit}
     validateOnBlur={false}
     validateOnChange={false}
   >
     {(props) => (
       <Form className="form">
         <ErrorMessage name="password" component="div" className="alert alert-warning" />
         {invalidCredentialsMessage && (
           <Alert key="danger" variant="danger">
             Invalid Credentials.
           </Alert>
         )}
         <fieldset className="form-group">
           <label className="label m-1">Email</label>
           <Field className="form-control" name="email" type="email" />
         </fieldset>
         <fieldset className="form-group">
           <label className="label m-1">Password</label>
           <Field className="form-control" name="password" type="password" />
         </fieldset>
         <fieldset className="d-flex justify-content-end">
           <button className="btn btn-success m-3">Login</button>
         </fieldset>
       </Form>
     )}
   </Formik>
   ```

5. **Signup Redirect:**  
   Provides a link to the signup page for new users.  

   **Code Snippet:**  
   ```javascript
   <div className="d-flex justify-content-center m-5 text-secondary">
     To create account - 
     <Link className="mx-2" to="/signup">
       click here.
     </Link>
   </div>
   ```

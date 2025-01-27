## React Authentication Documentation

### Overview
This React Authentication module provides a simple and secure way to manage user authentication in your application. It includes services for signing up users, logging in with JWT tokens, and protecting routes that require authentication.

---

### Components

#### 1. **`ApiClient.js`**

This file configures the `axios` instance to communicate with the backend API.

```javascript
import axios from "axios";

export const apiClient = axios.create({ baseURL: "http://localhost:5000" });
```

- **Description**: The `apiClient` is an instance of `axios` with a pre-configured base URL (`http://localhost:5000`), which is the server for authentication requests.
- **Usage**: This instance is used in other services (like `AuthenticationApiServices.js`) to make API calls.

---

#### 2. **`AuthenticationApiServices.js`**

This file defines two API service functions for signing up and authenticating users.

```javascript
import { apiClient } from "./ApiClient";

export const signUpServicesApi = async (body) => { 
  await apiClient.post("/sign-up/", body);
};

export const jwtAuthenticationServiceApi = async (request) => 
  await apiClient.post("/authenticate", request);
```

- **Functions**:
  - `signUpServicesApi(body)`: Sends a `POST` request to the `/sign-up/` endpoint to create a new user account.
  - `jwtAuthenticationServiceApi(request)`: Sends a `POST` request to the `/authenticate` endpoint to authenticate the user and return a JWT token.

---

#### 3. **`AuthContext.js`**

This file defines the `AuthContext` and provides authentication logic using React Context API.

```javascript
import { createContext, useContext, useState } from "react";
import { apiClient } from "../api/ApiClient";
import { jwtAuthenticationServiceApi } from "../api/AuthenticationApiServices";

export const AuthContext = createContext();

export const useAuth = () => useContext(AuthContext);

export default function AuthProvider({ children }) {
  const [isAuthenticated, setAuthenticated] = useState(false);
  const [token, setToken] = useState(null);

  async function login(request) {
    try {
      const response = await jwtAuthenticationServiceApi(request);
      setToken(response.data.jwtToken);
      setAuthenticated(true);

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

  function logout() {
    setAuthenticated(false);
    setToken(null);
    setTimeout(() => window.location.reload(), 1);
  }

  return (
    <AuthContext.Provider value={{ isAuthenticated, setAuthenticated, login, logout, token }}>
      {children}
    </AuthContext.Provider>
  );
}
```

- **Context**: `AuthContext` is created to store and manage authentication state across the application.
- **State**:
  - `isAuthenticated`: Boolean flag indicating whether the user is logged in.
  - `token`: Stores the JWT token received after a successful login.
- **Functions**:
  - `login(request)`: Authenticates the user by calling the `jwtAuthenticationServiceApi` and stores the JWT token.
  - `logout()`: Logs the user out, clears the authentication state, and refreshes the page.
- **Usage**: Wrap the `AuthProvider` component around your app’s root component to provide authentication context to all children components.

---

#### 4. **`App.js`**

This file contains the routing logic, including protecting authenticated routes.

```javascript
function AuthenticatedRoute({ children }) {
  const authContext = useAuth();
  if (authContext.isAuthenticated) {
    return children;
  }
  return <Navigate to="/login" />;
}

<Route
  path="/user-profile"
  element={
    <AuthenticatedRoute>
      <UserComponent />
    </AuthenticatedRoute>
  }
/>
```

- **`AuthenticatedRoute` Component**: This component checks if the user is authenticated. If yes, it renders the `children` components. If not, it redirects to the login page.
- **Route Protection**: The route for `/user-profile` is protected by the `AuthenticatedRoute`. Only authenticated users can access it.

---

### How to Use This Authentication Module

1. **Wrap Your App with `AuthProvider`**:
   In your `App.js` or `index.js`, wrap your application with the `AuthProvider` to make authentication data available to all components.

   ```javascript
   import AuthProvider from "./context/AuthContext";

   function App() {
     return (
       <AuthProvider>
         <Routes>
           {/* Your routes here */}
         </Routes>
       </AuthProvider>
     );
   }
   ```

2. **Using the Authentication Context**:
   Use the `useAuth` hook inside any component to access authentication states and functions like `login` and `logout`.

   Example:
   ```javascript
   const { login, logout, isAuthenticated } = useAuth();
   ```

3. **Protect Routes**:
   Use the `AuthenticatedRoute` component to protect routes from unauthorized access.

   Example:
   ```javascript
   <Route path="/dashboard" element={<AuthenticatedRoute><Dashboard /></AuthenticatedRoute>} />
   ```

---

### Notes
- This authentication module uses JWT (JSON Web Tokens) for securing user sessions.
- Ensure that your backend API is correctly set up to handle the `/sign-up/` and `/authenticate` routes.
- You can extend this module by adding features like password recovery, email verification, etc.
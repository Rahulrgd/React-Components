
## React Router Dom Documentation

### Overview
**React Router Dom** is a standard library for routing in React applications. It enables you to handle navigation, render components based on the URL, and manage route protection with ease.

---

### Installation

To install **React Router Dom** in your project, run the following command:

```bash
npm install react-router-dom
```

This will add React Router Dom to your project and make its components like `BrowserRouter`, `Routes`, and `Route` available for use.

---

### Basic Setup

After installing the package, you need to wrap your application in a **Router** component (`BrowserRouter`) to enable routing functionality across your app. The `Routes` component will hold all the `Route` definitions, which will map URLs to their corresponding components.

### Relevant Code

```javascript
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import { AuthProvider } from './context/AuthContext'; // Example import of AuthContext
import NavbarComponent from './components/NavbarComponent';
import FooterComponent from './components/FooterComponent';
import LoginComponent from './components/LoginComponent';
import SignupComponent from './components/SignupComponent';
import UserComponent from './components/UserComponent';
import AuthenticatedRoute from './components/AuthenticatedRoute';
import NoPage from './components/NoPage';

function App() {
  return (
    <AuthProvider>
      <BrowserRouter>
        <NavbarComponent />
        <Routes>
          {/* Public Routes */}
          <Route path="/login" element={<LoginComponent />} />
          <Route path="/signup" element={<SignupComponent />} />

          {/* Protected Routes (only accessible if authenticated) */}
          <Route path="/user-profile" element={<AuthenticatedRoute><UserComponent /></AuthenticatedRoute>} />
          <Route path="/user-job-posts" element={<AuthenticatedRoute><UserJobPostsComponent /></AuthenticatedRoute>} />
          <Route path="/user-resumes" element={<AuthenticatedRoute><UsersResumeComponent /></AuthenticatedRoute>} />
          <Route path="/upload-resume" element={<AuthenticatedRoute><UploadResumeComponent /></AuthenticatedRoute>} />
          <Route path="/editJobPost/:id" element={<AuthenticatedRoute><EditJobPostsComponent /></AuthenticatedRoute>} />
          <Route path="/add-job-posts" element={<AuthenticatedRoute><AddJobPostsComponent /></AuthenticatedRoute>} />

          {/* Default and Fallback Routes */}
          <Route path="/" element={<AllJobPostsComponent />} />
          <Route path="/all-job-posts" element={<AllJobPostsComponent />} />
          <Route path="/job-details/:id" element={<JobDetailsComponent />} />

          {/* Fallback for non-existing routes */}
          <Route path="*" element={<NoPage />} />
        </Routes>
        <FooterComponent />
      </BrowserRouter>
    </AuthProvider>
  );
}

export default App;
```

### Explanation

1. **`BrowserRouter`**:
   - This component wraps your entire app and provides the routing context. It keeps track of the URL and updates the displayed content based on the current path.
   
   Example:
   ```javascript
   <BrowserRouter>
     {/* Routes and components go here */}
   </BrowserRouter>
   ```

2. **`Routes`**:
   - This component is responsible for rendering the correct route based on the current URL.
   - It contains multiple `Route` components that define the URL path and the component to render.

   Example:
   ```javascript
   <Routes>
     <Route path="/login" element={<LoginComponent />} />
   </Routes>
   ```

3. **`Route`**:
   - Defines a route and maps a path (URL) to a specific component.
   - The `element` prop defines which component should be rendered when the path is matched.

   Example:
   ```javascript
   <Route path="/login" element={<LoginComponent />} />
   ```

4. **Protected Routes with `AuthenticatedRoute`**:
   - In the example, `AuthenticatedRoute` is used to protect routes that require the user to be authenticated (i.e., logged in).
   - It checks if the user is authenticated. If yes, it renders the child component; if not, it redirects to the login page.

   Example:
   ```javascript
   <Route path="/user-profile" element={<AuthenticatedRoute><UserComponent /></AuthenticatedRoute>} />
   ```

   This ensures that only authenticated users can access the `/user-profile` route.

5. **Fallback Route (`path="*"`)**:
   - The `path="*"` route catches any non-matching URLs and renders a fallback component, such as a `NoPage` component that can display a 404 page.

   Example:
   ```javascript
   <Route path="*" element={<NoPage />} />
   ```

6. **Dynamic Routes with Parameters**:
   - Dynamic routes allow you to capture parts of the URL as parameters. For example, the `id` in `/editJobPost/:id` is a dynamic parameter that can be accessed inside the component.

   Example:
   ```javascript
   <Route path="/editJobPost/:id" element={<AuthenticatedRoute><EditJobPostsComponent /></AuthenticatedRoute>} />
   ```

   Inside `EditJobPostsComponent`, you can access the `id` parameter using the `useParams` hook from React Router Dom:
   ```javascript
   import { useParams } from 'react-router-dom';
   const { id } = useParams();
   ```

---

### Summary of Key Concepts:
- **`BrowserRouter`**: Wraps the entire application and enables routing.
- **`Routes`**: Holds all the `Route` definitions for different paths.
- **`Route`**: Maps a URL path to a React component.
- **`AuthenticatedRoute`**: A custom component that protects routes based on authentication.
- **`path="*"`**: A fallback route for non-existing paths (commonly used for 404 pages).
- **Dynamic Routes**: Use URL parameters to pass data to components.

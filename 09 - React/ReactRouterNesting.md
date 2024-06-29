## Nested Routes with React Router

The new router provider and createRouter functions allow for much easier nesting. It is accomplished in the router configuration. A Page/Route is given the property 'children', and a page object/route object (path, element, errorElement) to the children property!

### Example Code
```js
// INSIDE src/index.js OR src/main.js (same thing)

import {
  createBrowserRouter,
  RouterProvider,
} from 'react-router-dom';

// import routes/pages from pages folder
// these components will be passed into the router object during configuration
import HomePage from './pages/HomePage'; 
import LoginPage from './pages/LoginPage';
import RegisterPage from './pages/RegisterPage';
import ErrorPage from './pages/ErrorPage';
import UsersPage from './pages/UsersPage';

// router object is created and configured here
// all routes/pages are defined here!!
const router = createBrowserRouter([
  {
    path: '/',
    element: <HomePage/>,
    errorElement: <ErrorPage/>
  },
  {
    path: '/login',
    element: <LoginPage/>,
    errorElement: <ErrorPage/>
  },
  {
    path: '/register',
    element: <RegisterPage/>,
    errorElement: <ErrorPage/>
  },
  {
    path:'/users',
    element: <UsersPage/>,
    errorElement: <ErrorPage/>,
    children: // CREATES A NESTED ROUTE, rendered with <Outlet/> inside <UsersPage/>
      {
        path: '/users/:id',
        element: <ProfilePage/>,
        errorElement: <ErrorPage/>
      }
  },    
  
]);

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <RouterProvider router={router} /> // router provider component is passed pre-configured router object in property called router
  </React.StrictMode>
)


// INSIDE src/pages/UsersPage.jsx

import Navbar from './../components/NavBar.jsx'
import UsersList from './../components/UsersList.jsx'

export function UsersPage(){

  return (
    <NavBar/>
    <UsersList/>
    <Outlet/> // outputs the children, inside router configuration inside main.jsx or index.jsx
  )
}

```
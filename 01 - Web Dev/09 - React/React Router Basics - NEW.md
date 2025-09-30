## React Router 

library for react that unlocks browser utilities for single page apps. There are multiple routers you can use:
* BrowserRouter - standard router, should be used in 99% of cases
* MemoryRouter - like browser router, but gives you complete control over user history
* HashRouter - 
* StaticRouter - 

React Router uses react context

This new format replaces Router Components for creating a react router. Router Components cannot use new data apis. (*add info on this!)

### Setup
1. Import createBrowserRouter function
2. Import RouterProvider Component
3. Create and configure the router object
4. Pass the router object to the RouterProvider via the 'router' property
5. create the page components that match the router configuration


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
    path:'/users', // PAGE SHOWING ALL USERS, link has user ID
    element: <UsersPage/>,
    errorElement: <ErrorPage/>
  },
  {
    path: '/users/:id', // DYNAMIC ADDRESS, using user ID
    element: <ProfilePage/>,
    errorElement: <ErrorPage/>
  }
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
  )
}

// INSIDE src/components/Navbar.jsx

// imports link component which is used to link buttons/text to various "pages" without reloading the browser page
// acts like an anchor tag, but does not break the single-page-app experience
import {link} from "react-router-dom";

export function Navbar() {

  return (
    <ul>
      <li>
        <Link to="/" >Home</Link> // takes 'to' property to change address bar when clicked
      </li>
      <li>
        <Link to="/login" >Login</Link> // takes 'to' property to change address bar when clicked
      </li>
      <li>
        <Link to="/register" >Register</Link> // takes 'to' property to change address bar when clicked
      </li>
    </ul>
  )
}

// INSIDE src/components/UsersList.jsx

import UserProfile from './UserProfile';

export function UsersList(props){
  const {usersList} = props;

  return (
    <>
      {usersList}
    </>
  )
}

// INSIDE src/components/UserProfile.jsx

import {useParams} from "react-router-dom";

export function UserProfile() {
  const {id} = useParams(); // grabs the parameters in the address bar, such as userId. Can be anything. can be multiple parameters, depending on what is defined in the route path
  // key of 'id' is set by the path in Route component
  // value of 'id' is set by the actual url in the browser
  return (
    <h1>User {id}</h1>
  )
}

```
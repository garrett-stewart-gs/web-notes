## React Router

library for react that unlocks browser utilities for single page apps

### Setup
1. import BrowserRouter from React-Router-Dom (or React-Router-Native for mobile)
2. wrap the entire app in the BrowserRouter Component (BrowserRouter is using react context!!)
3. create pages directory with all of the page routes you want the BrowserRouter to serve (ie. home, contact-us, register, login, not-found, etc..)
4. import route and routes components
5. wrap the each individual route inside the routes component
6. give each individual route component a path and an element. the element given should be a jsx component within the pages directory
7. import link component inside navbar to setup nav links (you can import link component in other areas too)

### Example Code
```js
// INSIDE src/index.js
import {BrowserRouter} from "react-router-dom";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <App/> // APP IS WRAPPED INSIDE BROWSERROUTER!!
    </BrowserRouter>
  </React.StrictMode>
)


// INSIDE src/app.js

// imports routes component which contains all of the routes for the website
// also imports the route component which is used inside the routes component to define individual routes
import {route, routes} from "react-router-dom";

// imports project-specific pages from pages directory
import {Home} from "./pages/Home";
import {Login} from "./pages/Login";
import {Register} from "./pages/Register";
import {UserProfile} from "./pages/UserProfile";

export function App() {

  return (
    <>
      <Navbar/> // renders nav bar on all pages, independent of selected route
      <Routes> // components inside will only be rendered if url in address bar matches path!!
        <Route path="/" element={<Home/>}/> // sets '/' path to load pages/Home.jsx
        <Route path="/login" element={<Login/>}/> // sets '/login' path to load pages/Login.jsx
        <Route path="/register" element={<Register/>}/> // sets '/register' path to load pages/register.jsx
        <Route path="/user/:id" element={<UserProfile/>}/> // sets view to user profile determined by id
      </Routes>
    </>
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
        <Link to="/" ></Link> // takes 'to' property to change address bar when clicked
      </li>
      <li>
        <Link to="/login" ></Link> // takes 'to' property to change address bar when clicked
      </li>
      <li>
        <Link to="/register" ></Link> // takes 'to' property to change address bar when clicked
      </li>
    </ul>
  )
}

// INSIDE src/pages/UserProfile

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
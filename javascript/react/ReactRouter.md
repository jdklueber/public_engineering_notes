# React Router

The de facto package for making your single page application work like a multi page application.

## Installation 

```console
npm install react-router-dom
```

## Setting up the routes:

**`App.java`**

```jsx
import {BrowserRouter, Routes, Route} from "react-router-dom";
function App() {
  return (
    <div>
      <BrowserRouter>
          <Routes>
              <Route path={"/"} element={<Home/>}/>
              <Route path={"/profile"} element={<Profile/>}/>
              <Route path={"/sign-in"} element={<SignIn/>}/>
              <Route path={"/sign-up"} element={<SignUp/>}/>
              <Route path={"/forgot-password"} element={<ForgotPassword/>}/>
              <Route path={"/offers"} element={<Offers/>}/>
          </Routes>
      </BrowserRouter>
    </div>
  );
}
```

Note:  Navigation components need to be inside of the `<BrowserRouter>` tag, so that `<Link>` will work properly.

Programmatic navigation (the `useNavigate` hook) and current location detection (the `useLocation` hook):

```jsx
import {useLocation, useNavigate} from "react-router-dom";

const location = useLocation();
    const navigate = useNavigate();
	
	//If you want to change up styling based on current location
	//Called from the roll your own links below
    const routeStyling = (route) => {
        return route === location.pathname ? "text-black border-b-red-500" : "";
    }
    
    // setting up the page
    // ...
    <ul className={"flex flex-row space-x-10"}>
        <li className={`${routeStyling("/")}` }
            onClick={() => navigate("/")}>Home</li>
        <li className={`${routeStyling("/offers")}`}
            onClick={() => navigate("/offers")}>Offers</li>
        <li className={`${routeStyling("/sign-in")}`}
            onClick={() => navigate("/sign-in")}>Sign In</li>
	</ul>
    
```

`react-router-dom` `Link` elements still work:

```jsx
import {Link} from "react-router-dom";
// ...
<Link to={"/forgot-password"}}>Forgot password?</Link>
```

Presumably `NavLink` works too.

### Private routes

Sometimes you need pages that can only be seen by signed in users.  **Note that this technique only works to redirect users, but anyone with web development skills can still see these pages with a little bit of work.** Only use this for UX reasons, not security reasons.  Secure your data on the server, not by hiding it with private routes!

There is a simple technique for handling private routes using some basic components of the `react-router-dom` package.  Here's the code:

`PrivateRoute.js`

```jsx
import {Outlet, Navigate} from "react-router-dom";

function PrivateRoute() {
    const loggedin = false; //This needs to actually know what the sign in state is (or whatever condition you're checking for)

    return loggedin ? <Outlet/>  //If logged in, <Outlet/> is set to 
                                 //whatever is enclosed by the
    				             //<PrivateRoute> tag
    							 //This is roughly equivalent to {children}
                    : <Navigate to={"/sign-in"}/>
                                //If not logged in, <Navigate> will redirect
                                //to the given route.
}

export default PrivateRoute;
```

One way you can accomplish the sign in test is with a custom hook, like so:

`useAuthStatus.js`

```jsx
import {useEffect, useState} from "react";
import {getAuth, onAuthStateChanged} from "firebase/auth";

function useAuthStatus() {
    //Keep the current log in status / waiting for info status in state
    const [loggedIn, setLoggedIn] = useState(false);
    const [waiting, setWaiting] = useState(true);

    //Set up the onAuthStateChanged listener to update the logged in state
    //whenever Firebase reports that it has changed
    useEffect(() => {
        const auth = getAuth();
        onAuthStateChanged(auth, (user) => {
            if (user) {
                setLoggedIn(true)
            } else {
                setLoggedIn(false);
            }
            //If this fires once, then we're connected to Firebase
            //and no longer need to report that we are waiting
            setWaiting(false);
        })
    }, []);

    //These are the variables that will be exposed by the hook
    return {loggedIn, waiting};
}

export {useAuthStatus};
```

Meanwhile, back in our `PrivateRoute` component...

`PrivateRoute.js`

```jsx
import {Outlet, Navigate} from "react-router-dom";
import {useAuthStatus} from "../hooks/useAuthStatus";

function PrivateRoute() {
    const {loggedIn, waiting} = useAuthStatus(); //Calling the hook

    if (waiting) {
        <h3>Loading...</h3> //Do better here, this is ugly
    } else {
        //The navigate to might need to be changed for your application
        return loggedIn ? <Outlet/> : <Navigate to={"/sign-in"}/>
    }
}

export default PrivateRoute;
```

Finally, let's look at how `PrivateRoute` is used to protect a `Route`:

`App.js`

```jsx
//skipping all the other imports
import PrivateRoute from "./components/PrivateRoute";

function App() {
  return (
    <div>
      <BrowserRouter>
          <Header/>
          <Content>
              <Routes>
                  <!-- skipping the routes we don't care about -->
                  <!-- The PrivateRoute has Profile as a sub route -->
                  <!-- this is how the Outlet tag gets its target -->
                  <Route path={"/profile"} element={<PrivateRoute/>}>
                    <Route path={"/profile"} element={<Profile/>}/>
                  </Route>
                  <!-- skipping the routes we don't care about -->
              </Routes>
          </Content>
      </BrowserRouter>
      <!-- Skipping the toast section -->
    </div>
  );
}

export default App;

```


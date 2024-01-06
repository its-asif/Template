# Project Name

Brief project description or tagline.

### Table of Contents
- [Overview](#overview)
- [Installation & Setup](#installation--setup)
  - [Backend](#backend)
  - [Client Side](#client-side)
  - [Firebase](#firebase)
- [React Template](#react-template)
  - [React Router](###react-router)
- [Deployment](#deployment)
 
## Overview
Provide a high-level overview of your project. What problem does it solve? Why is it awesome?

## Installation & Setup

### Backend
1. Create a 'server' directory.
2. Run the following commands:
```bash
npm init -y
npm i express cors mongodb dotenv
```
3. Create `index.js` and set up basic server configuration.
```js
const express = require("express");
const cors = require("cors");
const app = express();
const port = process.env.PORT || 5000;

// middleware
app.use(cors());
app.use(express.json());

app.get('/', (req, res) =>{
    res.send("Coffee server is running");
})

app.listen(port, () =>{
    console.log(`Coffee Server Port: ${port}`);
})
```

**[⬆ Back to Top](#table-of-contents)**

<hr/>

### Client Side 

1. Use React Router and create a Vite project. Run the following command:
```bash
npm create vite@latest name-of-your-project -- --template react
```

```bash
cd ..
```
2. Install necessary dependencies. Run the following command:
```bash
npm install react-router-dom localforage match-sorter sort-by
```
3. Set up Tailwind CSS and Daisy UI.
```bash
npm install -D tailwindcss postcss autoprefixer 
npm i -D daisyui@latest
npx tailwindcss init -p
```
4. tailwind.config.js

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: [
    "./index.html",
    "./src/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [require("daisyui")],
}

```

5. index.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

6. 
```
npm run dev
```

<!-- add important links -->

[React Router](https://reactrouter.com/en/main/start/tutorial)

[TailwindCSS](https://tailwindcss.com/docs/guides/vite)

[DaisyUI](https://daisyui.com/docs/install/)


<!-- 
### Setup MongoDB
### Handling Forms
### Adding Routes
### Setup Firebase -->


**[⬆ Back to Top](#table-of-contents)**

<hr/>

### Firebase

```
npm i firebase
```

<br/>

- paste in `firebase.config.js`
- export default app;

<br/>

`provider/AuthProvider.jsx`

```jsx
import { getAuth, onAuthStateChanged } from "firebase/auth";
import { createContext, useEffect, useState } from "react";
import app from "../firebase/firebase.config";

export const AuthContext = createContext(null);

const AuthProvider = ({children}) => {
    const auth = getAuth(app);
    const [loading, setLoading] = useState(true);
    const [user, setUser] = useState(null);

    
    // Get the currently signed-in user
    useEffect(() => {
        onAuthStateChanged(auth, currentUser => {
            setUser(currentUser);
            setLoading(false);
        })
    }, []);


    
    const authInfo = {
        user, setUser, loading, setLoading
    };
    
    return (
        <div>
            <AuthContext.Provider value={authInfo}>
                {children}
            </AuthContext.Provider>
        </div>
    );
};

export default AuthProvider;
```

- cover main with AuthProvider

<br/>

`ContinueWithGoogle`

```jsx
import { useContext } from "react";
import { AuthContext } from "../../provider/AuthProvider";
import { GoogleAuthProvider, signInWithPopup } from 'firebase/auth';
import { Navigate, useNavigate } from "react-router-dom";

const ContinueWithGoogle = () => {
    const {auth, user, setUser} = useContext(AuthContext);
    const navigate = useNavigate();

    // Google login
    const provider = new GoogleAuthProvider();

    const handleGoogleLogin = () => {
        signInWithPopup(auth, provider)
            .then((result) => {
                const user = result.user;
                // console.log(user);
                const {displayName, email, photoURL} = user;
                setUser({ name : displayName, email, photoURL});

                // Save user to database
                // show success message
                
                navigate("/");  // redirect to home page

            }).catch((error) => {
                const errorMessage = error;
                console.log(errorMessage);
            });

    }

    return (
        <div>
            <button 
                className="btn"
                onClick={() => handleGoogleLogin()}
            >Google Login</button>

        </div>
    );
};

export default ContinueWithGoogle;
```

**[⬆ Back to Top](#table-of-contents)**

## React Template

### React Router

1. Initialization

`main.jsx`
```jsx

import {
  RouterProvider,
} from "react-router-dom";
import router from './router/Router.jsx';


ReactDOM.createRoot(document.getElementById('root')).render(
  <RouterProvider router={router} />,
)

```

`Route.jsx`
```jsx

import {
    createBrowserRouter
  } from "react-router-dom";
  
  const router = createBrowserRouter([
    {
      path: "/",
      element: <div>Hello world!</div>,
    },
  ]);


  export default router;
```

2. Nested Routes and Error Page

`Route.jsx`
```jsx

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

`Main.jsx`
```jsx
import { Outlet } from "react-router-dom";
import Navbar from "../pages/shared/Navbar";

const Main = () => {
    return (
        <div>
            <Navbar />
            <Outlet />
        </div>
    );
};

export default Main;
```

3. Loader Data

```jsx
loader: rootLoader,
```

```jsx
const { contacts } = useLoaderData();
```

4. Active Link Styling

`NavLink`

**[⬆ Back to Top](#table-of-contents)**



### Context API


###### constext api basic format

```jsx
import { createContext } from "react";

export const AuthContext = createContext();

const AuthProvider = ({children}) => {
    const abcd = 5;

    const authInfo = {
        abcd,
    }

    return (
        <div>
            <AuthContext.Provider value={authInfo}>
                {children}
            </AuthContext.Provider>
        </div>
    );
};

export default AuthProvider;
```

- cover RouterProvider with `<AuthProvider>`

###### get the value from anywhere
```jsx
import { useContext } from "react";
import { AuthContext } from "../../provider/AuthProvider";

const Home = () => {
    const { abcd } = useContext(AuthContext);
    console.log(abcd);

    return (
        <div>
            hello world
        </div>
    );
};

export default Home;
```

**[⬆ Back to Top](#table-of-contents)**



### Axios


###### useAxiosPublic

```jsx
import axios from "axios";

const axiosPublic = axios.create({
    baseURL: 'http://localhost:8000/',
})

const useAxiosPublic = () => {
    return axiosPublic;
};

export default useAxiosPublic;
```





## Deployment



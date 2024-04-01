# Project Name

Brief project description or tagline.

### Table of Contents
- [Overview](#overview)

- [Installation & Setup](#installation--setup)
  - [Backend](#backend)
    - [Mongoose Template](backend/MONGOOSE.md)
  - [Client Side](#client-side)
  - [Firebase](#firebase)

- [React Template](#react-template)
  - [React Router](#react-router)
  - [Context API](#context-api)
  - [Axios](#axios)
  - [React-Hook-Form](#react-hook-form)
  - [Dark Mode](#enable-dark-mode)
  - [DND](#dnd)
  - [imgbb-auto-host](#auto-host-image-on-imgbb)

- [Javascript Template](Javascript.md)
- [Deployment](#deployment)
 
## Overview
Provide a high-level overview of your project. What problem does it solve? Why is it awesome?

Installation & Setup
=======



Backend
------


[Mongoose Template](backend/MONGOOSE.md)


1. Create a 'server' directory.
2. Run the following commands:
```bash
npm init -y
npm i express cors mongodb dotenv
```

3. file structure

File Structuring
----------------------------------------------------------------------------------------------

    .
    ├── ...
    ├── index.js
    ├── db.js    
    ├── routeHandler           
    │   └── users.js              
    │   └── ...         
    └── ...

    
4.  `index.js`
```js
const express = require("express");
const cors = require("cors");
require('dotenv').config();
const app = express();

const port = process.env.PORT || 3000;

const { connectToMongoDB } = require("./db");
const userRoutes = require("./routeHandler/users");


// middleware
app.use(cors());
app.use(express.json());

//connect to mongodb
connectToMongoDB();

// routes
app.use("/api/users", userRoutes);


app.get('/', (req, res) =>{
    res.send("My Task server is running");
})

app.listen(port, () =>{
    console.log(`My Task Server Port: ${port}`);
})
```

<br>

`db.js`
```js
const { MongoClient, ServerApiVersion } = require("mongodb");

const uri = `mongodb+srv://${process.env.DB_USER}:${process.env.DB_PASS}@cluster0.gf8ipgr.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0`;

const client = new MongoClient(uri, {
  serverApi: {
    version: ServerApiVersion.v1,
    strict: true,
    deprecationErrors: true,
  },
});

async function connectToMongoDB() {
  try {
    await client.connect();
    console.log("Connected to MongoDB");
  } catch (error) {
    console.error("Error connecting to MongoDB:", error);
  }
}

module.exports = { client, connectToMongoDB };
```

<br>

`users.js`

```js
const express = require("express");
const { client } = require("../db");
const router = express.Router();

const userCollection = client.db("MyTask").collection("users");


router.get('/', async (req, res) =>{
    const users = await userCollection.find().toArray();
    res.send(users);
})

router.get('/:email', async(req,res) =>{
    const userData = await userCollection.find({
        email : req.params.email
    }).toArray();
})

router.post('/', async(req,res) =>{
    const newUserData = req.body;
    const userExists = await userCollection.findOne({email : newUserData.email})
    newUserData.isAdmin = false;

    if(userExists){
        res.send("User already exists");
    }
    else{
        const result = await userCollection.insertOne(newUserData);
        console.log("new user data : ", newUserData);
        res.json(newUserData);
    }
})

module.exports = router;
```


**[⬆ Back to Top](#table-of-contents)**

<br>



Client Side
------ 

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



<br/>

**[⬆ Back to Top](#table-of-contents)**

<br/>

Firebase
------

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

<br>

**[⬆ Back to Top](#table-of-contents)**

<br>

React Template
========

React Router
------------

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

`Router.jsx`
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

5. Private Routing

`PrivateRouter.jsx`
```jsx
import { useContext } from "react";
import { AuthContext } from "../provider/AuthProvider";
import { Navigate } from "react-router-dom";


const PrivateRouter = ({children}) => {

    const {user, loading} = useContext(AuthContext);
    if(loading) return <p>Loading...</p>;
    // console.log(user);

    if (user) {
        return children;
    }
    else if( user == null ) return <Navigate to="/login" replace />; 
};

export default PrivateRouter;
```



**[⬆ Back to Top](#table-of-contents)**





Context API
--------


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

<br>

**[⬆ Back to Top](#table-of-contents)**

<br>


Axios
---------


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

<br>

**[⬆ Back to Top](#table-of-contents)**

<br>



[React Hook Form](https://react-hook-form.com/get-started)
------------------------------------------------

- install

```
npm install react-hook-form
```

- example

```jsx
import { useForm } from "react-hook-form"

export default function App() {
  const { register, handleSubmit } = useForm()
  const onSubmit = (data) => console.log(data)

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("firstName")} />
      <select {...register("gender")}>
        <option value="female">female</option>
        <option value="male">male</option>
        <option value="other">other</option>
      </select>
      <input type="submit" />
    </form>
  )
}
```

- **Apply validation**

  required, min, max, minLength, maxLength, pattern, validate

  ```jsx
  import { useForm } from "react-hook-form"

  export default function App() {
    const { register, handleSubmit } = useForm()
    const onSubmit = (data) => console.log(data)


    return (
      <form onSubmit={handleSubmit(onSubmit)}>
        <input {...register("firstName", { required: true, maxLength: 20 })} />
        <input {...register("lastName", { pattern: /^[A-Za-z]+$/i })} />
        <input type="number" {...register("age", { min: 18, max: 99 })} />
        <input type="submit" />
      </form>
    )
  }
  ```


<br>

**[⬆ Back to Top](#table-of-contents)**

<br>




Enable Dark Mode
------------------------------------------------

`tailwind.config.js`
  ```js
  plugins: [require("daisyui")],  
  daisyui: {
    themes: ['light', 'dark'],
  },
  darkMode: 'class',
  ```

  <br/>

`ThemeToggler.jsx`
  ```jsx
  import { useEffect, useState } from "react";


  const ThemeToggler = () => {
      const [theme, setTheme] = useState('light');
      
      const toggleTheme = () => {
          setTheme(theme === 'dark' ? 'light' : 'dark');
      };
          // initially set the theme and "listen" for changes to apply them to the HTML tag
      useEffect(() => {
          document.querySelector('html').setAttribute('data-theme', theme);

          // document.querySelector('html').classList.add('dark')
          // document.querySelector('html').classList.remove('dark')

          if(theme === 'dark'){
              document.querySelector('html').classList.add('dark')
          }
          else{
              document.querySelector('html').classList.remove('dark')
          }

      }, [theme]);

      return (
          <label className="swap swap-rotate mx-4 ">
              <input type="checkbox" onClick={toggleTheme} className="theme-controller" value="synthwave" />
              <svg className="swap-on fill-current w-5 h-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M5.64,17l-.71.71a1,1,0,0,0,0,1.41,1,1,0,0,0,1.41,0l.71-.71A1,1,0,0,0,5.64,17ZM5,12a1,1,0,0,0-1-1H3a1,1,0,0,0,0,2H4A1,1,0,0,0,5,12Zm7-7a1,1,0,0,0,1-1V3a1,1,0,0,0-2,0V4A1,1,0,0,0,12,5ZM5.64,7.05a1,1,0,0,0,.7.29,1,1,0,0,0,.71-.29,1,1,0,0,0,0-1.41l-.71-.71A1,1,0,0,0,4.93,6.34Zm12,.29a1,1,0,0,0,.7-.29l.71-.71a1,1,0,1,0-1.41-1.41L17,5.64a1,1,0,0,0,0,1.41A1,1,0,0,0,17.66,7.34ZM21,11H20a1,1,0,0,0,0,2h1a1,1,0,0,0,0-2Zm-9,8a1,1,0,0,0-1,1v1a1,1,0,0,0,2,0V20A1,1,0,0,0,12,19ZM18.36,17A1,1,0,0,0,17,18.36l.71.71a1,1,0,0,0,1.41,0,1,1,0,0,0,0-1.41ZM12,6.5A5.5,5.5,0,1,0,17.5,12,5.51,5.51,0,0,0,12,6.5Zm0,9A3.5,3.5,0,1,1,15.5,12,3.5,3.5,0,0,1,12,15.5Z"/></svg>
              <svg className="swap-off fill-current w-5 h-5" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="M21.64,13a1,1,0,0,0-1.05-.14,8.05,8.05,0,0,1-3.37.73A8.15,8.15,0,0,1,9.08,5.49a8.59,8.59,0,0,1,.25-2A1,1,0,0,0,8,2.36,10.14,10.14,0,1,0,22,14.05,1,1,0,0,0,21.64,13Zm-9.5,6.69A8.14,8.14,0,0,1,7.08,5.22v.27A10.15,10.15,0,0,0,17.22,15.63a9.79,9.79,0,0,0,2.1-.22A8.11,8.11,0,0,1,12.14,19.73Z"/></svg>
          </label>

      );
  };

  export default ThemeToggler;
  ```


<br>

**[⬆ Back to Top](#table-of-contents)**

<br>




DND
----------------------------------------------------------------

#### Basic single column DND

`DND.jsx`

```jsx
import { useState } from "react";
import { DragDropContext, Draggable, Droppable } from "react-beautiful-dnd";


const students = [
    { id: 1, name: 'student1' },
    { id: 2, name: 'student2' },
    { id: 3, name: 'student3' },
    { id: 4, name: 'student4' },
    { id: 5, name: 'student5' },
    { id: 6, name: 'student6' },
]

const DND = () => {

    const [data, setData] = useState(students);

    const handleOnDragEnd = ( result ) =>{
        if(!result.destination) return;
        
        const tempArray = data;
        const [slice] = tempArray.splice(result.source.index, 1);
        tempArray.splice(result.destination.index, 0, slice);

        setData(tempArray);
    }

    return (
        <div>
        <div className="w-1/4 mx-auto">
            
            <DragDropContext onDragEnd={handleOnDragEnd}>
                <Droppable droppableId="dnd3">
                    {
                        (props) =>
                        <ul
                            className=""
                            {...props.droppableProps}
                            ref = {props.innerRef}
                        >
                            {
                                data.map( (x, index) => 
                                <Draggable key={x.id} draggableId={x.id.toString()} index={index}>
                                    {
                                        (props) =>
                                        <li 
                                            ref={props.innerRef}
                                            {...props.draggableProps}
                                            {...props.dragHandleProps}
                                        >
                                            <p>{x.name}</p>
                                        </li>
                                    }
                                </Draggable>
                                )
                            }
                            {props.placeholder}
                        </ul>
                    }
                </Droppable>
            </DragDropContext>
        </div>
        </div>
    );
};

export default DND;
```



<br>

**[⬆ Back to Top](#table-of-contents)**

<br>



Auto Host Image on Imgbb
----------------------------------------------------------------

```js
const image_hosting_key = import.meta.env.VITE_IMAGE_HOSTING_KEY;
const image_hosting_api = `https://api.imgbb.com/1/upload?key=${image_hosting_key}`;


const Register = () => {
  const axiosPublic = useAxiosPublic(); 
  //...

    const handleRegister = async(e) =>{
      e.preventDefault();
      const form = new FormData(e.currentTarget);

      // console.log(form.get('photoURL'));
        const imageFile = {image : form.get('photoURL')};
        const res = await axiosPublic.post(image_hosting_api, imageFile,{
            headers: {
                'content-type': 'multipart/form-data'
            }
        });

        const name = form.get('name');
        // ...
        const photoURL = res.data.data.url;

        const password = //...
        console.log(..., photoURL, .....);
    }
  //...


  return(
    <div>
      // ...

      <form className="w-3/4 md:w-1/2 lg:w-1/3 mx-auto" onSubmit={handleRegister}>
        //......
        {/* Photo */}
        <div className="form-control">
            <label className="label">
                <span className="label-text">Photo URL</span>
            </label>
            <input type="file"  name="photoURL" className="file-input w-full max-w-xs"  required/>
        </div>

        //......

      </form>
    </div>
  )
}
```

<br>

**[⬆ Back to Top](#table-of-contents)**

<br>



## Deployment


<br>

**[⬆ Back to Top](#table-of-contents)**

<br>

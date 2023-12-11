# Project Name

Brief project description or tagline.

## Table of Contents
- [Overview](#overview)
- [Installation & Setup](#installation--setup)
  - [Backend](#backend)
  - [Client Side](#client-side)
  - [Setup MongoDB](#setup-mongodb)
  - [Handling Forms](#handle-form)
  - [Adding Routes](#adding-routes)
  - [Setup Firebase](#setup-firebase)
- [User Authentication](#user-authentication)
  - [Register](#register)
  - [Login](#login)
  - [Create User in Database](#create-user-in-database)
  - [Load All User Data](#load-all-user-data)
- [Deployment](#deployment)
  - [Vercel](#vercel-deploy)
- [Contributing](#contributing)
- [License](#license)

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

### Client Side
1. Use React Router and create a Vite project. Run the following command:
```bash
npm create vite@latest name-of-your-project -- --template react
```
2. Install necessary dependencies. Run the following command:
```bash
cd ..
npm install react-router-dom localforage match-sorter sort-by
```
3. Set up Tailwind CSS and Daisy UI.
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```
4. Add routes and components for the client side.

### Setup MongoDB
1. Set up MongoDB Atlas.
2. Configure environment variables using dotenv.
3. Connect to MongoDB in the server-side code.
4. Perform CRUD operations with MongoDB.

### Handling Forms
1. Create a form for handling data input.
2. Handle form submission and gather data.
3. Send data to the server.

### Adding Routes
1. Create necessary components (Main, Routes, Navbar, Footer).
2. Define routes using React Router.
3. Integrate routes into the main application.

### Setup Firebase
1. Create a Firebase project.
2. Initialize Firebase in the client-side code.
3. Enable email-password and Google authentication.
4. Implement sign-up and login functionality.

## User Authentication

### Register
1. Design and implement the registration form.
2. Handle user registration on the client side.
3. Send user data to the server and store in MongoDB.

### Login
1. Design and implement the login form.
2. Handle user login on the client side.
3. Authenticate with Firebase.

### Create User in Database
1. Set up a user collection in MongoDB.
2. Store user data in the database after registration.

### Load All User Data
1. Create a Users component.
2. Fetch and display user data.

## Deployment

### Vercel Deploy
1. Create `vercel.json` for server deployment.
2. Deploy to Vercel and configure environment variables.

## Contributing
Contribution guidelines and information on how others can contribute to your project.

## License
This project is licensed under the [Your License] License - see the [LICENSE.md](LICENSE.md) file for details.

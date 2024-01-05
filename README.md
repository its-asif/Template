# Project Name

Brief project description or tagline.

### Table of Contents
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

**[⬆ Back to Top](#table-of-contents)**


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

**[⬆ Back to Top](#table-of-contents)**

### Setup MongoDB


### Handling Forms


### Adding Routes


### Setup Firebase



**[⬆ Back to Top](#table-of-contents)**


## User Authentication

### Register
1. 

### Login
1. 

### Create User in Database
1. 

### Load All User Data
1. 

## Deployment

### Vercel Deploy
1. Create `vercel.json` for server deployment.
2. Deploy to Vercel and configure environment variables.

## Contributing
Contribution guidelines and information on how others can contribute to your project.

## License
This project is licensed under the [Your License] License - see the [LICENSE.md](LICENSE.md) file for details.

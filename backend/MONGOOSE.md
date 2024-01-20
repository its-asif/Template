[Go to Home](../README.md)

Mongoose Template
===============================

## Table of Contents
- [Installation](#installation)
- [File Structuring](#file-structuring)
- [Format](#format)
- [Guide](#guide)
    - [GET](#get-data)
    - [POST](#post-data)
    - [PUT](#put-data)
    - [DELETE](#delete-data)



<br>

Installation
---------------------------------------------------------------------------------------------
```
    npm i mongoose express nodemon
```


<br>

**[⬆ Back to Top](#table-of-contents)**


File Structuring
----------------------------------------------------------------------------------------------

    .
    ├── ...
    ├── index.js
    ├── ...
    ├── schema           
    │   └── todoSchema.js             
    │   └── ...    
    ├── routeHandler           
    │   └── todoHandler.js              
    │   └── ...         
    └── ...

<br>

**[⬆ Back to Top](#table-of-contents)**


Format
----------------------------------------------------------------------------------------------


`index.js`

```js
const express = require('express');
const mongoose = require('mongoose');
const todoHandler = require('./routeHandler/todoHandler');

// express app initialization
const app = express();
app.use(express.json());

// database connection with mongoose

mongoose.connect("mongodb+srv://<username>:<password>@cluster0.eyhty.mongodb.net/<database_name>?retryWrites=true&w=majority")
    .then( () => console.log("Connected to MongoDB"))
    .catch( err => console.log("Error connecting to MongoDB", err));
 

// application routes
app.use('/todos', todoHandler);



// default error handler
function errorHandler(err, req, res, next) {
    
    if( res.headersSent ) {
        return next(err);
    }
    res.status(500).json({ error: err });
}

app.listen(3000, () => {
    console.log('Server started on port 3000');
}); 
```

<br/>

`todoHandler.js`
```js
const express = require('express')
const mongoose = require('mongoose')
const router = express.Router() // router is a middleware which is used to handle the routes
const todoSchema = require('../schemas/todoSchema')

const Todo = new mongoose.model('Todo', todoSchema) // "Todo" is the collection name


// Get all the todos
router.get('/', async (req, res) => {
    
})

// Get a single todo by ID
router.get('/:id', async(req, res) => {
    
})

// POST / Create a new todo
router.post('/', async(req, res) => {
    
})

// Post multiple todos
router.post('/all', async(req, res) => {
    
})

// PUT / Update a todo by ID
router.put('/:id', async(req, res) => {
    
})

// DELETE / Delete a todo by ID
router.delete('/:id', async(req, res) => {
    
})

module.exports = router;
```


<br/>

`todoSchema.js`

```js
const mongoose = require('mongoose');

const todoSchema = mongoose.Schema({
    title: {
        type: String,
        required: true,
    },
    description: String,
    status: {
        type: String,
        enum: ['active', 'inactive'],   // enum is used to restrict the value of a field
    },
    date: {
        type: Date,
        default: Date.now,
    },
})

module.exports = todoSchema;
```




<br>


**[⬆ Back to Top](#table-of-contents)**

Guide
----------------------------------------------------------------------------------------------

#### Get Data

###### Get all data
```js
router.get('/', async (req, res) => {
    try{
        const todos = await Todo.find({
            status: 'active',
        })
        // .select({
        //     _id: 0,
        //     date: 0,
        // })
        // .limit(3)
        res.status(200).json({
            todos
        })
    } catch (err) {
        res.status(500).json({
            error : "There was a server side error!",
        })
    }
})
```


###### Get single Data by ID
```js
router.get('/:id', async(req, res) => {
    try{
        const todos = await Todo.find({
            _id: req.params.id,
        })
        res.status(200).json({
            todos
        })
    } catch (err) {
        res.status(500).json({
            error : "There was a server side error!",
        })
    }
})
```


#### POST Data

###### Create a new todo
```js
router.post('/', async(req, res) => {
    console.log(req.body)
    try{
        const newTodo = new Todo(req.body);
        await newTodo.save();

        res.status(200).json({
            message : "Todo was inserted successfully!",
        })
    } catch (err) {
        res.status(500).json({
            error : "There was a server side error!",
        })
    }
})
```


###### Create multiple todos
```js
router.post('/all', async(req, res) => {
    try{
        await Todo.insertMany(req.body);
        res.status(200).json({
            message : "Todos were inserted successfully!",
        })
    
    }   catch (err) {
        res.status(500).json({
            error : "There was a server side error!",
        })
    }
})
```


##### PUT Data

```js
router.put('/:id', async(req, res) => {
    console.log("updated", req.params.id)
    try{
        await Todo.findOneAndUpdate(
            { _id: req.params.id },
            { $set: {
                status: 'active',
            } 
            },
        )
        res.status(200).json({
            message : "Todo was updated successfully!",
        })
    }
    catch (err) {
        res.status(500).json({
            error : "There was a server side error!",
        })
    }
})
```


#### DELETE Data

```js
router.delete('/:id', async(req, res) => {
    try{
        const todos = await Todo.deleteOne({
            _id: req.params.id,
        })
        res.status(200).json({
            todos
        })
    } catch (err) {
        res.status(500).json({
            error : "There was a server side error!",
        })
    }
})
```


**[⬆ Back to Top](#table-of-contents)**
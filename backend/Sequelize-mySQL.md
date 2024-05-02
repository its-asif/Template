[Go to Home](../README.md)

Sequelize x MySQL Template
===============================

## Table of Contents
- [Installation & Initialization](#installation--initialization)
- [File Structuring](#file-structuring)
- [Format](#format)
    - [server.js](#serverjs)
    - [config.js](#configjs)
    - [blog.js](#blogjs)
    - [user.js](#userjs)
    - [userroute.js](#userroutejs)
    - [usercontroller.js](#usercontrollerjs)
- [Guide](#guide)
    - [GET](#get-data)
    - [POST](#post-data)
    - [PUT](#put-data)
    - [DELETE](#delete-data)




<br>

Installation & Initialization
---------------------------------------------------------------------------------------------
```cmd
npm i sequelize mysql2 express fs path dotenv
```

```cmd
npm install --save-dev sequelize-cli
```

```cmd
npx sequelize-cli init
```

extra ( if you want ):
```cmd
npm i bcrypt cors
```

<br>

**[⬆ Back to Top](#table-of-contents)**



File Structuring
----------------------------------------------------------------------------------------------

    .
    ├── ...
    ├── server.js
    ├── ...
    │
    ├── config           
    │   └── config.js  
    │   
    ├── models           
    │   └── index.js              
    │   └── User.js              
    │   └── ...  
    │       
    ├── src/routes           
    │   └── router.js 
    │   │  
    │   ├── users           
    │   │   └── userController.js              
    │   │   └── userRoute.js              
    │   └── ...         
    └── ...

<br>

**[⬆ Back to Top](#table-of-contents)**


Format
----------------------------------------------------------------------------------------------



##### `server.js`


```js
const express = require('express');
const app = express();
const PORT = process.env.PORT || 8000;
const db = require('./models');


// middleware
app.use(express.json());


// router
const router = require('./src/routes/router');
app.use('/api', router);


db.sequelize.sync().then(() => {
    app.listen(PORT, () => {
        console.log(`Server is running on port ${PORT}`);
    });
}).catch(err => {
    console.log(err);
});
```

<br>

**[⬆ Back to Top](#table-of-contents)**



##### `config.js`

* change `config.json` to `config.js` inside `models/index.js` 

```js
require('dotenv').config();

module.exports = {
  "development": {
    "username": process.env.DB_user,
    "password": process.env.DB_pass,
    "database": process.env.DB_name,
    "host": "localhost",
    "dialect": "mysql"
  },
  "test": {
    "username": process.env.DB_user,
    "password": process.env.DB_pass,
    "database": process.env.DB_name,
    "host": "localhost",
    "dialect": "mysql"
  },
  "production": {
    "username": process.env.DB_user,
    "password": process.env.DB_pass,
    "database": process.env.DB_name,
    "host": "localhost",
    "dialect": "mysql"
  }
}
```


<br>

**[⬆ Back to Top](#table-of-contents)**


##### `Blog.js`

```js

module.exports = (sequelize, DataTypes) => {
    const Blog = sequelize.define('Blog', {
        blog_id: {
            type: DataTypes.INTEGER,
            primaryKey: true,
            autoIncrement: true
        },
        title: {
            type: DataTypes.STRING,
            allowNull: false,
            validate: {
                len: [4, 30]
            }
        },
        subtitle: {
            type: DataTypes.STRING,
            allowNull: true,
            validate: {
                len: [4, 100]
            }
        },
        content: {
            type: DataTypes.STRING,
            allowNull: false,
        },
        tags: {
            type: DataTypes.STRING,
            allowNull: true,
            get() {
                return this.getDataValue('tags').split(';')
            },
            set(val) {
                this.setDataValue('tags', val.join(';'));
            }
        },
        status: {
            type : DataTypes.ENUM('public', 'private'),
            allowNull: false,
            defaultValue: 'public'
        },
        user_id: {
            type: DataTypes.INTEGER,
            allowNull: false,
        },
        created_at: {
            type: DataTypes.DATE,
            defaultValue: DataTypes.NOW
        }
    }, {
        freezeTableName: true,
        timestamps: true
    });

    Blog.associate = (models) => {
        const { User, Comment } = models;
        Blog.belongsTo(User, { foreignKey: 'user_id', targetKey: 'user_id' });
        Blog.hasMany(Comment, { foreignKey: 'blog_id' })
    };

    return Blog;
}
```


<br>

##### `User.js`

```js
const bcrypt = require('bcrypt');

module.exports = (sequelize, DataTypes) => {
    const User = sequelize.define('User', {
        user_id: {
            type: DataTypes.INTEGER,
            primaryKey: true,
            autoIncrement: true
        },
        username: {
            type: DataTypes.STRING,
            allowNull: false,
            validate: {
                len: [4,10]
            }
        },
        password: {
            type: DataTypes.STRING,
            allowNull: false,
        },
        age: {
            type: DataTypes.INTEGER,
            defaultValue: 21,
            allowNull: false,
        }
    },
    {
        freezeTableName: true,
        timestamps: false
    })

    // Before saving the user, hash the password
    User.beforeCreate(async (user) => {
        const hashedPassword = await bcrypt.hash(user.password, 10);
        user.password = hashedPassword;
    });
    

    User.associate = (models) => {
        const { Blog, Comment } = models;
        User.hasMany(Blog, { foreignKey: 'user_id' });
        User.hasMany(Comment, { foreignKey: 'user_id' });
    };

    return User;
}
```


<br>

**[⬆ Back to Top](#table-of-contents)**


##### `userRoute.js`

```js
const router = require('express').Router();
const { getAllUsers, getUserById, createUser, deleteUser, updateUserAge, getUserBlogs } = require('./userController');


router.get('/', getAllUsers);

router.get('/:id', getUserById);

router.get('/blogs/:id', getUserBlogs);

router.post('/', createUser);

router.put('/:id', updateUserAge);

router.delete('/:id', deleteUser);



module.exports = router;
```


<br>

**[⬆ Back to Top](#table-of-contents)**


##### `userController.js`

```js
const {User, Blog} = require('./../../../models');

const getAllUsers = async (req, res) => {
    try {
        const users = await User.findAll();
        res.json(users);

    } catch (error) {
        console.log(error);
    }
};

const getUserById = async (req, res) => {
    try {
        const user = await User.findByPk(req.params.id);
        res.json(user);
    } catch (error) {
        console.log(error);
    }
};

const getUserBlogs = async (req, res) => {
    const user_id = req.params.id;
    try {
        const user = await User.findByPk(user_id, {
            include: Blog
        });
        res.json(user);
    } catch (error) {
        console.log(error);
    }
};

const createUser = async (req, res) => {
    try {
        const user = {
            username: req.body.username,
            password: req.body.password,
            age: req.body.age
        };
        console.log(user);

        const newUser = await User.create(user);
        res.json(newUser);
    } catch (error) {
        console.log(error);
        if( error.name === 'SequelizeValidationError' ) {
            return res.status(400).json({ message: error.errors[0].message });
        }

        res.status(500).json({ message: 'Server Error' });
    }
};

const updateUserAge = async (req, res) => {
    try {
        await User.update({
            age: req.body.age
        }, {
            where : {
                user_id : req.params.id
            }
        });
        res.json({ message: 'User updated successfully' });
    } catch (error) {
        console.log(error);
        res.status(500).json({ message: 'Server Error' });
    }
}

const deleteUser = async (req, res) => {
    try {
        await User.destroy({
            where : {
                user_id : req.params.id
            }
        });
        res.json({ message: 'User deleted successfully' });
    } catch (error) {
        console.log(error);
        res.status(500).json({ message: 'Server Error' });
    }
}

module.exports = {
    getAllUsers,
    getUserById,
    createUser,
    updateUserAge,
    deleteUser,
    getUserBlogs,
};
```

<br>

**[⬆ Back to Top](#table-of-contents)**



Guide
----------------------------------------------------------------------------------------------

#### Get Data

###### Get all data

```js
const getAllBlogs = async (req, res) =>{
    try {
        
        const blogs = await Blog.findAll();
        res.json(blogs);

    } catch (error) {
        console.log(error);
        res.status(500).json({ message: 'Server Error' });
    }
}
```

<br>

###### Get by id

```js
await Model.findByPk( req.params.id );
```

<br>


###### Get by some other criteria

```js
await Model.findAll({
            where: {
                some_id
            }
        });
```

<br>


###### Get blog with comments (inner/left/right join)

```js
const blog = await Blog.findByPk(blog_id, {
            include: Comment
        });
```

<br>

**[⬆ Back to Top](#table-of-contents)**



#### POST data

###### post data

```js
await Comment.create(req.body);
```

###### handle SequelizeValidationError

```js
catch (error) {
    console.log(error);
    if( error.name === 'SequelizeValidationError' ) {
        return res.status(400).json({ message: error.errors[0].message });
    }
    res.status(500).json({ message: 'Server Error' });
}
```

<br>

**[⬆ Back to Top](#table-of-contents)**




#### UPDATE data

###### edit data

```js
await Model.update({
            field: req.body.field_name
        }, {
            where : {
                some_id : req.params.id
            }
        });
```

<br>

**[⬆ Back to Top](#table-of-contents)**




#### DELETE data

###### delete data

```js
await Model.destroy({
            where : {
                some_id : req.params.id
            }
        });
```

<br>

**[⬆ Back to Top](#table-of-contents)**


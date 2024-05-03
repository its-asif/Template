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

### Get Data


1. **Finding all records:**
   You can use the `findAll` method to retrieve all records from a table.

   ```javascript
   const allUsers = await User.findAll();
   console.log('All users:', allUsers);
   ```

2. **Finding records with conditions:**
   You can use the `findAll` method with a `where` clause to retrieve records that meet specific conditions.

   ```javascript
   const specificUsers = await User.findAll({
     where: {
       firstName: 'John',
     },
   });
   console.log('Specific users:', specificUsers);
   ```

3. **Finding the first record:**
   You can use the `findOne` method to retrieve the first record that matches the specified conditions.

   ```javascript
   const firstUser = await User.findOne();
   console.log('First user:', firstUser);
   ```

4. **Finding a record by primary key:**
   If your table has a primary key, you can use the `findByPk` method to retrieve a record by its primary key value.

   ```javascript
   const userById = await User.findByPk(1);
   console.log('User by ID:', userById);
   ```

5. **Counting records:**
   You can use the `count` method to get the count of records that match the specified conditions.

   ```javascript
   const userCount = await User.count();
   console.log('Total users:', userCount);
   ```

6. **Limiting the number of records:**
   You can use the `limit` option to limit the number of records returned.

   ```javascript
   const limitedUsers = await User.findAll({ limit: 10 });
   console.log('Limited users:', limitedUsers);
   ```

7. **Ordering records:**
   You can use the `order` option to specify the order in which records are returned.

   ```javascript
   const orderedUsers = await User.findAll({ order: [['createdAt', 'DESC']] });
   console.log('Ordered users:', orderedUsers);
   ```

8. **Finding records with attributes selection:**
   You can specify which attributes you want to retrieve using the `attributes` option.

   ```javascript
   const selectedUsers = await User.findAll({
     attributes: ['firstName', 'lastName'],
   });
   console.log('Selected users:', selectedUsers);
   ```

9. **Finding records with inclusion of associated models:**
   If your models have associations, you can include associated models in the query results using the `include` option. [ left/right/inner join]

   ```javascript
   const usersWithPosts = await User.findAll({
     include: [{ model: Post }],
   });
   console.log('Users with posts:', usersWithPosts);
   ```

10. **Finding records with exclusion of attributes:**
    You can exclude certain attributes from the query results using the `attributes` option with the exclude array.

    ```javascript
    const usersWithoutEmails = await User.findAll({
      attributes: { exclude: ['email'] },
    });
    console.log('Users without emails:', usersWithoutEmails);
    ```

11. **Finding records with pagination:**
    You can implement pagination by using the `offset` and `limit` options.

    ```javascript
    const page = 1;
    const pageSize = 10;
    const paginatedUsers = await User.findAll({
      offset: (page - 1) * pageSize,
      limit: pageSize,
    });
    console.log('Paginated users:', paginatedUsers);
    ```

12. **Finding records with group by and having clause:**
    Sequelize supports `group` and `having` options for grouping query results and applying conditions to grouped data.

    ```javascript
    const groupedUsers = await User.findAll({
      attributes: ['department', [sequelize.fn('COUNT', sequelize.col('id')), 'userCount']],
      group: ['department'],
      having: sequelize.where(sequelize.fn('COUNT', sequelize.col('id')), '>', 5),
    });
    console.log('Grouped users:', groupedUsers);
    ```

13. **Finding records with raw SQL queries:**
    Sequelize allows you to execute raw SQL queries if you need to perform complex operations that can't be achieved using Sequelize's built-in methods.

    ```javascript
    const [results, metadata] = await sequelize.query('SELECT * FROM users WHERE age > 18');
    console.log('Raw query results:', results);
    ```

    Just be cautious when using raw SQL queries to avoid SQL injection vulnerabilities.

14. **Finding records with complex conditions:**
    Sequelize provides various operators like `Op.and`, `Op.or`, `Op.gt`, `Op.lt`, etc., to build complex conditions in your queries.

    ```javascript
    const { Op } = require('sequelize');
    const usersAbove18 = await User.findAll({
      where: {
        [Op.and]: [
          { age: { [Op.gt]: 18 } },
          { isAdmin: false },
        ],
      },
    });
    console.log('Users above 18 who are not admins:', usersAbove18);
    ```


<br>

**[⬆ Back to Top](#table-of-contents)**



### POST data


1. **Creating a single record:**
   You can use the `create` method to insert a new record into the database.

   ```javascript
   const newUser = await User.create({
     firstName: 'John',
     lastName: 'Doe',
     email: 'john@example.com',
   });
   console.log('New user created:', newUser);
   ```

2. **Creating multiple records:**
   If you have an array of data, you can use the `bulkCreate` method to insert multiple records at once.

   ```javascript
   const newUsers = await User.bulkCreate([
     { firstName: 'Jane', lastName: 'Doe', email: 'jane@example.com' },
     { firstName: 'Alice', lastName: 'Smith', email: 'alice@example.com' },
   ]);
   console.log('New users created:', newUsers);
   ```

3. **Creating associated records:**
   If your models have associations, you can create associated records along with the main record.

   ```javascript
   const newUserWithPosts = await User.create({
     firstName: 'John',
     lastName: 'Doe',
     email: 'john@example.com',
     Posts: [
       { title: 'First Post', content: 'This is the content of the first post.' },
       { title: 'Second Post', content: 'This is the content of the second post.' },
     ],
   }, {
     include: [Post],
   });
   console.log('New user with posts created:', newUserWithPosts);
   ```

4. **Handling validation errors:**
   If your model has validation rules defined, Sequelize will throw an error if the data doesn't meet those rules. You can handle these errors using try-catch blocks.

   ```javascript
   try {
     const invalidUser = await User.create({ email: 'invalid-email' });
     console.log('New user created:', invalidUser);
   } catch (error) {
     console.error('Error creating user:', error.message);
   }
   ```


5. **Creating records with associations using nested includes:**
   If your models have nested associations, you can use nested includes to create records and their associated records in a single operation.

   ```javascript
   const newAuthorWithBooks = await Author.create({
     name: 'John Doe',
     Books: [
       { title: 'Book 1', genre: 'Fiction' },
       { title: 'Book 2', genre: 'Non-fiction' },
     ],
   }, {
     include: [{ model: Book, as: 'Books' }],
   });
   console.log('New author with books created:', newAuthorWithBooks);
   ```

6. **Creating records with transaction:**
   If you need to ensure that multiple operations are executed atomically (i.e., either all of them succeed or none of them succeed), you can use transactions.

   ```javascript
   const t = await sequelize.transaction();
   try {
     const newUser = await User.create({
       firstName: 'John',
       lastName: 'Doe',
       email: 'john@example.com',
     }, { transaction: t });

     const newProfile = await Profile.create({
       userId: newUser.id,
       bio: 'This is John Doe\'s profile.',
     }, { transaction: t });

     await t.commit();
     console.log('New user and profile created:', newUser, newProfile);
   } catch (error) {
     await t.rollback();
     console.error('Error creating user and profile:', error);
   }
   ```

7. **Creating records with custom hooks:**
   Sequelize allows you to define hooks that are executed before or after certain lifecycle events (such as beforeCreate, afterCreate, etc.). You can use these hooks to perform custom logic before or after creating records.

   ```javascript
   User.addHook('beforeCreate', (user, options) => {
     console.log('Before creating user:', user);
   });

   const newUser = await User.create({
     firstName: 'John',
     lastName: 'Doe',
     email: 'john@example.com',
   });
   ```




<br>

**[⬆ Back to Top](#table-of-contents)**




### UPDATE data


1. **Updating a single record:**
   You can use the `update` method to update a single record in the database.

   ```javascript
   const [numRowsAffected, updatedUser] = await User.update(
     { firstName: 'Jane', lastName: 'Doe' }, // New data
     { where: { id: 1 } } // Condition
   );
   console.log('Number of rows affected:', numRowsAffected);
   console.log('Updated user:', updatedUser);
   ```

2. **Updating multiple records:**
   You can also update multiple records that match certain conditions using the `update` method.

   ```javascript
   const [numRowsAffected, updatedUsers] = await User.update(
     { status: 'active' }, // New data
     { where: { department: 'IT' } } // Condition
   );
   console.log('Number of rows affected:', numRowsAffected);
   console.log('Updated users:', updatedUsers);
   ```

3. **Updating records with associations:**
   If your models have associations, you can update associated records along with the main record.

   ```javascript
   const updatedUser = await User.findByPk(1);
   updatedUser.firstName = 'Jane';
   updatedUser.lastName = 'Doe';
   await updatedUser.save();
   ```

4. **Handling validation errors:**
   Just like with "POST" operations, if your model has validation rules defined, Sequelize will throw an error if the updated data doesn't meet those rules.

   ```javascript
   try {
     const updatedUser = await User.update(
       { email: 'invalid-email' },
       { where: { id: 1 } }
     );
     console.log('Updated user:', updatedUser);
   } catch (error) {
     console.error('Error updating user:', error.message);
   }
   ```



<br>

**[⬆ Back to Top](#table-of-contents)**




### DELETE data


1. **Deleting a single record:**
   You can use the `destroy` method to delete a single record from the database.

   ```javascript
   const numRowsDeleted = await User.destroy({
     where: { id: 1 } // Condition
   });
   console.log('Number of rows deleted:', numRowsDeleted);
   ```

2. **Deleting multiple records:**
   You can also delete multiple records that match certain conditions using the `destroy` method.

   ```javascript
   const numRowsDeleted = await User.destroy({
     where: { department: 'IT' } // Condition
   });
   console.log('Number of rows deleted:', numRowsDeleted);
   ```

3. **Deleting records with associations:**
   If your models have associations, you can delete associated records along with the main record.

   ```javascript
   const userToDelete = await User.findByPk(1);
   await userToDelete.destroy();
   ```




<br>

**[⬆ Back to Top](#table-of-contents)**


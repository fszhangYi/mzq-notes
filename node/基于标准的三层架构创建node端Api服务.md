基于标准的三层架构（Controller, Service, Model）可以创建一个简单的功能：用户注册。这将涉及到用户模型（User Model），用户服务（User Service），以及用户控制器（User Controller）。

[总述传送门](https://juejin.cn/post/7324296963137830946)

[项目GitHub地址](https://github.com/fszhangYi/my-koa-project)

### 1. Model: 用户模型（User Model）

文件路径: `app/models/user.js`

```javascript
const Sequelize = require('sequelize');
const initializeDatabase = require('../../config/database');

const User = async () => {
    const sequelize = await initializeDatabase();
    const User = await sequelize.define('user', {
        name: {
            type: Sequelize.STRING,
            allowNull: false
        },
        email: {
            type: Sequelize.STRING,
            allowNull: false,
            unique: true
        }
    });
    await sequelize.sync(); // important
    return sequelize.models.user;
}

module.exports = User;
```

文件路径: `app/models/index.js`

```js
const User = require('./user');

module.exports = {User};
```

### 2. Service: 用户服务（User Service）

文件路径: `app/services/userService.js`

```javascript
const {User} = require('../models');

const createUser = async (name, email) => {
  try {
    const _ = await User();
    const createdAt = +new Date();
    const updatedAt = +new Date();
    const user = await _.create({ name, email, createdAt, updatedAt });
    return user;
  } catch (error) {
    // 错误处理逻辑
    throw error;
  }
};

const getUsers = async () => {
  try {
    const _ = await User();
    const users = await _.findAll();
    return users;
  } catch (error) {
    // 错误处理逻辑
    throw error;
  }
};

module.exports = {
  createUser,
  getUsers,
};
```

### 3. Controller: 用户控制器（User Controller）

文件路径: `app/controllers/userController.js`

```javascript
const userService = require('../services/userService');

const register = async (ctx) => {
  const { name, email } = ctx.request.body;
  try {
    const user = await userService.createUser(name, email);
    ctx.body = { message: 'User created successfully', user };
  } catch (error) {
    ctx.status = 400;
    ctx.body = { message: error.message };
  }
};

const getUsers = async (ctx) => {
  try {
    const users = await userService.getUsers();
    ctx.body = { message: 'User created successfully', users };
  } catch (error) {
    ctx.status = 400;
    ctx.body = { message: error.message };
  }
};

module.exports = {
  register,
  getUsers,
};

```

### 4. 路由和应用程序入口

首先，创建一个简单的路由处理器。

文件路径: `app/routes/user.js`

```javascript
const Router = require('koa-router');
const userController = require('../controllers/userController.js');

const router = new Router();

router.post('/register', userController.register);
router.get('/users', userController.getUsers);

module.exports = router;

```

然后，在应用程序入口文件中配置Koa应用。

文件路径: `app.js`

```javascript
const Koa = require('koa');
const {koaBody} = require('koa-body');
const router = require('./app/routes/user');
const initializeDatabase = require('./config/database');

const app = new Koa();

// 中间件
app.use(koaBody({ multipart: true }));
app.use(router.routes()).use(router.allowedMethods());

// 数据库连接
initializeDatabase().then(
    sequelize => {
        sequelize.sync()
        .then(() => {
          console.log('Database connected.');
        })
        .catch((err) => {
          console.error('Unable to connect to the database:', err);
        });
    }
)


// 服务器启动
const port = 6666;
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});

module.exports = app;

```


最后，配置数据库相关信息。

文件路径: `config/database.js`
```js
const Sequelize = require('sequelize');

// 请根据你的数据库配置填写以下信息
const dbName = 'xxx'; // 数据库名
const dbUser = 'root'; // 数据库用户
const dbPassword = '66666@'; // 数据库密码
const dbHost = 'localhost'; // 数据库主机地址
const dbDialect = 'mysql'; // 数据库类型，可以是mysql、postgres等

async function initializeDatabase() {
  let sequelize = {};
  try {
    const url = `mysql://${dbUser}:${dbPassword}@${dbHost}`;
    console.log('url:', url)
    sequelize = new Sequelize(url, {
      host: dbHost,
      dialect: dbDialect,
      logging: false,
    });

    // 创建数据库（如果不存在）
    await sequelize.query(`CREATE DATABASE IF NOT EXISTS ${dbName};`);
    console.log('Database checked/created successfully');

    // 关闭初始连接
    await sequelize.close();

    // 重新连接到新创建的数据库
    sequelize = new Sequelize(dbName, dbUser, dbPassword, {
      host: dbHost,
      dialect: dbDialect,
      logging: false,
    });
  } catch(e){
    throw new Error(e.message);
  } finally {
    return sequelize;
  }
}

module.exports = initializeDatabase;
```

在这个示例中，创建了一个简单的用户注册功能，包括用户模型（`User`），用户服务（`userService`），和用户控制器（`userController`）。用户通过发送POST请求到`/register`路由来注册，其中包含他们的用户名和密码。服务层（`userService`）处理创建用户的逻辑，而模型层（`User`）定义了用户数据的结构。

本文所示的例子是一个基本的三层架构应用，展示了如何将不同的关注点（路由处理、业务逻辑、数据访问）分离。

---

# English version
Creating a Simple User Registration Feature Using the Standard Three-Tier Architecture (Controller, Service, Model)

This implementation involves a user model (User Model), user service (User Service), and user controller (User Controller).

[Overview Portal](https://juejin.cn/post/7324296963137830946)

### 1. Model: User Model

File Path: `app/models/user.js`

```javascript
const Sequelize = require('sequelize');
const sequelize = require('../../config/database');

const User = sequelize.define('User', {
  username: {
    type: Sequelize.STRING,
    unique: true,
    allowNull: false
  },
  password: {
    type: Sequelize.STRING,
    allowNull: false
  }
});

module.exports = User;
```

### 2. Service: User Service

File Path: `app/services/userService.js`

```javascript
const User = require('../models/user');

const createUser = async (username, password) => {
  try {
    const user = await User.create({ username, password });
    return user;
  } catch (error) {
    // Error handling logic
    throw error;
  }
};

module.exports = {
  createUser
};
```

### 3. Controller: User Controller

File Path: `app/controllers/userController.js`

```javascript
const userService = require('../services/userService');

const register = async (ctx) => {
  const { username, password } = ctx.request.body;
  try {
    const user = await userService.createUser

(username, password);
    ctx.body = { message: 'User created successfully', user };
  } catch (error) {
    ctx.status = 400;
    ctx.body = { message: error.message };
  }
};

module.exports = {
  register
};
```

### 4. Routing and Application Entry

First, create a simple route handler.

File Path: `app/routes.js`

```javascript
const Router = require('koa-router');
const userController = require('./controllers/userController');

const router = new Router();

router.post('/register', userController.register);

module.exports = router;
```

Then, configure the Koa application in the application entry file.

File Path: `app.js`

```javascript
const Koa = require('koa');
const bodyParser = require('koa-bodyparser');
const router = require('./app/routes');
const sequelize = require('./config/database');

const app = new Koa();

// Middleware
app.use(bodyParser());
app.use(router.routes()).use(router.allowedMethods());

// Database connection
sequelize.sync()
  .then(() => {
    console.log('Database connected.');
  })
  .catch((err) => {
    console.error('Unable to connect to the database:', err);
  });

// Server startup
const port = 3000;
app.listen(port, () => {
  console.log(`Server is running on http://localhost:${port}`);
});

module.exports = app;
```

In this example, a simple user registration functionality is

created, including a user model (`User`), user service (`userService`), and user controller (`userController`). Users register by sending a POST request to the `/register` route, which includes their username and password. The service layer (`userService`) handles the logic for creating the user, while the model layer (`User`) defines the structure of user data.

The example shown in this article is a basic three-tier architecture application that demonstrates how to separate different concerns (route handling, business logic, data access).

本文演示几个在nodejs环境下使用sequelize操作mysql的高级操作：复杂查询、管理事务、动态模型操作、高级关联和性能优化。
### 第1节：使用Sequelize进行复杂查询

#### 示例1：使用`where`进行高级筛选
```javascript
const User = require('./models/user');

// 查找年龄超过30岁且具有特定职位称谓的用户
User.findAll({
  where: {
    age: {
      [Sequelize.Op.gt]: 30
    },
    jobTitle: 'Software Engineer'
  }
});
```

#### 示例2：使用`include`进行深度加载关联
```javascript
const Post = require('./models/post');
const Author = require('./models/author');

// 获取文章及其作者的详细信息
Post.findAll({
  include: [{
    model: Author,
    required: true
  }]
});
```

#### 示例3：使用`order`和`group`进行结果排序和分组
```javascript
const Order = require('./models/order');

// 获取按状态分组并按创建日期排序的订单
Order.findAll({
  group: 'status',
  order: [
    ['createdAt', 'ASC']
  ]
});
```

### 第2节：管理事务

#### 示例1：基本事务语法
```javascript
const sequelize = require('./sequelize');

async function transactionExample() {
  const t = await sequelize.transaction();

  try {
    // 在此处进行事务操作

    await t.commit();
  } catch (error) {
    await t.rollback();
  }
}
```

#### 示例2：错误时实现回滚
```javascript
async function createUserWithTransaction() {
  const t = await sequelize.transaction();

  try {
    // 创建用户
    await User.create({ name: 'John Doe' }, { transaction: t });

    // 若发生错误，则事务将回滚
    throw new Error('糟糕！出了点问题。');

    await t.commit();
  } catch (error) {
    await t.rollback();
  }
}
```

#### 示例3：链式多事务操作
```javascript
async function complexTransaction() {
  const t = await sequelize.transaction();

  try {
    await User.create({ name: 'Alice' }, { transaction: t });
    await Order.create({ productId: 1, userId: 2 }, { transaction: t });

    await t.commit();
  } catch (error) {
    await t.rollback();
  }
}
```

### 第3节：动态模型操作

#### 示例1：向模型添加属性
```javascript
// 假设User模型已定义
User.addAttribute('nickname', Sequelize.STRING);
```

#### 示例2：动态更改验证规则
```javascript
// 更新User模型中电子邮件字段的验证
User.attributes.email.validate = {
  isEmail: true,
  notEmpty: true
};
```

#### 示例3：实现模型级自定义函数
```javascript
User.prototype.getFullName = function() {
  return this.firstName + ' ' + this.lastName;
};

const user = await User.findOne({ where: { id: 1 } });
console.log(user.getFullName());
```

### 第4节：高级关联技术

#### 示例1：设置多态关联
```javascript
// 假设有Comment和Post模型
Comment.belongsTo(Post, { as: 'commentable', constraints: false, allowNull: true, foreignKey: 'commentableId' });

// 用法：获取某篇文章的评论
Post.findOne({
  where: { id: 1 },
  include: [{
    model: Comment,
    as: 'commentable'
  }]
});
```

#### 示例2：带有附加属性的多对多关系
```javascript
// User和Project为模型；UserProjects为连接表
User.belongsToMany(Project, { through: UserProjects });
Project.belongsToMany(User, { through: UserProjects });

UserProjects.belongsTo(User);
UserProjects.belongsTo(Project);
UserProjects.hasOne(Role); // Role为另一个模型
```

#### 示例3：使用关联范围筛选相关数据
```javascript
User.hasMany(Post, {
  scope: {
    published: true
  }
});

// 只获取发布文章的用户
User.findOne({
  include: [{
    model: Post
  }]
});
```

### 第5节：性能优化

#### 示例1：使用急切加载减少数据库查询
```javascript
User.findAll({
  include: [Post]
});
```

#### 示例2：使用原始查询进行复杂操作
```javascript
sequelize.query("SELECT * FROM Users WHERE status = 'active'", {
  type: Sequelize.QueryTypes.SELECT
});
```

#### 示例3：使用Redis缓存频繁查询
```javascript
const redis = require('redis');
const client = redis.createClient();

// 获取并缓存一个用户
const cachedUser = await client.get('user_1');
if (cachedUser) {
  return JSON.parse(cachedUser);
} else {
  const user = await User.findByPk(1);
  client.set('user_1', JSON.stringify(user));
  return user;
}
```

这些示例展示了Sequelize与MySQL在复杂查询、事务管理、模型操作、关联技术和性能优化方面的高级用例。实施细节可能会根据具体的应用环境和Sequelize配置有所不同。这些示例为开发者提供了坚实的基础，帮助他们在使用MySQL数据库的更复杂场景中充分利用Sequelize的功能。

---

# English version
The advanced use cases of Sequelize with MySQL.

### Section 1: Complex Queries with Sequelize

#### Example 1: Advanced Filtering with `where`
```javascript
const User = require('./models/user');

// Finding users older than 30 and have a specific job title
User.findAll({
  where: {
    age: {
      [Sequelize.Op.gt]: 30
    },
    jobTitle: 'Software Engineer'
  }
});
```

#### Example 2: Deep Loading Associations with `include`
```javascript
const Post = require('./models/post');
const Author = require('./models/author');

// Fetching posts along with the author details
Post.findAll({
  include: [{
    model: Author,
    required: true
  }]
});
```

#### Example 3: Sorting and Grouping Results with `order` and `group`
```javascript
const Order = require('./models/order');

// Getting orders grouped by status and sorted by creation date
Order.findAll({
  group: 'status',
  order: [
    ['createdAt', 'ASC']
  ]
});
```

### Section 2: Managing Transactions

#### Example 1: Basic Transaction Syntax
```javascript
const sequelize = require('./sequelize');

async function transactionExample() {
  const t = await sequelize.transaction();

  try {
    // your transactional operations here

    await t.commit();
  } catch (error) {
    await t.rollback();
  }
}
```

#### Example 2: Implementing Rollback on Error
```javascript
async function createUserWithTransaction() {
  const t = await sequelize.transaction();

  try {
    // Creating a user
    await User.create({ name: 'John Doe' }, { transaction: t });

    // If an error occurs, the transaction will be rolled back
    throw new Error('Oops! Something went wrong.');

    await t.commit();
  } catch (error) {
    await t.rollback();
  }
}
```

#### Example 3: Chaining Multiple Transactional Operations
```javascript
async function complexTransaction() {
  const t = await sequelize.transaction();

  try {
    await User.create({ name: 'Alice' }, { transaction: t });
    await Order.create({ productId: 1, userId: 2 }, { transaction: t });

    await t.commit();
  } catch (error) {
    await t.rollback();
  }
}
```

### Section 3: Dynamic Model Manipulation

#### Example 1: Adding Attributes to a Model
```javascript
// Assuming a User model is already defined
User.addAttribute('nickname', Sequelize.STRING);
```

#### Example 2: Changing Validation Rules Dynamically
```javascript
```javascript
// Updating validation for an email field in the User model
User.attributes.email.validate = {
  isEmail: true,
  notEmpty: true
};
```

#### Example 3: Implementing Model-Level Custom Functions
```javascript
User.prototype.getFullName = function() {
  return this.firstName + ' ' + this.lastName;
};

const user = await User.findOne({ where: { id: 1 } });
console.log(user.getFullName());
```

### Section 4: Advanced Association Techniques

#### Example 1: Setting Up Polymorphic Associations
```javascript
// Assuming a Comment and Post model
Comment.belongsTo(Post, { as: 'commentable', constraints: false, allowNull: true, foreignKey: 'commentableId' });

// Usage: Fetching comments for a post
Post.findOne({
  where: { id: 1 },
  include: [{
    model: Comment,
    as: 'commentable'
  }]
});
```

#### Example 2: Many-to-Many Relationships with Additional Attributes
```javascript
// User and Project are models; UserProjects is the join table
User.belongsToMany(Project, { through: UserProjects });
Project.belongsToMany(User, { through: UserProjects });

UserProjects.belongsTo(User);
UserProjects.belongsTo(Project);
UserProjects.hasOne(Role); // Role is another model
```

#### Example 3: Using Association Scopes for Filtering Related Data
```javascript
User.hasMany(Post, {
  scope: {
    published: true
  }
});

// Fetching user with only published posts
User.findOne({
  include: [{
    model: Post
  }]
});
```

### Section 5: Performance Optimization

#### Example 1: Eager Loading to Reduce Database Queries
```javascript
User.findAll({
  include: [Post]
});
```

#### Example 2: Using Raw Queries for Complex Operations
```javascript
sequelize.query("SELECT * FROM Users WHERE status = 'active'", {
  type: Sequelize.QueryTypes.SELECT
});
```

#### Example 3: Caching Frequent Queries with Redis
```javascript
const redis = require('redis');
const client = redis.createClient();

// Fetch and cache a user
const cachedUser = await client.get('user_1');
if (cachedUser) {
  return JSON.parse(cachedUser);
} else {
  const user = await User.findByPk(1);
  client.set('user_1', JSON.stringify(user));
  return user;
}
```

These examples demonstrate advanced use cases of Sequelize with MySQL, focusing on complex queries, transactions, model manipulation, associations, and performance optimization. The implementation details can vary based on the specific application context and Sequelize configuration. These examples provide a solid foundation for developers looking to leverage Sequelize's capabilities in more complex scenarios with MySQL databases.
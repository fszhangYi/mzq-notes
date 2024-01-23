## 挂载路径简介

在 Express.js 中，有效地组织路由和静态资源对于构建可扩展和可维护的网络应用至关重要。挂载路径在此组织中扮演了关键角色，为应用中间件和路由提供了特定的应用位置。本文深入探讨了挂载路径的理念、原则和使用方法，重点介绍了 `express.static` 和 `express.Router`。

## 挂载路径的理念

挂载路径源于对网络应用进行模块化和清晰结构化的需求。它们允许开发者：

1. **封装功能**：对相关路由或中间件进行分组，使应用更加模块化。

2. **避免命名空间冲突**：为应用的不同部分定义明确的命名空间。

3. **增强可扩展性**：通过逻辑地组织路由和静态资源，使大型应用更易于扩展和管理。

## 比较：使用挂载路径前后在 Express.js 中的路由匹配
### 未使用挂载路径的路由匹配

在**未使用挂载路径**的 Express.js 应用中，路由直接在应用的根路径上定义和匹配。以下是一个示例：

```javascript
const express = require('express');
const app = express();

// 直接在应用上定义路由
app.get('/products', (req, res) => { /* ... */ });
app.get('/users', (req, res) => { /* ... */ });

// 匹配: "/products", "/users"
```

在此设置下：

1. 定义为 `/products` 的路由可以在 `http://yourdomain.com/products` 访问。

2. 定义为 `/users` 的路由可以在 `http://yourdomain.com/users` 访问。

3. `/products` 或 `/users` 之前没有额外的路径段。

### 使用挂载路径的路由匹配

使用 **挂载路径** 如 `express.Router()` 时，路由相对于挂载路径进行匹配。以下是一个示例：

```javascript
const express = require('express');
const app = express();
const productRouter = express.Router();
const userRouter = express.Router();

// 在路由器上定义路由
productRouter.get('/', (req, res) => { /* ... */ }); // 相对路径
userRouter.get('/', (req, res) => { /* ... */ });    // 相对路径

// 挂载路由器
app.use('/products', productRouter);
app.use('/users', userRouter);

// 匹配: "/products/", "/users/"
```

在此设置下：

1. `productRouter` 被挂载在 `/products`。所以在 `productRouter` 中定义为 `/` 的路由实际上可以在 `http://yourdomain.com/products/` 访问。

2. 类似地，`userRouter` 被挂载在 `/users`。在 `userRouter` 中定义为 `/` 的路由可以在 `http://yourdomain.com/users/` 访问。

3. 路径段的前导部分（`/products` 或 `/users`）充当定义在相应路由器中所有路由的命名空间。

### 主要差异

1. **命名空间组织**：使用挂载路径后，路由被命名空间化，使应用在增长时更加有组织。

2. **路由定义**：在使用挂载路径的情况下，路由是相对于挂载路径定义的，这导致代码更加模块化。

3. **URL 结构**：挂载路径在 URL 中引入了额外的段，这对于逻辑地分组路
由很有用，但也改变了 URL 的形成和匹配方式。

通过理解这些差异，可以更好地构建您的 Express.js 应用，确保它们是可扩展的、可维护的，并且逻辑上组织良好。

## 理解 `express.static`

`express.static` 是 Express.js 中用于从指定目录提供静态文件的内置中间件函数。

它直接从给定目录提供文件，绕过额外的路由。这对于提供 CSS、JavaScript、图片和其他静态资源非常高效。

### 与挂载路径结合的使用
- **默认使用**：从根路径提供文件。
  ```javascript
  app.use(express.static('public'));
  ```
- **使用挂载路径**：在特定路径下提供文件。
  ```javascript
  app.use('/static', express.static('public'));
  ```

## 利用 `express.Router`

`express.Router` 是一个创建模块化路由处理程序的类。它类似于一个能够执行中间件和路由功能的迷你应用。

路由器允许在不同文件或模块中定义路由，每个模块可能有自己的中间件，使代码库更加干净、易于管理。

### 与挂载路径结合的使用
- 创建路由器实例：
  ```javascript
  const router = express.Router();
  ```
- 在路由器上定义路由：
  ```javascript
  router.get('/', (req, res) => res.send('Router Home'));
  ```
- 在路径上挂载路由器：
  ```javascript
  app.use('/myrouter', router);
  ```

## 将 `express.static` 与 `express.Router` 结合使用

结合 Express.js 这两个强大的特性，可以创建一个高度有组织和高效的结构。

- 通过 `express.Router` 为特定模块提供相关的静态文件。
- 在相同的挂载路径下定义 API 端点和相关静态资源。

```javascript
const express = require('express');
const app = express();
const router = express.Router();

// 路由器特定的中间件
router.use('/static', express.static('path_to_static_assets'));

// 路由器中的 API 端点
router.get('/api', (req, res) => {
  res.send('API 响应');
});

// 挂载路由器
app.use('/mymodule', router);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`服务器运行在端口 ${PORT}`));
```

在这种配置中，'path_to_static_assets' 中的静态资产可在 '/mymodule/static' 下访问，而 API 端点可在 '/mymodule/api' 访问。

## 结论

理解并有效地使用 Express.js 中的挂载路径，特别是结合 `express.static` 和 `express.Router`，对于开发结构良好、可扩展的网络应用至关重要。通过对应用的不同部分进行分隔，管理复杂项目变得更加可行，最终导致代码库更加干净、高效。

---

# English version: Mastering Mount Paths in Express.js: Harnessing `express.static` and `express.Router`

## Introduction to Mount Paths

In the world of Express.js, organizing routes and static assets effectively is crucial for building scalable and maintainable web applications. Mount paths play a pivotal role in this organization, providing a way to specify where middleware and routes should be applied. This article delves into the philosophies, principles, and usage of mount paths, spotlighting `express.static` and `express.Router`.

## Philosophy of Mount Paths

Mount paths are rooted in the need for modular and clear structuring of web applications. They allow developers to:

1. **Encapsulate Functionality**: Group related routes or middleware, making the application more modular.

2. **Avoid Namespace Collisions**: Define distinct namespaces for different parts of an application.

3. **Enhance Scalability**: Make it easier to scale and manage large applications by organizing routes and static assets logically.

## Comparison: Before and After Using Mount Paths in Express.js
Let's dive into the comparison of how route matching behaves in Express.js before and after the use of mount paths

### Route Matching Without Mount Paths

In an Express.js application **without using mount paths**, routes are defined and matched directly off the root of the application. Here's an example to illustrate:

```javascript
const express = require('express');
const app = express();

// Define routes directly on the app
app.get('/products', (req, res) => { /* ... */ });
app.get('/users', (req, res) => { /* ... */ });

// Matches: "/products", "/users"
```
In this setup:
1. A route defined as `/products` is accessible at `http://yourdomain.com/products`.
2. A route defined as `/users` is accessible at `http://yourdomain.com/users`.
3. There's no additional path segment before `/products` or `/users`.

### Route Matching With Mount Paths

When using **mount paths** with something like `express.Router()`, the routes are matched relative to the mount path. Here's an example:

```javascript
const express = require('express');
const app = express();
const productRouter = express.Router();
const userRouter = express.Router();

// Define routes on the routers
productRouter.get('/', (req, res) => { /* ... */ }); // Relative path
userRouter.get('/', (req, res) => { /* ... */ });    // Relative path

// Mount the routers
app.use('/products', productRouter);
app.use('/users', userRouter);

// Matches: "/products/", "/users/"
```

In this setup:

1. The `productRouter` is mounted at `/products`. So a route defined as `/` in `productRouter` is actually accessible at `http://yourdomain.com/products/`.


2. Similarly, the `userRouter` is mounted at `/users`. A route defined as `/` in `userRouter` is accessible at `http://yourdomain.com/users/`.


3. The leading path segment (`/products` or `/users`) acts as a namespace for all routes defined in the respective router.

### Key Differences

1. **Namespace Organization**: With mount paths, routes are namespaced, making the application more organized, especially as it grows.

2. **Route Definitions**: In the case of mount paths, routes are defined relative to the mount path, leading to more modular code.

3. **URL Structure**: Mount paths introduce an additional segment in the URL, which is great for logically grouping routes but changes how URLs are formed and matched.

By understanding these differences, you can better structure your Express.js applications, ensuring that they are scalable, maintainable, and logically organized.

## Understanding `express.static`

`express.static` is a built-in middleware function in Express.js used to serve static files from a specified directory.

### Principle
It serves files directly from a given directory, bypassing additional routing. This is efficient for serving CSS, JavaScript, images, and other static assets.

### Usage with Mount Paths
- **Default Usage**: Serve files from the root path.
  ```javascript
  app.use(express.static('public'));
  ```
- **With Mount Path**: Serve files under a specific path.
  ```javascript
  app.use('/static', express.static('public'));
  ```

## Leveraging `express.Router`

`express.Router` is a class that creates modular route handlers. It's akin to a mini-app capable of performing middleware and routing functions.

### Principle
Routers allow defining routes in separate files or modules, each potentially having its middleware, making the codebase cleaner and more manageable.

### Usage with Mount Paths
- Create a router instance:
  ```javascript
  const router = express.Router();
  ```
- Define routes on the router:
  ```javascript
  router.get('/', (req, res) => res.send('Router Home'));
  ```
- Mount the router on a path:
  ```javascript
  app.use('/myrouter', router);
  ```

## Collaborating `express.static` with `express.Router`

Combining these two powerful features of Express.js allows for a highly organized and efficient structure.

- Serve static files related to a specific module via `express.Router`.
- Define API endpoints and related static assets under the same mount path.

```javascript
const express = require('express');
const app = express();
const router = express.Router();

// Router-specific middleware
router.use('/static', express.static('path_to_static_assets'));

// API endpoint within the router
router.get('/api', (req, res) => {
  res.send('API response');
});

// Mount the router
app.use('/mymodule', router);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

In this configuration, static assets in 'path_to_static_assets' are accessible under '/mymodule/static', while the API endpoint is accessible at '/mymodule/api'.

## Conclusion

Understanding and effectively using mount paths in Express.js, particularly with `express.static` and `express.Router`, is essential for developing well-structured, scalable web applications. By compartmentalizing different parts of the application, managing complex projects becomes more feasible, ultimately leading to cleaner, more efficient codebases.

This article aims to provide a comprehensive understanding of mount paths in Express.js, emphasizing their strategic use to enhance application structure and maintainability.
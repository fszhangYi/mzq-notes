## CORS（跨源资源共享）简介

CORS 是在 web 浏览器中实现的一种安全功能，允许服务器指定哪些来源可以访问服务器上的资源。默认情况下，web 浏览器执行同源策略，这意味着网页只能从提供它们的同一来源（域、协议和端口）请求资源。CORS 提供了一种方式，允许从不同的来源请求资源，同时保持安全性。

### Node.js 中的 CORS 包

在 Node.js 和 Express.js 的上下文中，`cors` 包是一种中间件，简化了在应用程序中启用 CORS 的过程。它允许定义规则，指明哪些来源可以访问服务器的资源。

#### 基本用法

首先，使用 npm 安装 `cors` 包：

```bash
npm install cors
```

然后，可以这样将其包含在 Express 应用程序中：

```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// 使用默认设置使用 CORS（允许来自任何来源的请求）
app.use(cors());

app.get('/', (req, res) => {
    res.send('为所有来源启用了 CORS！');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`服务器运行在端口 ${PORT}`));
```

#### 高级用法

为了更多的控制，可以使用特定选项配置 CORS：

```javascript
app.use(cors({
    origin: 'http://example.com', // 仅允许此来源访问资源
    methods: 'GET,POST', // 仅允许这些 HTTP 方法
    allowedHeaders: 'Content-Type,Authorization', // 仅允许这些头
    credentials: true, // 允许请求携带凭证
}));
```

### 原理理解

CORS 通过向服务器的响应中添加特定的 HTTP 头部来工作。最关键的头部是 `Access-Control-Allow-Origin`，它告诉浏览器哪些域被允许访问该资源。其他头部控制方法、头部、凭证等。当浏览器发出 CORS 请求时，它首先发送一个预检请求（使用 OPTIONS 方法），以检查实际请求是否安全。

### 简单模拟 CORS 中间件的演示

为了模仿 CORS 的基本功能，可以创建一个简单的中间件：

```javascript
function simpleCors(req, res, next) {
    res.setHeader('Access-Control-Allow-Origin', '*'); // 允许所有来源
    next();
}

app.use(simpleCors);
```

这个函数将 `Access-Control-Allow-Origin` 头部设置为 `*`，意味着允许来自任何来源的请求。这是 `cors` 包所做工作的简化版本。

### 结论

了解和实施 CORS 对于 web 开发人员至关重要，特别是在构建需要从不同来源访问资源的应用程序时。Node.js 中的 `cors` 包简化了 CORS 的配置和实施，确保 web 应用程序可以在不同域之间安全、高效地通信。

---

# English version

### Introduction to CORS

CORS, or Cross-Origin Resource Sharing, is a security feature implemented in web browsers. It allows servers to specify who (i.e., which origins) can access the resources on the server. By default, web browsers enforce the same-origin policy, meaning that web pages can only request resources from the same origin (domain, protocol, and port) they were served from. CORS provides a way to allow resources to be requested from different origins, enhancing the possibilities for web applications while maintaining security.

### The CORS Package in Node.js

In the context of Node.js and Express.js, the `cors` package is a middleware that simplifies the process of enabling CORS in your application. It allows you to define rules for which origins can access your server's resources.

#### Basic Usage

First, install the `cors` package using npm:

```bash
npm install cors
```

Then, you can include it in your Express application like so:

```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// Use CORS with default settings (allows requests from any origin)
app.use(cors());

app.get('/', (req, res) => {
    res.send('CORS-enabled for all origins!');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

#### Advanced Usage

For more control, you can configure CORS with specific options:

```javascript
app.use(cors({
    origin: 'http://example.com', // Allow only this origin to access resources
    methods: 'GET,POST', // Allow only these HTTP methods
    allowedHeaders: 'Content-Type,Authorization', // Allow only these headers
    credentials: true, // Allow cookies to be sent with requests
}));
```

### Understanding the Principle

CORS works by adding specific HTTP headers to responses from the server. The most critical header is `Access-Control-Allow-Origin`, which tells the browser which domains are permitted to access the resource. Other headers control methods, headers, credentials, and more. When a browser makes a CORS request, it first sends a preflight request (using the OPTIONS method) to check if the actual request is safe to send.

### A Simple CORS-like Middleware Demo

To mimic the basic functionality of CORS, you can create a simple middleware:

```javascript
function simpleCors(req, res, next) {
    res.setHeader('Access-Control-Allow-Origin', '*'); // Allow all origins
    next();
}

app.use(simpleCors);
```

This function sets the `Access-Control-Allow-Origin` header to `*`, which means it allows requests from any origin. This is a simplified version of what the `cors` package does.

### Conclusion

Understanding and implementing CORS is essential for web developers, especially when building applications that access resources from different origins. The `cors` package in Node.js simplifies the configuration and implementation of CORS, ensuring that your web applications can securely and efficiently communicate across different domains.
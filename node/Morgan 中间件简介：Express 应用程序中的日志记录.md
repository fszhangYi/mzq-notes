## 概述
Morgan 是用于 Express 框架的 Node.js 应用程序的流行中间件包。它主要用于记录 HTTP 请求，这对于监控和调试非常关键。本文将探讨 Morgan 的基本和高级用法，深入了解其原理，最后简单模拟其功能的实现。

## 基本用法
要开始使用 Morgan，首先需要使用 npm 进行安装：

```bash
npm install morgan
```

安装后，可以将其纳入 Express 应用程序。以下是一个基本示例：

```javascript
const express = require('express');
const morgan = require('morgan');

const app = express();

// 使用 'dev' 格式的 Morgan
app.use(morgan('dev'));

app.get('/', (req, res) => {
    res.send('Hello World!');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`服务器运行在端口 ${PORT}`));
```

在此示例中，Morgan 设置为 'dev' 格式，该格式提供了适合开发环境的简洁彩色输出。

## 高级用法
Morgan 高度可定制。可以定义自定义令牌格式，甚至创建自己的日志格式。以下是使用自定义格式的示例：

```javascript
app.use(morgan(':method :url :status :res[content-length] - :response-time ms'));
```

此格式记录了 HTTP 方法、URL、状态码、响应内容长度和响应时间。

## 原理
Morgan 通过拦截 Express 应用程序中的 HTTP 请求和响应来工作。然后根据配置的格式格式化并记录必要的信息。在内部，它使用 Node.js 的事件驱动架构来监听请求和响应事件，使其能够异步捕获和记录数据，而不会显著影响性能。

## 模拟 Morgan 功能的简单演示
让我们创建一个基础版本的 Morgan，以理解其核心功能。此演示中间件将记录每个请求的 HTTP 方法和 URL：

```javascript
const express = require('express');

const app = express();

// 自定义日志中间件
function simpleLogger(req, res, next) {
    console.log(`${req.method} ${req.url}`);
    next();
}

app.use(simpleLogger);

app.get('/', (req, res) => {
    res.send('Hello World!');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`服务器运行在端口 ${PORT}`));
```

在此演示中，`simpleLogger` 是一个中间件函数，记录了请求方法和 URL。这是对 Morgan 内部操作方式的基本说明。

## 结论
Morgan 是 Express 应用程序中记录 HTTP 请求的强大工具。其易用性与灵活性的结合，使其成为开发和生产环境中不可或缺的包。理解其原理和用法可以显著帮助监控和调试 Node.js 应用程序。

--- 

# English version
# Introduction to Morgan: A Middleware for Logging in Express Applications

## Overview
Morgan is a popular middleware package for Node.js applications using the Express framework. Its primary function is to log HTTP requests, which is crucial for monitoring and debugging purposes. This article will explore Morgan's basic and advanced usage, delve into its underlying principles, and conclude with a simple demo that mimics its functionality.

## Basic Usage
To get started with Morgan, you first need to install it using npm:

```bash
npm install morgan
```

Once installed, you can incorporate it into your Express application. Here's a basic example:

```javascript
const express = require('express');
const morgan = require('morgan');

const app = express();

// Use Morgan with the 'dev' format
app.use(morgan('dev'));

app.get('/', (req, res) => {
    res.send('Hello World!');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

In this example, Morgan is set up with the 'dev' format, which provides a concise colored output suitable for development environments.

## Advanced Usage
Morgan is highly customizable. You can define custom token formats or even create your own logging format. Here's an example of using a custom format:

```javascript
app.use(morgan(':method :url :status :res[content-length] - :response-time ms'));
```

This format logs the HTTP method, URL, status code, response content length, and response time.

## Underlying Principle
Morgan works by intercepting HTTP requests and responses in the Express application. It then formats and logs the necessary information based on the configured format. Internally, it uses Node.js' event-driven architecture to listen for request and response events, enabling it to capture and log data asynchronously without significantly impacting performance.

## Mimicking Morgan's Functionality: A Simple Demo
Let's create a basic version of Morgan to understand its core functionality. This demo middleware will log the HTTP method and URL of each request:

```javascript
const express = require('express');

const app = express();

// Custom logging middleware
function simpleLogger(req, res, next) {
    console.log(`${req.method} ${req.url}`);
    next();
}

app.use(simpleLogger);

app.get('/', (req, res) => {
    res.send('Hello World!');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

In this demo, `simpleLogger` is a middleware function that logs the request method and URL. It's a basic illustration of how Morgan operates under the hood.

## Conclusion
Morgan is a powerful tool for logging HTTP requests in Express applications. Its ease of use, combined with its flexibility, makes it an essential package for both development and production environments. Understanding its principles and usage can significantly aid in monitoring and debugging Node.js applications.
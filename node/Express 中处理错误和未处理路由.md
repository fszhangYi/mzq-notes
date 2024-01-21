## 概览

在使用 Express 开发 Web 应用程序时，有效地处理错误和管理未匹配任何定义处理程序的路由至关重要。这确保了应用程序的健壮性和更好的用户体验。

## Express 中的错误处理

Express 通过中间件提供了内置的错误处理机制。在 Express 应用程序中进行适当的错误处理包括定义错误处理中间件函数，以捕获错误并作出相应响应。

### 分步指南

#### 1. 创建错误处理中间件

错误处理中间件定义了四个参数：`err`、`req`、`res` 和 `next`。

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('出错了！');
});
```

#### 2. 使用 `next()`

`next()` 函数至关重要。它将错误传递给链中的下一个错误处理中间件。在路由处理程序或其他中间件中，调用 `next(err)` 将错误传递给错误处理程序。

```javascript
app.get('/route', (req, res, next) => {
  try {
    // ... 您的逻辑
  } catch (err) {
    next(err);
  }
});
```

#### 3. 处理异步错误

对于异步代码，使用 `catch` 或与 promises 和 async/await 一起使用 `next` 来处理错误。

```javascript
app.get('/async-route', async (req, res, next) => {
  try {
    // 尝试运行异步操作
    const data = await someAsyncOperation();
    // 如果成功，将数据发送回客户端
    res.send(data);
  } catch (err) {
    // 如果 someAsyncOperation() 中发生错误，捕获它
    // 然后将错误传递给下一个错误处理中间件
    next(err);
  }
});
```

## 处理未匹配的路由

未处理的路由是指不匹配任何定义路由的请求。优雅地处理这些路由是一个良好的实践。

### 处理未处理路由的步骤

#### 1. 定义全捕获路由

在所有路由之后，添加一个全捕获路由处理程序。

```javascript
app.use('*', (req, res) => {
  res.status(404).send('404 - 未找到');
});
```

这将捕获对未定义路由的任何请求，并返回 404 状态码。

#### 2. 自定义响应

您可以根据需要自定义 404 响应，比如渲染一个 404 页面或返回一个 JSON 响应。

```javascript
app.use('*', (req, res) => {
  res.status(404).render('NotFound');
});
```

## 结论

在 Express 中适当地处理错误和管理未匹配的路由是稳定和用户友好 Web 应用程序的关键组成部分。通过实施这些策略，您可以确保您的应用程序行为可预测，并在发生错误或访问错误路由时为用户提供信息性反馈。

---
# English version: Handling Errors and Unhandled Routes in Express

## Overview

When developing web applications with Express, it's crucial to handle errors effectively and manage routes that do not match any defined handlers. This ensures a robust application and a better user experience.

## Error Handling in Express

Express provides a built-in mechanism for error handling through middleware. Proper error handling in an Express application involves defining error-handling middleware functions that catch errors and respond accordingly.

### Step-by-Step Guide

#### 1. Creating an Error Handling Middleware

An error handling middleware is defined with four arguments: `err`, `req`, `res`, and `next`.

```javascript
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

#### 2. Using `next()`

The `next()` function is crucial. It passes the error to the next error handling middleware in line. In your route handlers or other middleware, call `next(err)` to pass errors to your error handler.

```javascript
app.get('/route', (req, res, next) => {
  try {
    // ... your logic
  } catch (err) {
    next(err);
  }
});
```

#### 3. Handling Asynchronous Errors

For asynchronous code, use `catch` or `next` with promises and async/await to handle errors.

```javascript
app.get('/async-route', async (req, res, next) => {
  try {
    // Attempt to run the asynchronous operation
    const data = await someAsyncOperation();
    // If successful, send the data back to the client
    res.send(data);
  } catch (err) {
    // If an error occurs in someAsyncOperation(), catch it
    // Then pass the error to the next error handling middleware
    next(err);
  }
});

```

## Handling Unmatched Routes

Unhandled routes are requests that do not match any of the defined routes. It's good practice to handle these gracefully.

### Steps for Handling Unhandled Routes

#### 1. Define a Catch-All Route

After all your routes, add a catch-all route handler.

```javascript
app.use('*', (req, res) => {
  res.status(404).send('404 - Not Found');
});
```

This will catch any requests to undefined routes and return a 404 status code.

#### 2. Customize the Response

You can customize the 404 response as needed, maybe rendering a 404 page or returning a JSON response.

```javascript
app.use('*', (req, res) => {
  res.status(404).render('NotFound');
});
```

## Conclusion

Proper error handling and managing unmatched routes in Express are key components of a stable and user-friendly web application. By implementing these strategies, you can ensure that your application behaves predictably and provides informative feedback to the user in case of errors or incorrect route access.

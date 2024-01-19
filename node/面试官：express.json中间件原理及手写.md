Express.js 中的 `express.json()` 中间件是一个内置的中间件函数，用于解析传入的请求体作为 JSON。这个中间件对于需要处理 JSON 数据的服务器尤其重要，因为它简化了在请求中解析和处理 JSON 负载的过程。本文对其原理和功能进行解释，并基于此手写一个简单的express.json中间件。

### 1. 解析 JSON 请求体
- **功能**：当客户端向 Express.js 服务器（如 API 调用）发送请求时，请求可以包含数据体。如果这些数据是 JSON 格式的，`express.json()` 中间件会自动将这个 JSON 字符串解析成一个 JavaScript 对象。
- **用途**：它通常用于 POST 或 PUT HTTP 方法，其中客户端需要向服务器发送数据。

### 2. 中间件的性质
- **工作方式**：作为 Express.js 中的一个中间件，`express.json()` 通过拦截请求在到达路由处理程序之前来处理请求。如果请求的 Content-Type 头部设置为 `application/json`，它会将请求体从 JSON 字符串解析为 JavaScript 对象。
- **访问数据**：解析后，生成的 JavaScript 对象会附加到 `req.body` 属性上，使其在后续的路由处理程序或中间件中容易访问。

### 3. 配置选项
- **限制**：可以为传入的 JSON 负载设置大小限制。如果负载超过此限制，中间件将不会解析它，并且会抛出一个错误。
- **扩展语法**：`express.json()` 中间件在底层使用 `body-parser` 包。尽管默认设置对大多数情况已经足够，但可以根据具体需求进行进一步配置，如处理自定义类型或调整解析选项。

### 4. 安全性和效率
- **防止大负载**：通过设置合理的大小限制，可以防止服务器处理过大的负载，这可能是潜在的攻击向量或导致性能问题。
- **效率**：仅在需要时（基于 Content-Type 头部）解析 JSON，有助于维护服务器效率，因为并非所有请求都需要 JSON 解析。

### 5. 在 Express.js 中的示例用法

```javascript
const express = require('express');
const app = express();

app.use(express.json()); // 激活 express.json() 中间件

app.post('/api/data', (req, res) => {
    console.log(req.body); // 客户端解析后的 JSON 数据
    // ...其余路由逻辑
});

app.listen(3000, () => console.log('服务器运行在 3000 端口'));
```

在此示例中，任何对 `/api/data` 的 POST 请求带有 JSON 负载，其体将自动被解析并在 `req.body` 中作为一个 JavaScript 对象可用。

### 6. 手动实现一个简单的 express.json
创建一个类似于 `express.json()` 的中间件涉及解析传入的 JSON 格式的请求体。为此需要：

1. 检查传入请求的 `Content-Type` 头部是否为 `application/json`。
2. 读取请求体（以流的形式传入）。
3. 将请求体从 JSON 字符串解析为 JavaScript 对象。
4. 将解析后的对象附加到 `req.body` 上。

以下是在 Express.js 中创建此类中间件的分步指南：

#### 步骤 1：设置基本中间件结构

首先，定义中间件函数的结构。它应该有三个参数：`req`（请求对象）、`res`（响应对象）和 `next`（调用堆栈中下一个中间件的函数）。

```javascript
function customJsonParser(req, res, next) {
    // 中间件逻辑将在这里编写
}
```



#### 步骤 2：检查 Content-Type

仅在 `Content-Type` 头部设置为 `application/json` 时继续解析。

```javascript
function customJsonParser(req, res, next) {
    if (req.headers['content-type'] === 'application/json') {
        // 继续解析
    } else {
        next(); // 如果不是 JSON，则跳过此中间件
    }
}
```

#### 步骤 3：读取并解析请求体

使用流读取请求体，然后将其解析为 JSON。由于请求体以流的形式传入，需要收集数据块，直到流结束。

```javascript
function customJsonParser(req, res, next) {
    if (req.headers['content-type'] === 'application/json') {
        let data = '';

        req.on('data', chunk => {
            data += chunk;
        });

        req.on('end', () => {
            try {
                req.body = JSON.parse(data);
                next();
            } catch (error) {
                res.status(400).send('无效的 JSON');
            }
        });
    } else {
        next();
    }
}
```

#### 步骤 4：在 Express 应用中使用中间件

在 Express 应用中使用这个中间件，就像使用 `express.json()` 一样。

```javascript
const express = require('express');
const app = express();

app.use(customJsonParser);

app.post('/api/data', (req, res) => {
    console.log(req.body); // 解析后的 JSON 数据
    res.send('数据已接收');
});

app.listen(3000, () => console.log('服务器运行在 3000 端口'));
```

这个自定义的 `customJsonParser` 中间件现在将为需要它的路由解析传入的 JSON 负载，并将解析后的数据附加到 `req.body` 上。

#### 注意

这个实现是基础的，仅用于演示目的。在生产环境中，如果想要添加更多功能，如错误处理、负载大小限制和对不同字符编码的支持。实际的 `express.json()` 中间件更加健壮，能够处理各种边缘情况和配置，因此更适合大多数实际应用。

## 总结
总之，`express.json()` 是 Express.js 生态系统中 API 的重要组成部分，使得在请求中轻松有效地处理 JSON 数据成为可能。

---
# English version
The middleware `express.json()` in Express.js is a built-in middleware function that parses incoming request bodies as JSON. This middleware is particularly important for servers that need to handle JSON data, as it simplifies the process of parsing and working with JSON payloads in the requests. Here's an explanation of its principles and functionality:

### 1. Parsing JSON Request Bodies
- **Functionality**: When a client sends a request to an Express.js server (like an API call), the request can include a body of data. If this data is in JSON format, `express.json()` middleware automatically parses this JSON string into a JavaScript object.
- **Usage**: It's typically used in POST or PUT HTTP methods where the client needs to send data to the server.

### 2. Middleware Nature
- **How It Works**: As a middleware in Express.js, `express.json()` works by intercepting the request before it reaches the route handler. It processes the request, and if the Content-Type header of the request is set to `application/json`, it parses the body of the request from JSON string to a JavaScript object.
- **Accessing Data**: After parsing, the resulting JavaScript object is attached to the `req.body` property, making it easily accessible in subsequent route handlers or middleware.

### 3. Configuration Options
- **Limit**: You can set a size limit for incoming JSON payloads. If a payload exceeds this limit, the middleware will not parse it, and an error will be thrown.
- **Extended Syntax**: The `express.json()` middleware uses the `body-parser` package under the hood. Although the default settings are sufficient for most cases, you can configure it further for specific needs, like handling custom types or adjusting parsing options.

### 4. Security and Efficiency
- **Preventing Large Payloads**: By setting a reasonable size limit, you can prevent your server from processing overly large payloads, which could be a potential attack vector or cause performance issues.
- **Efficiency**: Parsing JSON only when needed (based on the Content-Type header) helps in maintaining server efficiency, as not all requests require JSON parsing.

### 5. Example Usage in Express.js

```javascript
const express = require('express');
const app = express();

app.use(express.json()); // Activates the express.json() middleware

app.post('/api/data', (req, res) => {
    console.log(req.body); // The parsed JSON data from the client
    // ...rest of the route logic
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

In this example, any POST request to `/api/data` with a JSON payload will have its body automatically parsed and available as a JavaScript object in `req.body`.

### 6. DIY an simple express.json
Creating a middleware similar to `express.json()` involves parsing incoming request bodies that are in JSON format. To implement this, you need to:

1. Check the `Content-Type` header of incoming requests to see if it's `application/json`.
2. Read the request body (which comes as a stream).
3. Parse the body from a JSON string to a JavaScript object.
4. Attach the parsed object to `req.body`.

Here's a step-by-step guide to creating such a middleware in Express.js:

#### Step 1: Set Up Basic Middleware Structure

First, define the structure of your middleware function. It should take three parameters: `req` (the request object), `res` (the response object), and `next` (a function to call the next middleware in the stack).

```javascript
function customJsonParser(req, res, next) {
    // Middleware logic will go here
}
```

#### Step 2: Check Content-Type

Only proceed with parsing if the `Content-Type` header is set to `application/json`. 

```javascript
function customJsonParser(req, res, next) {
    if (req.headers['content-type'] === 'application/json') {
        // Proceed with parsing
    } else {
        next(); // Skip this middleware if not JSON
    }
}
```

#### Step 3: Read and Parse the Request Body

Read the request body using streams, and then parse it as JSON. Since the request body comes as a stream, you need to collect the chunks of data until the stream is finished.

```javascript
function customJsonParser(req, res, next) {
    if (req.headers['content-type'] === 'application/json') {
        let data = '';

        req.on('data', chunk => {
            data += chunk;
        });

        req.on('end', () => {
            try {
                req.body = JSON.parse(data);
                next();
            } catch (error) {
                res.status(400).send('Invalid JSON');
            }
        });
    } else {
        next();
    }
}
```

#### Step 4: Using the Middleware in Your Express App

Now, you can use this middleware in your Express application just like you would use `express.json()`.

```javascript
const express = require('express');
const app = express();

app.use(customJsonParser);

app.post('/api/data', (req, res) => {
    console.log(req.body); // Parsed JSON data
    res.send('Data received');
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

This custom middleware `customJsonParser` will now parse incoming JSON payloads for routes that need it, attaching the parsed data to `req.body`.

#### Note

This implementation is basic and for demonstration purposes. In a production environment, you might want to add more features like error handling, size limits for payloads, and support for different character encodings. The actual `express.json()` middleware is more robust and handles various edge cases and configurations, making it preferable for most real-world applications.

## Summary
In summary, `express.json()` is a crucial part of the Express.js ecosystem for APIs, enabling easy and efficient handling of JSON data in requests.


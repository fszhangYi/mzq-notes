## 1. 什么是 express.urlencoded
Express.js 中的 `express.urlencoded()` 中间件是另一个内置函数，它在处理传入请求中起着至关重要的作用。这个中间件专门设计用来解析内容类型为 `application/x-www-form-urlencoded` 的传入请求体。这种内容类型通常用于 HTML 表单。本文对其原理和功能进行解释，并基于此手写一个简单的express.urlencoded中间件。

### `express.urlencoded()` 的原理

1. **目的**：
   - `express.urlencoded()` 的主要目的是解析传入请求体的数据，这种数据格式是网页浏览器在使用 POST 方法从表单发送数据时通常使用的格式。

2. **内容类型**：
   - 它专门处理 `application/x-www-form-urlencoded` 内容类型。这种格式是 HTML 表单提交中的标准，表单数据被编码为键值对。

### 功能

1. **解析表单数据**：
   - 当客户端从表单提交数据时，数据被发送到服务器作为 URL 编码的字符串（字符以 URL 转义代码编码）。
   - `express.urlencoded()` 中间件将这些 URL 编码的数据解析成 JavaScript 对象。

2. **附加到请求对象**：
   - 解析后，中间件将结果对象附加到 `req.body` 属性上，类似于 `express.json()`。这使得表单数据在路由处理程序或后续中间件中易于访问。

3. **配置选项**：
   - `extended`：这个选项配置中间件使用 `querystring` 库（当为 false 时）或 `qs` 库（当为 true 时）进行解析。`qs` 库允许创建更丰富的对象，例如数组和嵌套对象。
   - `limit`：设置请求体的最大大小，提供对允许的数据量的控制。

4. **常见用途**：
   - 常用于服务器需要接受来自 HTML 表单的提交数据的场景。
   - 在处理提交表单数据的 POST 请求时尤为重要。

### 示例用法

```javascript
const express = require('express');
const app = express();

// 激活 express.urlencoded 中间件
app.use(express.urlencoded({ extended: true }));

app.post('/form-submit', (req, res) => {
    console.log(req.body); // 客户端的表单数据
    // ...其余路由逻辑
});

app.listen(3000, () => console.log('服务器运行在 3000 端口'));
```

在这个示例中，任何对 `/form-submit` 的 POST 请求，如果发送来自 HTML 表单（内容类型为 `application/x-www-form-urlencoded`）的数据，其体将被解析为通过 `req.body` 访问的 JavaScript 对象。

### 结论

`express.urlencoded()` 是处理 Express.js 应用程序中表单提交的必需中间件。它解析 URL 编码的表单数据的能力使其成为网页应用程序中常见的表单处理需求不可或缺的部分。就像 `express.json()` 一样，它是 Express.js 中间件生态系统的关键组成部分，促进了客户端提交数据的简单高效处理。

## 2. DIY 实现 express.urlencoded
实现 `express.urlencoded()` 中间件的基本版本涉及解析请求体中的 URL 编码数据。这种类型的数据通常由 HTML 表单发送。创建这样的中间件的基本步骤包括：

1. **检查内容类型**：确保请求的 Content-Type 是 `application/x-www-form-urlencoded`。
2. **读取和解析请求体**：URL 编码数据作为请求体中的字符串发送。这个字符串需要被解析成一个 JavaScript 对象。
3. **将解析后的数据附加到 `req.body`**：解析后，将结果对象附加到 `req.body` 属性上，以便在后续的中间件或路由处理程序中轻松访问。

以下是带有相应代码的分步指南：

### 步骤 1：设置基本中间件结构

定义中间件函数的结构。它应该具有三个参数：`req`、`res` 和 `next`。

```javascript
function customUrlEncodedParser(req, res, next) {
    // 中间件逻辑将在此处编写
}
```

### 步骤 2：检查内容类型

仅在内容类型为 `application/x-www-form-urlencoded` 时继续解析。

```javascript
function customUrlEncodedParser(req, res, next) {
    if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
        // 继续解析
    } else {
        next(); // 如果不是 URL 编码，则跳过此中间件
    }
}
```

### 步骤 3：读取和解析请求体

使用流读取请求体。URL 编码的数据以字符串形式出现，我们需要将其解析成键值对。

```javascript
function customUrlEncodedParser(req, res, next) {
    if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
        let body = '';

        req.on('data', chunk => {
            body += chunk.toString(); // 将 Buffer 转换为字符串
        });

        req.on('end', () => {
            req.body = parseUrlEncoded(body);
            next();
        });
    } else {
        next();
    }
}

function parseUrlEncoded(str) {
    const pairs = str.split('&');
    return pairs.reduce((acc, pair) => {
        const [key, value] = pair.split('=');
        acc[decodeURIComponent(key)] = decodeURIComponent(value);
        return acc;
    }, {});
}
```

### 步骤 4：在 Express 应用中使用中间件

就像使用 `express.urlencoded()` 一样，在 Express 应用中使用这个中间件。

```javascript
const express = require('express');
const app = express();

app.use(customUrlEncodedParser);

app.post('/form-submit', (req, res) => {
    console.log(req.body); // 解析后的表单数据
    res.send('已收到表单数据');
});

app.listen(3000, () => console.log('服务器运行在 3000 端口'));
```

在这个设置中，任何对 `/form-submit` 的 POST 请求，如果带有 URL 编码的数据（来自 HTML 表单），将被解析为通过 `req.body` 访问的 JavaScript 对象。

### 注意

这个实现是一个基本版本，用于演示。实际应用场景可能需要处理更复杂的数据结构，解码嵌套对象或处理数组。实际的 `express.urlencoded()` 中间件更加复杂，能够处理各种边缘情况和配置，使其成为生产环境的更佳选择。


## 3. 扩展以实现选项配置
`express.urlencoded({ extended: true })` 中的 `{ extended: true }` 选项是一个重要的配置设置，它决定了如何解析 URL 编码的数据。具体来说，它告诉中间件使用哪个解析库：

- 当 `extended` 设置为 `true` 时，中间件使用 `qs` 库进行解析。这个库允许将丰富的对象和数组编码成 URL 编码格式，意味着它可以处理嵌套对象。
- 当 `extended` 设置为 `false` 时，中间件使用 `querystring` 库，它不支持嵌套对象。

要实现这个功能的基本版本，您可以编写一个模仿这种行为的自定义解析函数。以下是如何做到这一点：

### 步骤 1：扩展解析函数

首先，定义一个基于 `extended` 选项的解析函数。为了简单起见，我们将实现一个基本的方法来解析 `extended` 为 true 时的嵌套对象。完整的实现将需要一个更强大的解析算法，或直接使用 `qs` 库。

```javascript
function parseUrlEncoded(str, extended) {
    if (!extended) {
        // 简单解析（不处理嵌套对象）
        return str.split('&').reduce((acc, pair) => {
            const [key, value] = pair.split('=');
            acc[decodeURIComponent(key)] = decodeURIComponent(value);
            return acc;
        }, {});
    } else {
        // 扩展解析的基本实现（处理嵌套对象）
        // 注意：这仅用于演示。完整的实现将更复杂。
        return str.split('&').reduce((acc, pair) => {
            const [key, value] = pair.split('=');
            const path = key.split('[').map(k => k.replace(/]$/, ''));
            let current = acc;
            for (let i = 0; i < path.length - 1; i++) {
                if (!current[path[i]]) current[path[i]] = {};
                current = current[path[i]];
            }
            current[path[path.length - 1]] = decodeURIComponent(value);
            return acc;
        }, {});
    }
}
```

### 步骤 2：更新中间件

修改自定义中间件以接受一个选项对象，并根据选项使用解析函数。

```javascript
function customUrlEncodedParser(options = {}) {
    const extended = options.extended || false;

    return function (req, res, next) {
        if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
            let body = '';

            req.on('data', chunk => {
                body += chunk.toString(); // 将 Buffer 转换为字符串
            });

            req.on('end', () => {
                req.body = parseUrlEncoded(body, extended);
                next();
            });
        } else {
            next();
        }
    };
}
```

### 步骤 3：使用带有选项的中间件

现在，您可以使用带有 `extended` 选项的这个中间件。

```javascript
const express = require('express');
const app = express();

// 使用带有 extended 选项的自定义中间件
app.use(customUrlEncodedParser({ extended: true }));

app.post('/form-submit', (req, res) => {
    console.log(req.body); // 解析后的表单数据
    res.send('已收到表单数据');
});

app.listen(3000, () => console.log('服务器运行在 3000 端口'));
```

### 注意

这个实现提供了如何实现 `extended` 选项的基本思路。实际的 `express.urlencoded()` 使用 `{ extended: true }` 时会使用 `qs` 库，该库能够更好地处理复杂的嵌套对象和数组。对于解析 URL 编码的数据，特别是处理复杂结构时，建议在生产环境中依赖内置的 Express 中间件或直接使用 `qs` 库。


---
# English version
## what is the express.urlencoded
The `express.urlencoded()` middleware in Express.js is another built-in function that plays a crucial role in handling incoming requests. This middleware is specifically designed to parse incoming request bodies when the content type is `application/x-www-form-urlencoded`. This content type is commonly used in HTML forms. Here's an overview of its principle and functionality:

### Principle of `express.urlencoded()`

1. **Purpose**:
   - The primary purpose of `express.urlencoded()` is to parse incoming request bodies in a format that web browsers typically use when sending data from a form using the POST method.

2. **Content Type**:
   - It specifically handles the `application/x-www-form-urlencoded` content type. This format is standard for form submissions in HTML forms, where the form data is encoded as key-value pairs.

### Functionality

1. **Parsing Form Data**:
   - When a client submits data from a form, that data is sent to the server as a URL-encoded string (where characters are encoded as URL escape codes).
   - `express.urlencoded()` middleware parses this URL-encoded data into a JavaScript object.

2. **Attachment to Request Object**:
   - After parsing, the middleware attaches the resulting object to the `req.body` property, similar to `express.json()`. This makes the form data easily accessible in the route handlers or subsequent middleware.

3. **Configuration Options**:
   - `extended`: This option configures the middleware to use either the `querystring` library (when false) or the `qs` library (when true) for parsing. The `qs` library allows for a richer object creation, such as arrays and nested objects.
   - `limit`: Sets the maximum size of request bodies, providing control over the allowable amount of data.

4. **Common Usage**:
   - Commonly used in scenarios where your server needs to accept data submitted from HTML forms.
   - Especially important for handling POST requests where form data is submitted.

### Example Usage

```javascript
const express = require('express');
const app = express();

// Activate express.urlencoded middleware
app.use(express.urlencoded({ extended: true }));

app.post('/form-submit', (req, res) => {
    console.log(req.body); // Form data from the client
    // ...rest of the route logic
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

In this example, any POST request to `/form-submit` that sends data from an HTML form (with the content type `application/x-www-form-urlencoded`) will have its body parsed into a JavaScript object accessible through `req.body`.

### Conclusion

`express.urlencoded()` is an essential middleware for handling form submissions in Express.js applications. Its ability to parse URL-encoded form data makes it indispensable for web applications where form handling is a common requirement. Just like `express.json()`, it's a key component of the Express.js middleware ecosystem, facilitating the easy and efficient processing of client-submitted data.

## DIY a express.urlencoded
Implementing a basic version of the `express.urlencoded()` middleware involves parsing URL-encoded data from the request body. This type of data is typically sent by HTML forms. The basic steps to create such a middleware are:

1. **Check the Content-Type**: Ensure the request's Content-Type is `application/x-www-form-urlencoded`.
2. **Read and Parse the Request Body**: URL-encoded data is sent as a string in the request body. This string needs to be parsed into a JavaScript object.
3. **Attach the Parsed Data to `req.body`**: After parsing, attach the resulting object to the `req.body` property for easy access in subsequent middleware or route handlers.

Here's a step-by-step guide with corresponding code:

### Step 1: Set Up Basic Middleware Structure

Define the structure of the middleware function. It should have three parameters: `req`, `res`, and `next`.

```javascript
function customUrlEncodedParser(req, res, next) {
    // Middleware logic will go here
}
```

### Step 2: Check Content-Type

Only proceed with parsing if the Content-Type is `application/x-www-form-urlencoded`.

```javascript
function customUrlEncodedParser(req, res, next) {
    if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
        // Proceed with parsing
    } else {
        next(); // Skip this middleware if not URL-encoded
    }
}
```

### Step 3: Read and Parse the Request Body

Read the request body using streams. URL-encoded data comes as a string, which we need to parse into key-value pairs.

```javascript
function customUrlEncodedParser(req, res, next) {
    if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
        let body = '';

        req.on('data', chunk => {
            body += chunk.toString(); // convert Buffer to string
        });

        req.on('end', () => {
            req.body = parseUrlEncoded(body);
            next();
        });
    } else {
        next();
    }
}

function parseUrlEncoded(str) {
    const pairs = str.split('&');
    return pairs.reduce((acc, pair) => {
        const [key, value] = pair.split('=');
        acc[decodeURIComponent(key)] = decodeURIComponent(value);
        return acc;
    }, {});
}
```

### Step 4: Using the Middleware in Your Express App

Use this middleware in your Express application just as you would with `express.urlencoded()`.

```javascript
const express = require('express');
const app = express();

app.use(customUrlEncodedParser);

app.post('/form-submit', (req, res) => {
    console.log(req.body); // Parsed form data
    res.send('Form data received');
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

In this setup, any POST request to `/form-submit` with URL-encoded data (from an HTML form) will be parsed into a JavaScript object accessible through `req.body`.

### Note

This implementation is a basic version for demonstration. Real-world scenarios might require handling more complex data structures, decoding nested objects, or dealing with arrays. The actual `express.urlencoded()` middleware is more sophisticated and handles various edge cases and configurations, making it a better choice for production environments.

## extend to implement option config
The `{ extended: true }` option in `express.urlencoded({ extended: true })` is an important configuration setting that determines how the URL-encoded data is parsed. Specifically, it tells the middleware which parsing library to use:

- When `extended` is set to `true`, the middleware uses the `qs` library for parsing. This library allows for rich objects and arrays to be encoded into the URL-encoded format, meaning it can handle nested objects.
- When `extended` is set to `false`, the middleware uses the `querystring` library, which does not support nested objects.

To implement a basic version of this functionality, you can write a custom parsing function that mimics this behavior. Here's how you can do it:

### Step 1: Extended Parsing Function

First, define a function to handle the parsing based on the `extended` option. For simplicity, we'll implement a basic way to parse nested objects when `extended` is true. A full implementation would require a more robust parsing algorithm or directly using the `qs` library.

```javascript
function parseUrlEncoded(str, extended) {
    if (!extended) {
        // Simple parsing (not handling nested objects)
        return str.split('&').reduce((acc, pair) => {
            const [key, value] = pair.split('=');
            acc[decodeURIComponent(key)] = decodeURIComponent(value);
            return acc;
        }, {});
    } else {
        // Basic implementation for extended parsing (handling nested objects)
        // Note: This is for demonstration. A complete implementation would be more complex.
        return str.split('&').reduce((acc, pair) => {
            const [key, value] = pair.split('=');
            const path = key.split('[').map(k => k.replace(/]$/, ''));
            let current = acc;
            for (let i = 0; i < path.length - 1; i++) {
                if (!current[path[i]]) current[path[i]] = {};
                current = current[path[i]];
            }
            current[path[path.length - 1]] = decodeURIComponent(value);
            return acc;
        }, {});
    }
}
```

### Step 2: Update the Middleware

Modify the custom middleware to accept an options object and use the parsing function accordingly.

```javascript
function customUrlEncodedParser(options = {}) {
    const extended = options.extended || false;

    return function (req, res, next) {
        if (req.headers['content-type'] === 'application/x-www-form-urlencoded') {
            let body = '';

            req.on('data', chunk => {
                body += chunk.toString(); // Convert Buffer to string
            });

            req.on('end', () => {
                req.body = parseUrlEncoded(body, extended);
                next();
            });
        } else {
            next();
        }
    };
}
```

### Step 3: Using the Middleware with Options

Now you can use this middleware with the `extended` option.

```javascript
const express = require('express');
const app = express();

// Use the custom middleware with extended option
app.use(customUrlEncodedParser({ extended: true }));

app.post('/form-submit', (req, res) => {
    console.log(req.body); // Parsed form data
    res.send('Form data received');
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

### Note

This implementation provides a basic idea of how the `extended` option might be implemented. The actual `express.urlencoded()` with `{ extended: true }` uses the `qs` library, which is much more capable of handling complex nested objects and arrays. For production use, relying on the built-in Express middleware or the `qs` library directly is recommended for parsing URL-encoded data, especially when dealing with complex structures.
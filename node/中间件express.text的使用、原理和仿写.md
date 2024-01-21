`express.text()` 中间件用于解析传入请求的正文内容作为纯文本。它是Express.js中body-parser中间件功能的一部分。本文是对其使用和原理的解释，并演示如何通过一个小demo实现这个中间件的功能。

### `express.text()` 的原理和用法
1. **解析**：它解析带有 `Content-Type: text/plain` 的传入请求的正文。
2. **访问**：解析后的文本通过 `req.body` 可以访问。
3. **配置**：可以配置中间件以处理不同的字符编码，限制正文大小等。

### 使用步骤
1. **引入中间件**：在应用程序中添加 `express.text()` 来处理纯文本正文。
2. **配置（可选）**：可以向 `express.text()` 传递选项来自定义其行为。
3. **数据访问**：在路由处理器中使用 `req.body` 来访问解析后的文本数据。

### 使用 `express.text()` 的示例代码
```javascript
const express = require('express');
const app = express();

// 配置 express.text() 中间件以解析纯文本正文
app.use(express.text());

// 接收纯文本正文的路由
app.post('/receive-text', (req, res) => {
  console.log(req.body); // 将纯文本正文记录到控制台
  res.send('接收到纯文本数据！');
});

app.listen(3000, () => {
  console.log('服务器正在端口3000上运行');
});
```

### 模拟 `express.text()` 中间件
让我们创建一个简单的自定义中间件来模拟 `express.text()` 的基本功能：

```javascript
const express = require('express');
const app = express();

// 自定义中间件以解析 text/plain 正文
const customTextParser = (req, res, next) => {
  if (req.headers['content-type'] === 'text/plain') {
    let data = '';
    req.on('data', chunk => {
      data += chunk;
    });
    req.on('end', () => {
      req.body = data;
      next();
    });
  } else {
    next();
  }
};

app.use(customTextParser);

// 接收纯文本正文的路由
app.post('/receive-text', (req, res) => {
  console.log(req.body); // 将纯文本正文记录到控制台
  res.send('接收到纯文本数据！');
});

app.listen(3000, () => {
  console.log('服务器正在端口3000上运行');
});
```

在这个自定义中间件 `customTextParser` 中，我们检查 `Content-Type` 头部，确保只处理纯文本正文。我们读取数据块，并将它们连成一个数据变量。当数据传输完成时，我们将连好的数据赋值给 `req.body` 并调用 `next()`，将控制权传递给下一个中间件或路由处理器。

这个简单版本没有处理错误、编码或内容长度限制，但它捕捉了 `express.text()` 工作的核心原理。

---

# English version

The `express.text()` middleware is used to parse the body of incoming requests as plain text. It's part of the Express.js body-parser middleware functionalities. Below is an explanation of its usage and principles, followed by a demonstration of how you might implement a simplified version of this middleware.

### Principles and Usage of `express.text()`
1. **Parsing**: It parses the body of incoming requests with `Content-Type: text/plain`.
2. **Access**: The parsed text is then made available on `req.body`.
3. **Configuration**: It can be configured to handle different character encodings, limit the size of the body, and more.

### Step by Step Usage
1. **Include the Middleware**: Add `express.text()` in your application to process plain text bodies.
2. **Configure (Optional)**: You can pass options to `express.text()` to customize its behavior.
3. **Access Data**: Use `req.body` within your route handlers to access the parsed text data.

### Example Code with `express.text()`
```javascript
const express = require('express');
const app = express();

// Configure express.text() middleware for parsing plain text bodies
app.use(express.text());

// Route that receives a plain text body
app.post('/receive-text', (req, res) => {
  console.log(req.body); // Logs the plain text body to the console
  res.send('Received plain text data!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

### Mimicking `express.text()` Middleware
Let's create a simple custom middleware that mimics the basic functionality of `express.text()`:

```javascript
const express = require('express');
const app = express();

// Custom middleware to parse text/plain bodies
const customTextParser = (req, res, next) => {
  if (req.headers['content-type'] === 'text/plain') {
    let data = '';
    req.on('data', chunk => {
      data += chunk;
    });
    req.on('end', () => {
      req.body = data;
      next();
    });
  } else {
    next();
  }
};

app.use(customTextParser);

// Route that receives a plain text body
app.post('/receive-text', (req, res) => {
  console.log(req.body); // Logs the plain text body to the console
  res.send('Received plain text data!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

In this custom middleware `customTextParser`, we're checking the `Content-Type` header to ensure we only process plain text bodies. We read the data chunks as they come in and concatenate them to a data variable. When the data transmission is complete, we assign the concatenated data to `req.body` and call `next()` to pass control to the next middleware or route handler.

This simple version doesn't handle errors, encoding, or content length limits, but it captures the core principle of how `express.text()` works.
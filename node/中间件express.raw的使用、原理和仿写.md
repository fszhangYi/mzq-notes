`express.raw()` 是Express.js提供的中间件，用于将传入请求的正文解析为Buffer。这通常用于处理二进制数据，或者当需要以自定义方式处理正文数据时。本文是对其使用和原理的解释，并演示如何通过一个小demo实现这个中间件的功能。

### `express.raw()` 的原理及使用
1. **解析**: 解析传入请求的正文，将其作为原始缓冲区。
2. **访问**: 解析后的原始缓冲区可以通过 `req.body` 访问。
3. **配置**: 可以配置多个选项，如限制正文的大小等。

### 使用步骤
1. **引入中间件**: 在应用中使用 `express.raw()` 来处理原始二进制正文。
2. **配置(可选)**: 可以向 `express.raw()` 传入选项来自定义其行为，如设置正文大小限制。
3. **数据访问**: 在路由处理函数中使用 `req.body` 访问原始Buffer数据。

### `express.raw()` 示例代码
```javascript
const express = require('express');
const app = express();

// 为解析原始二进制正文配置 express.raw() 中间件
app.use(express.raw({ type: 'application/octet-stream' }));

// 路由，用于接收原始二进制正文
app.post('/receive-raw', (req, res) => {
  console.log(req.body); // 在控制台记录原始Buffer
  res.send('已接收原始二进制数据！');
});

app.listen(3000, () => {
  console.log('服务器运行于3000端口');
});
```

### 模拟 `express.raw()` 中间件
以下是实现此中间件基本功能的简单版本：

```javascript
const express = require('express');
const app = express();

// 自定义中间件，用于解析原始二进制正文
const customRawParser = (req, res, next) => {
  if (req.headers['content-type'] === 'application/octet-stream') {
    let data = [];
    req.on('data', chunk => {
      data.push(chunk);
    });
    req.on('end', () => {
      req.body = Buffer.concat(data);
      next();
    });
  } else {
    next();
  }
};

app.use(customRawParser);

// 路由，用于接收原始二进制正文
app.post('/receive-raw', (req, res) => {
  console.log(req.body); // 在控制台记录原始Buffer
  res.send('已接收原始二进制数据！');
});

app.listen(3000, () => {
  console.log('服务器运行于3000端口');
});
```

在自定义的 `customRawParser` 中间件中，通过将数据块累积到数组中。数据传输完成后，使用 `Buffer.concat` 将所有数据块合并为一个Buffer实例，然后赋值给 `req.body`。这样，下一个中间件或路由处理函数就可以访问原始二进制数据了。


---

# English version

`express.raw()` is another middleware provided by Express.js that is used to parse incoming request bodies as a Buffer. This is typically used when you want to handle binary data or you want to process the body data yourself in a custom way.

### Principles and Usage of `express.raw()`
1. **Parsing**: It parses the body of incoming requests and exposes the raw buffer.
2. **Access**: The raw buffer is then available on `req.body`.
3. **Configuration**: It can be configured for various options, such as limiting the size of the body.

### Step by Step Usage
1. **Include the Middleware**: Use `express.raw()` in your application to process raw binary bodies.
2. **Configure (Optional)**: Pass options to `express.raw()` to customize, such as setting a limit to the body size.
3. **Access Data**: Use `req.body` within your route handlers to access the raw Buffer data.

### Example Code with `express.raw()`
```javascript
const express = require('express');
const app = express();

// Configure express.raw() middleware for parsing raw binary bodies
app.use(express.raw({ type: 'application/octet-stream' }));

// Route that receives a raw binary body
app.post('/receive-raw', (req, res) => {
  console.log(req.body); // Logs the raw Buffer to the console
  res.send('Received raw binary data!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

### Mimicking `express.raw()` Middleware
Here's how you could implement a simple version of this middleware:

```javascript
const express = require('express');
const app = express();

// Custom middleware to parse raw binary bodies
const customRawParser = (req, res, next) => {
  if (req.headers['content-type'] === 'application/octet-stream') {
    let data = [];
    req.on('data', chunk => {
      data.push(chunk);
    });
    req.on('end', () => {
      req.body = Buffer.concat(data);
      next();
    });
  } else {
    next();
  }
};

app.use(customRawParser);

// Route that receives a raw binary body
app.post('/receive-raw', (req, res) => {
  console.log(req.body); // Logs the raw Buffer to the console
  res.send('Received raw binary data!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

In this custom `customRawParser` middleware, we accumulate chunks of data into an array. When the data transmission is complete, we use `Buffer.concat` to merge all chunks into a single `Buffer` instance, which we then assign to `req.body`. This allows the next middleware or route handler to access the raw binary data.
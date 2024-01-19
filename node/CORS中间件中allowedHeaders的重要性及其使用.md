本文介绍在使用express的Cors中间件的时候，配置项中`allowedHeaders`的作用，并提供了在postman中进行测试的思路。
### `allowedHeaders` 是什么？

在 CORS (跨源资源共享) 的上下文中，`allowedHeaders` 用于指定允许发送到服务器的 HTTP 请求中哪些头部是被允许的。当客户端发起跨域请求时，可能会发送额外的头部（如 `Content-Type`、`Authorization`、`X-Requested-With` 等）。出于安全原因，服务器需要明确声明哪些头部是可接受的。

### `allowedHeaders` 为何重要？

1. **安全性**：通过指定允许哪些头部，可以防止未授权的头部被用于可能的恶意方式。
2. **控制性**：这提供了对客户端与服务器通信时所需或允许的信息的控制。

### 默认行为

如果没有指定 `allowedHeaders`，CORS 中间件将自动允许安全且常用的头部。然而，如果应用程序需要特定的自定义头部，则需要明确定义它们。

### 代码示例

以下是在使用 `cors` 包的 Express.js 应用程序中设置 `allowedHeaders` 的基本示例：

#### 步骤 1：安装 CORS 包

```bash
npm install cors
```

#### 步骤 2：设置 Express 应用程序

```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// 定义允许的头部
const corsOptions = {
    allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With'],
};

// 启用 CORS 并指定选项
app.use(cors(corsOptions));

app.get('/', (req, res) => {
    res.send('启用 CORS，指定特定允许的头部');
});

const PORT = 3000;
app.listen(PORT, () => console.log(`服务器运行在端口 ${PORT}`));
```

在此示例中，通过 `allowedHeaders` 选项设置仅允许 `Content-Type`、`Authorization` 和 `X-Requested-With` 头部在跨域请求中通过。客户端发送的任何不在此列表中的其他头部都将被服务器拒绝。

### 测试设置

可以使用 Postman 或 CURL 等工具通过向服务器发送不同头部集的请求来测试此设置。应该会看到，带有允许头部的请求可以通过，而其他的被阻止。

### 结论

在处理跨域请求的 web 应用程序中，理解并正确配置 CORS 设置中的 `allowedHeaders` 对于安全性和功能性至关重要。这种控制确保服务器只接受必要且安全的头部，维护应用程序数据交换的完整性。

---
# English version

### What are `allowedHeaders`?

In the context of CORS, `allowedHeaders` specifies which HTTP headers are allowed in requests sent to your server. When a client makes a cross-origin request, it may send additional headers (like `Content-Type`, `Authorization`, `X-Requested-With`, etc.). For security reasons, the server needs to explicitly state which of these headers are acceptable.

### Why is `allowedHeaders` Important?

1. **Security**: By specifying which headers are allowed, you prevent unauthorized headers that could potentially be used in malicious ways.
2. **Control**: It gives you control over what information is required or allowed when clients communicate with your server.

### Default Behavior

If `allowedHeaders` is not specified, CORS middleware will automatically allow headers that are safe and commonly used. However, if your application requires specific custom headers, you need to explicitly define them.

### Code Example

Let's see a basic example of setting up `allowedHeaders` in an Express.js application using the `cors` package:

#### Step 1: Install CORS Package

```bash
npm install cors
```

#### Step 2: Set Up Your Express Application

```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// Define allowed headers
const corsOptions = {
    allowedHeaders: ['Content-Type', 'Authorization', 'X-Requested-With'],
};

// Enable CORS with specified options
app.use(cors(corsOptions));

app.get('/', (req, res) => {
    res.send('CORS-enabled with specific allowed headers');
});

const PORT = 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

In this example, the `allowedHeaders` option is set to allow only `Content-Type`, `Authorization`, and `X-Requested-With` headers in cross-origin requests. Any other headers sent by the client that are not in this list will be rejected by the server.

### Testing Your Setup

You can test this setup using tools like Postman or CURL by making requests to your server with different sets of headers. You should see that requests with allowed headers pass through, while others are blocked.

### Conclusion

Understanding and properly configuring `allowedHeaders` in CORS settings is crucial for the security and functionality of web applications dealing with cross-origin requests. This control ensures that your server only accepts the necessary and safe headers, maintaining the integrity of your application's data exchange.
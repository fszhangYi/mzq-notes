本文介绍了在使用express的时候使用的中间件cors中的credentials配置项的作用，从正反两个角度对其作用进行了阐述并附上对应的代码。

# 理解 CORS 中的 `credentials` 选项

CORS 中的 `credentials` 选项用于指定是否应在跨源请求中包含 cookies。当设置为 `true` 时，它允许浏览器在请求中发送 cookies 和 HTTP 认证信息。

### 后端设置 (Node.js/Express)

1. **安装 CORS 包**:
   ```bash
   npm install cors
   ```

2. **带 CORS `credentials` 的 Express 设置**:
   ```javascript
   const express = require('express');
   const cors = require('cors');

   const app = express();

   const corsOptions = {
       origin: 'http://example-frontend.com', // 替换为您前端的 URL
       credentials: true,
   };

   app.use(cors(corsOptions));

   app.get('/', (req, res) => {
       res.send('启用了带凭据的 CORS');
   });

   const PORT = 3000;
   app.listen(PORT, () => console.log(`服务器运行在端口 ${PORT}`));
   ```

   在此设置中，`credentials: true` 允许浏览器在向您的 Express 服务器发送请求时附带 cookies 和 HTTP 认证头。

### 前端设置 (JavaScript)

对于前端，您需要配置 HTTP 请求以包含凭据（如 cookies）。以下是您使用纯 JavaScript 以及像 Axios 或 Fetch 这样的流行库的方式。

1. **纯 JavaScript (Fetch API)**:
   ```javascript
   fetch('http://example-backend.com', {
       method: 'GET',
       credentials: 'include', // 确保发送 cookies
   })
   .then(response => response.text())
   .then(data => console.log(data))
   .catch(error => console.error('Error:', error));
   ```

2. **使用 Axios**:
   ```javascript
   axios.get('http://example-backend.com', { withCredentials: true })
        .then(response => console.log(response.data))
        .catch(error => console.error('Error:', error));
   ```

### 浏览器、后端和前端如何协同工作

1. **浏览器的角色**:
   浏览器出于安全原因执行同源策略。当对不同来源发出请求时，浏览器会检查服务器响应中的相应 CORS 头（`Access-Control-Allow-Origin`、`Access-Control-Allow-Credentials` 等）以确定是否允许该请求。

2. **发送凭据**:
   - 前端必须明确请求与跨源请求一起发送凭据（cookies，HTTP Auth）。
   - 后端必须明确允许来自请求来源的凭据。

3. **Cookies 和认证**:
   - 如果包含凭据，后端设置的 cookies 将随前端的每个请求发送，如果使用了 HTTP 认证，也会包含其中。

### 安全考虑

在 CORS 中启用凭据应谨慎进行，因为如果配置不正确，可能会使应用程序暴露于某些类型的攻击。建议明确指定允许的来源，而不是使用通配符（`*`），并确保应用程序具有适当的会话管理和安全措施。

---

反面情况：

### 后端设置 `credentials: false`

当在 Express 服务器的 CORS 配置中将 `credentials` 选项设置为 `false` 时，它指示服务器不在跨源请求中包含凭据（如 cookies 或 HTTP 认证）。代码如下所示：

```javascript
// Express 服务器设置中的 CORS credentials 设置为 false
const express = require('express');
const cors = require('cors');

const app = express();

const corsOptions = {
    origin: 'http://example-frontend.com', // 替换为您前端的 URL
    credentials: false,
};

app.use(cors(corsOptions));

app.get('/', (req, res) => {
    res.send('启用了不带凭据的 CORS');
});

const PORT = 3000;
app.listen(PORT, () => console.log(`服务器运行在端口 ${PORT}`));
```

在此设置中，即使前端在请求

中包含了凭据，服务器也会忽略它们。这意味着 cookies 和 HTTP 认证头不会被发送或接收。

### 前端设置 `withCredentials: false` 或等效

在前端，如果您将 `withCredentials` 设置为 `false`（或省略它，默认为 `false`），即使服务器配置为接受它们，浏览器也不会在请求中发送 cookies 或 HTTP 认证信息。

1. **纯 JavaScript (Fetch API)**:
   ```javascript
   fetch('http://example-backend.com', {
       method: 'GET',
       // 不包含凭据
   })
   .then(response => response.text())
   .then(data => console.log(data))
   .catch(error => console.error('Error:', error));
   ```

2. **使用 Axios**:
   ```javascript
   axios.get('http://example-backend.com')
        .then(response => console.log(response.data))
        .catch(error => console.error('Error:', error));
   ```

### 这些情况下会发生什么？

1. **后端设置 `credentials: false`**:
   - 服务器不接受来自客户端的凭据。
   - 服务器设置的 cookies 或 HTTP 认证不会包含在响应给客户端的数据中。
   - 即使前端发送了带有凭据的请求，服务器也不会承认。

2. **前端设置 `withCredentials: false`**:
   - 浏览器不会在请求中发送 cookies 或 HTTP 认证信息，无论服务器的配置如何。
   - 这是除非明确设置为 `true` 外的默认行为。

### 关键点

- CORS 中的 `credentials` 选项和前端的 `withCredentials` 标志需要一致，才能在请求中包含凭据。如果任何一方不允许凭据，它们将不会被发送或接收。
- 禁用凭据是一种常见的安全措施，特别是当服务器和客户端不需要交换像 cookies 或认证细节这样的敏感信息时。

理解这些配置有助于有效管理应用程序如何处理涉及凭据的跨源请求。

### 何时使用

CORS 中的 `credentials` 选项根据您的 web 应用程序的要求在不同情况下使用。以下是 Markdown 格式的表格，概述了一些常见情况：

| 情况 | 使用 `credentials` | 描述 |
|------|--------------------|------|
| **用户认证** | `true` | 当您的应用程序需要用户认证（如登录会话），并且此信息存储在 cookies 中。 |
| **需要认证令牌的 API** | `true` | 对于使用存储在 cookies 中的令牌进行授权和访问控制的 API。 |
| **第三方服务** | `false` | 当访问第三方 API 或服务时，认证由其他方式处理，或不需要认证。 |
| **公共 API** | `false` | 为公共访问而设计的 API，不需要任何用户特定数据或认证。 |
| **静态内容传输** | `false` | 传输静态内容如图片、样式表和脚本时，不需要用户会话或认证。 |
| **内部服务通信** | 视情况而定 | 对于微服务架构，是否使用 `credentials` 取决于服务之间的认证策略。如果服务通过 cookies 或认证头进行认证，则设置 `credentials` 为 `true`。 |

记住，由于安全影响，设置 `credentials` 为 `true` 应该是经过深思熟虑的决定。只有在绝对必要并且有适当安全措施到位时才应启用。

### 结论

CORS 中的 `credentials` 选项是 web 应用程序中允许客户端和服务器之间进行更丰富、更安全交互的强

大功能。正确设置后端和前端以处理凭据可以确保身份验证和会话数据的无缝流动，同时维护浏览器同源策略强制的安全性。

---

# English version
### Understanding the `credentials` Option

The `credentials` option in CORS is used to specify whether cookies should be included in cross-origin requests. When set to `true`, it allows browsers to send cookies and HTTP Authentication information with the request.

### Backend Setup (Node.js/Express)

1. **Install CORS Package**:
   ```bash
   npm install cors
   ```

2. **Express Setup with CORS `credentials`**:
   ```javascript
   const express = require('express');
   const cors = require('cors');

   const app = express();

   const corsOptions = {
       origin: 'http://example-frontend.com', // Replace with your frontend's URL
       credentials: true,
   };

   app.use(cors(corsOptions));

   app.get('/', (req, res) => {
       res.send('CORS-enabled with credentials');
   });

   const PORT = 3000;
   app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
   ```

   In this setup, `credentials: true` allows the browser to send cookies and HTTP Authentication headers along with the request to your Express server.

### Frontend Setup (JavaScript)

For the frontend, you need to configure your HTTP requests to include credentials (like cookies). Here's how you would do it with plain JavaScript and also with popular libraries like Axios or Fetch.

1. **Plain JavaScript (Fetch API)**:
   ```javascript
   fetch('http://example-backend.com', {
       method: 'GET',
       credentials: 'include', // Ensures cookies are sent
   })
   .then(response => response.text())
   .then(data => console.log(data))
   .catch(error => console.error('Error:', error));
   ```

2. **Using Axios**:
   ```javascript
   axios.get('http://example-backend.com', { withCredentials: true })
        .then(response => console.log(response.data))
        .catch(error => console.error('Error:', error));
   ```

### How Browser, Backend, and Frontend Work Together

1. **Browser's Role**:
   The browser enforces the same-origin policy for security reasons. When a request is made to a different origin, the browser checks for appropriate CORS headers (`Access-Control-Allow-Origin`, `Access-Control-Allow-Credentials`, etc.) in the server's response to determine if the request should be allowed.

2. **Sending Credentials**:
   - The frontend must explicitly ask to send credentials (cookies, HTTP Auth) with cross-origin requests.
   - The backend must explicitly allow credentials from the requesting origin.

3. **Cookies and Authentication**:
   - If credentials are included, cookies set by the backend will be sent with each request from the frontend, and HTTP Authentication (if used) will also be included.

### Security Considerations

Enabling credentials in CORS should be done with caution, as it can expose your application to certain types of attacks if not correctly configured. It's recommended to explicitly specify which origins are allowed rather than using a wildcard (`*`), and to ensure that your application has proper session management and security measures in place.

---

Look at the opposite side:

### Backend with `credentials: false`

When the `credentials` option is set to `false` in the CORS configuration on your Express server, it instructs the server not to include credentials (like cookies or HTTP authentication) in cross-origin requests. Here's how it looks in code:

```javascript
// Express server setup with CORS credentials set to false
const express = require('express');
const cors = require('cors');

const app = express();

const corsOptions = {
    origin: 'http://example-frontend.com', // Replace with your frontend's URL
    credentials: false,
};

app.use(cors(corsOptions));

app.get('/', (req, res) => {
    res.send('CORS-enabled without credentials');
});

const PORT = 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

In this setup, even if the frontend makes a request with credentials included, the server will ignore them. This means cookies and HTTP authentication headers will not be sent or received.

### Frontend with `withCredentials: false` or Equivalent

On the frontend, if you set `withCredentials` to `false` (or omit it, as it defaults to `false`), the browser will not send cookies or HTTP authentication information with the request, even if the server is configured to accept them.

1. **Plain JavaScript (Fetch API)**:
   ```javascript
   fetch('http://example-backend.com', {
       method: 'GET',
       // credentials not included
   })
   .then(response => response.text())
   .then(data => console.log(data))
   .catch(error => console.error('Error:', error));
   ```

2. **Using Axios**:
   ```javascript
   axios.get('http://example-backend.com')
        .then(response => console.log(response.data))
        .catch(error => console.error('Error:', error));
   ```

### What Happens in These Scenarios?

1. **Backend with `credentials: false`**:
   - The server does not accept credentials from the client.
   - Cookies or HTTP authentication set by the server will not be included in the response to the client.
   - Even if the frontend sends a request with credentials, they will not be acknowledged by the server.

2. **Frontend with `withCredentials: false`**:
   - The browser will not send cookies or HTTP authentication information with the request, regardless of the server's configuration.
   - This is the default behavior unless explicitly set to `true`.

### Key Takeaway

- The `credentials` option in CORS and the `withCredentials` flag on the frontend need to be in agreement for credentials to be included in requests. If either side disallows credentials, they will not be sent or received.
- Disabling credentials is a common security measure, especially when the server and client do not need to exchange sensitive information like cookies or authentication details. 

Understanding these configurations helps in effectively managing how your application handles cross-origin requests concerning credentials.

### when to use
Certainly! The `credentials` option in CORS is used in various scenarios, depending on the requirements of your web application. Here's a table in Markdown format outlining some common scenarios:

| Scenario | Use of `credentials` | Description |
|----------|----------------------|-------------|
| **User Authentication** | `true` | When your application requires user authentication (like login sessions), and this information is stored in cookies. |
| **APIs Requiring Auth Tokens** | `true` | For APIs that use tokens passed in cookies for authorization and access control. |
| **Third-party Services** | `false` | When accessing third-party APIs or services where authentication is handled by other means, or not required. |
| **Public APIs** | `false` | For APIs intended for public access without any user-specific data or authentication. |
| **Static Content Delivery** | `false` | When serving static content like images, stylesheets, and scripts, where no user session or authentication is needed. |
| **Internal Services Communication** | Depends | For microservices architecture, it depends on whether these services require authentication via cookies. |


### Explanation of Scenarios:

- **User Authentication**: If your application has user login functionality, and the session information is stored in cookies, you need to set `credentials` to `true` to include these cookies in requests.

- **APIs Requiring Auth Tokens**: Similar to user authentication, if your API uses tokens stored in cookies for validating requests, `credentials` should be set to `true`.

- **Third-party Services**: When your application is consuming APIs from third-party services where cookies or auth headers are not necessary, it's safer to keep `credentials` as `false`.

- **Public APIs**: APIs designed for public access, without any restriction or authentication, should have `credentials` set to `false` to prevent unnecessary credential transmission.

- **Static Content Delivery**: Serving static files like images or CSS doesn't typically require user context, so `credentials` can be set to `false`.

- **Internal Services Communication**: In a microservices architecture, whether to use `credentials` depends on the authentication strategy between services. If services authenticate each other using cookies or auth headers, set `credentials` to `true`.

Remember, setting `credentials` to `true` should be a well-considered decision due to the security implications it carries. It should only be enabled when absolutely necessary and when proper security measures are in place.

### Conclusion

The `credentials` option in CORS is a powerful feature that allows for richer, more secure interactions between the client and the server in web applications. Correctly setting up both the backend and frontend to handle credentials ensures a seamless flow of authentication and session data while maintaining the security enforced by the browser's same-origin policy.
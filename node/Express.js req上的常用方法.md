在 Express.js 中，`request` 对象（通常称为 `req`）代表 HTTP 请求，具有请求查询字符串、参数、正文、HTTP 头等的属性。本文介绍 `req` 对象的一些关键属性和方法。

### 1. `req.params`
- **功能**: 包含映射到命名路由参数的属性的对象。例如，如果您有路由 `/user/:name`，则可以通过 `req.params.name` 访问 `name` 属性。
- **示例**:
  ```javascript
  app.get('/user/:name', (req, res) => {
    res.send(`你好, ${req.params.name}`);
  });
  ```

### 2. `req.query`
- **功能**: 包含路由中每个查询字符串参数的属性的对象。如果查询字符串为 `?name=John&age=30`，`req.query` 将是 `{ name: 'John', age: '30' }`。
- **示例**:
  ```javascript
  app.get('/search', (req, res) => {
    const { name, age } = req.query;
    res.send(`搜索结果：${name}，年龄 ${age}`);
  });
  ```

### 3. `req.body`
- **功能**: 包含请求正文中提交的键值对。默认情况下，它是 `undefined`，当您使用诸如 `express.json()` 或 `express.urlencoded()` 之类的解析中间件时，它会被填充。
- **示例**:
  ```javascript
  app.post('/login', express.json(), (req, res) => {
    const { username, password } = req.body;
    res.send(`正在登录 ${username}`);
  });
  ```

### 4. `req.headers`
- **功能**: 包含请求的 HTTP 头的对象。例如，`req.headers['content-type']` 将给出请求的内容类型。
- **示例**:
  ```javascript
  app.get('/api', (req, res) => {
    const contentType = req.headers['content-type'];
    res.send(`内容类型是 ${contentType}`);
  });
  ```

### 5. `req.method`
- **功能**: 表示请求的 HTTP 方法的字符串：GET、POST、PUT、DELETE 等。
- **示例**:
  ```javascript
  app.use((req, res, next) => {
    console.log(`收到一个 ${req.method} 请求`);
    next();
  });
  ```

### 6. `req.url` 或 `req.originalUrl`
- **功能**: `req.url` 提供 URL 的路径部分，而 `req.originalUrl` 提供完整的原始 URL。
- **示例**:
  ```javascript
  app.use((req, res, next) => {
    console.log(`原始 URL: ${req.originalUrl}`);
    next();
  });
  ```

### 7. `req.path`
- **功能**: 包含请求 URL 的路径部分。
- **示例**:
  ```javascript
  app.get('/example', (req, res) => {
    console.log(`路径: ${req.path}`);
    res.send('路径示例');
  });
  ```

### 8. `req.cookies`
- **功能**: 包含请求发送的 cookies 的对象。需要使用 `cookie-parser` 中间件。
- **示例**:
  ```javascript
  app.use(cookieParser());
  app.get('/', (req, res) => {
    console.log('Cookies: ', req.cookies);
    res.send('已接收 Cookie 数据');
  });
  ```

### 9. `req.hostname`
- **功能**: 包含从 `Host` HTTP 头派生的主机名。
- **示例**:
  ```javascript
  app.get('/host', (req, res) => {
    res.send(`主机名: ${req.hostname}`);
  });
  ```

### 10. `req.ip`
- **功能**: 包含请求的远程 IP 地址。
- **示例**:
  ```javascript
  app.use((req, res, next) => {
    console.log(`IP 地址: ${req.ip}`);
    next();
  });
  ```

### 11. `req.protocol`
- **功能**: 包含请求协议字符串：http 或 https（对于 TLS 请求）。
- **示例**:
  ```javascript
  app.get('/protocol', (req, res) => {
    res.send(`协议: ${req.protocol}`);
  });
  ```

### 12. `req.secure`
- **功能**: 如果建立了 TLS 连接，则为 true 的布尔属性。
- **示例**:
  ```javascript
  app.get('/secure', (req, res) => {
    res.send(`是否安全: ${req.secure}`);
  });
  ```

这些 `req` 对象的属性和方法对于处理 Express.js 应用程序中的传入请求至关重要，使您能够访问请求参数、头部和正文内容等。

---

# English version

In Express.js, the `request` object (commonly referred to as `req`) represents the HTTP request and has properties for the request query string, parameters, body, HTTP headers, and more. Let's dive into some of the key properties and methods of the `req` object, along with explanations and code examples:

### 1. `req.params`
- **Functionality**: An object containing properties mapped to the named route parameters. For example, if you have the route `/user/:name`, then the `name` property is available as `req.params.name`.
- **Example**:
  ```javascript
  app.get('/user/:name', (req, res) => {
    res.send(`Hello, ${req.params.name}`);
  });
  ```

### 2. `req.query`
- **Functionality**: An object containing a property for each query string parameter in the route. If there is a query string `?name=John&age=30`, `req.query` would be `{ name: 'John', age: '30' }`.
- **Example**:
  ```javascript
  app.get('/search', (req, res) => {
    const { name, age } = req.query;
    res.send(`Search results for ${name} aged ${age}`);
  });
  ```

### 3. `req.body`
- **Functionality**: Contains key-value pairs of data submitted in the request body. By default, it is `undefined`, and is populated when you use body-parsing middleware such as `express.json()` or `express.urlencoded()`.
- **Example**:
  ```javascript
  app.post('/login', express.json(), (req, res) => {
    const { username, password } = req.body;
    res.send(`Logging in ${username}`);
  });
  ```

### 4. `req.headers`
- **Functionality**: An object containing the request’s HTTP headers. For example, `req.headers['content-type']` will give you the content type of the request.
- **Example**:
  ```javascript
  app.get('/api', (req, res) => {
    const contentType = req.headers['content-type'];
    res.send(`Content-Type is ${contentType}`);
  });
  ```

### 5. `req.method`
- **Functionality**: A string representing the HTTP method of the request: GET, POST, PUT, DELETE, etc.
- **Example**:
  ```javascript
  app.use((req, res, next) => {
    console.log(`Received a ${req.method} request`);
    next();
  });
  ```

### 6. `req.url` or `req.originalUrl`
- **Functionality**: `req.url` provides the path part of the URL, whereas `req.originalUrl` provides the full original URL.
- **Example**:
  ```javascript
  app.use((req, res, next) => {
    console.log(`Original URL: ${req.originalUrl}`);
    next();
  });
  ```

### 7. `req.path`
- **Functionality**: Contains the path part of the request URL.
- **Example**:
  ```javascript
  app.get('/example', (req, res) => {
    console.log(`Path: ${req.path}`);
    res.send('Path example');
  });
  ```

### 8. `req.cookies`
- **Functionality**: An object containing the cookies sent by the request. Requires the `cookie-parser` middleware to be used.
- **Example**:
  ```javascript
  app.use(cookieParser());
  app.get('/', (req, res) => {
    console.log('Cookies: ', req.cookies);
    res.send('Cookie data received');
  });
  ```

### 9. `req.hostname`
- **Functionality**: Contains the hostname derived from the `Host` HTTP header.
- **Example**:
  ```javascript
  app.get('/host', (req, res) => {
    res.send(`Hostname: ${req.hostname}`);
  });
  ```

### 10. `req.ip`
- **Functionality**: Contains the remote IP address of the request.
- **Example**:
  ```javascript
  app.use((req, res, next) => {
    console.log(`IP address: ${req.ip}`);
    next();
  });
  ```

### 11. `req.protocol`
- **Functionality**: Contains the request protocol string: either http or (for TLS requests) https.
- **Example**:
  ```javascript
  app.get('/protocol', (req, res) => {
    res.send(`Protocol: ${req.protocol}`);
  });
  ```

### 12. `req.secure`
- **Functionality**: A Boolean property that is true if a TLS connection is established.
- **Example**:
  ```javascript
  app.get('/secure', (req, res) => {}`);
  });
  ```

These properties and methods of the `req` object are essential for handling incoming requests in an Express.js application, allowing you to access request parameters, headers, and body content, among other things.

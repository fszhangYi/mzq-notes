本文介绍了在Nodejs环境下使用express建立一个Api服务的时候如何正确的使用状态码和提示信息。喜欢的可以收藏起来，方便以后使用。
## 1. 基本使用
### 2xx - 成功

- **200 OK**：请求成功。当 API 成功返回 GET 请求的数据时使用此状态码。
  ```javascript
  app.get('/users', (req, res) => {
    // 获取用户的逻辑...
    res.status(200).json(users);
  });
  ```

- **201 Created**：请求成功且已创建新资源。在 POST 请求成功添加新记录时使用此状态码。
  ```javascript
  app.post('/users', (req, res) => {
    // 创建用户的逻辑...
    res.status(201).json(newUser);
  });
  ```

- **204 No Content**：服务器成功处理了请求，但不返回任何内容。在 DELETE 请求完成时使用此状态码。
  ```javascript
  app.delete('/users/:id', (req, res) => {
    // 删除用户的逻辑...
    res.status(204).send();
  });
  ```

### 4xx - 客户端错误

- **400 Bad Request**：由于客户端错误（例如，请求语法错误），服务器无法处理请求。
  ```javascript
  app.post('/users', (req, res) => {
    if (!req.body.name || !req.body.email) {
      res.status(400).json({ error: '需要提供姓名和电子邮件' });
    }
    // 继续用户创建过程...
  });
  ```

- **401 Unauthorized**：请求缺少目标资源的有效认证凭据。
  ```javascript
  app.get('/private-data', (req, res) => {
    if (!req.headers.authorization) {
      res.status(401).json({ error: '未发送认证信息！' });
    }
    // 继续数据检索...
  });
  ```

- **403 Forbidden**：服务器理解请求但拒绝授权。
  ```javascript
  app.delete('/users/:id', (req, res) => {
    if (!userCanDelete(req.user)) {
      res.status(403).json({ error: '您无权删除此用户' });
    }
    // 继续用户删除过程...
  });
  ```

- **404 Not Found**：服务器找不到请求的资源。
  ```javascript
  app.get('/users/:id', (req, res) => {
    const user = findUserById(req.params.id);
    if (!user) {
      res.status(404).json({ error: '未找到用户' });
    }
    // 继续返回用户信息...
  });
  ```

- **422 Unprocessable Entity**：服务器理解请求实体的内容类型，并且请求实体的语法是正确的，但无法处理所包含的指令。
  ```javascript
  app.post('/users', (req, res) => {
    const validationErrors = validateUser(req.body);
    if (validationErrors) {
      res.status(422).json({ errors: validationErrors });
    }
    // 继续用户创建过程...
  });
  ```

### 5xx - 服务器错误

- **500 Internal Server Error**：遇到意外情况且没有更具体的消息时给出的一般错误消息。
  ```javascript
  app.get('/users', (req, res) => {
    try {
      // 尝试获取用户...
    } catch (error) {
      res.status(500).json({ error: '内部服务器错误' });
    }
  });
  ```

- **503 Service Unavailable**：服务器尚未准备好处理请求。常见原因是服务器正在维护或过载。
  ```javascript
  app.use((req, res) => {
    res.status(503).json({ error: '服务当前不可用。请稍后再试。' });
  });
  ```

## 2. 高级使用

### 2xx - 成功

- **202 Accepted**：请求已经收到但尚未处理。这是非承诺性的，因为没有保证会执行该动作。
  ```javascript
  app.post('/tasks', async (req, res) => {
    const task = scheduleTask(req.body);
    res.status(202).json({ message: '任务已接收，将会被处理' });
  });
  ```

- **206 Partial Content**：由于客户端发送的范围头部，服务器只交付资源的一部分。
  ```javascript
  app.get('/video', (req, res) => {
    // 处理范围请求的逻辑...
    res.status(206).send(videoData);
  });
  ```

### 3xx - 重定向

- **301 Moved Permanently**：此响应码意味着请求的资源的 URI 已永久更改。
  ```javascript
  app.get('/old-page', (req, res) => {
    res.redirect(301, '/new-page');
  });
  ```

- **304 Not Modified**：表示自上次请求以来资源未被修改。
  ```javascript
  app.get('/cacheable-data', (req, res) => {
    // 判断数据是否发生变化的逻辑...
    res.status(304).end();
  });
  ```

### 4xx - 客户端错误

- **405 Method Not Allowed**：不支持对请求资源使用的请求方法。
  ```javascript
  app.post('/read-only-resource', (req, res) => {
    res.status(405).json({ error: '此资源不允许 POST 方法' });
  });
  ```

- **409 Conflict**：表示请求与服务器当前状态冲突。
  ```javascript
  app.post('/users', (req, res) => {
    const userExists = checkUserExists(req.body.email);
    if (userExists) {
      res.status(409).json({ error: '用户已存在' });
    }
    // 继续用户创建过程...
  });
  ```

- **429 Too Many Requests**：用户在给定时间内发送了太多请求（“限制频率”）。
  ```javascript
  app.get('/rate-limited', (req, res) => {
    // 检查频率限制的逻辑...
    res.status(429).json({ error: '请求过多。请稍后再试。' });
  });
  ```

### 5xx - 服务器错误

- **501 Not Implemented**：服务器不识别请求方法，或者缺乏完成请求的能力。
  ```javascript
  app.trace('/users', (req, res) => {
    res.status(501).json({ error: 'TRACE 方法未实现' });
  });
  ```

- **504 Gateway Timeout**：服务器充当网关或代理，未及时从上游服务器接收响应。
  ```javascript
  app.get('/proxy', (req, res) => {
    // 代理逻辑...
    res.status(504).json({ error: '网关超时' });
  });
  ```

## 3. 实际应用
### 1xx - 信息性响应

- **100 Continue**:
  ```javascript
  app.post('/large-file-upload', (req, res) => {
    // 处理文件上传的逻辑...
    res.status(100).send('Continue');
  });
  ```

- **101 Switching Protocols**:
  ```javascript
  app.get('/websocket', (req, res) => {
    // 切换到 WebSocket 协议的逻辑...
    res.status(101).send('Switching Protocols');
  });
  ```

- **102 Processing**:
  ```javascript
  app.get('/long-process', (req, res) => {
    // 处理耗时过程的逻辑...
    res.status(102).send('Processing');
  });
  ```

- **103 Checkpoint**:
  ```javascript
  app.get('/save-progress', (req, res) => {
    // 保存当前进度的逻辑...
    res.status(103).send('Checkpoint');
  });
  ```

### 2xx - 成功

- **200 OK**:
  ```javascript
  app.get('/users', (req, res) => {
    // 获取用户的逻辑...
    res.status(200).json(users);
  });
  ```

- **201 Created**:
  ```javascript
  app.post('/users', (req, res) => {
    // 创建用户的逻辑...
    res.status(201).json(newUser);
  });
  ```

- **202 Accepted**:
  ```javascript
  app.post('/tasks', (req, res) => {
    // 任务调度的逻辑...
    res.status(202).json({ message: '任务已接受并正在处理' });
  });
  ```

- **203 Non-Authoritative Information**:
  ```javascript
  app.get('/proxy-data', (req, res) => {
    // 通过代理获取数据的逻辑...
    res.status(203).json(proxyData);
  });
  ```

- **204 No Content**:
  ```javascript
  app.delete('/users/:id', (req, res) => {
    // 删除用户的逻辑...
    res.status(204).send();
  });
  ```

- **205 Reset Content**:
  ```javascript
  app.post('/reset-form', (req, res) => {
    // 重置表单数据的逻辑...
    res.status(205).send('Reset Content');
  });
  ```

- **206 Partial Content**:
  ```javascript
  app.get('/video', (req, res) => {
    // 流式传输视频内容的逻辑...
    res.status(206).send(videoData);
  });
  ```

- **207 Multi-Status**:
  ```javascript
  app.get('/batch-request', (req, res) => {
    // 处理批量请求的逻辑...
    res.status(207).json(multiStatusResponse);
  });
  ```

- **208 Already Reported**:
  ```javascript
  app.get('/report-status', (req, res) => {
    // 报告任务状态的逻辑...
    res.status(208).json({ message: 'Already Reported' });
  });
  ```

- **226 IM Used**:
  ```javascript
  app.get('/delta-encoding', (req, res) => {
    // 处理增量编码的逻辑...
    res.status(226).json(deltaEncodedData);
  });
  ```

### 3xx - 重定向

- **300 Multiple Choices**:
  ```javascript
  app.get('/document', (req, res) => {
    // 提供多种选项的逻辑...
    res.status(300).json({ options: ['PDF', 'Word', 'HTML'] });
  });
  ```

- **301 Moved Permanently**:
  ```javascript
  app.get('/old-page', (req, res) => {
    res.redirect(301, '/new-page');
  });
  ```

- **302 Found**:
  ```javascript
  app.get('/redirect', (req, res) => {
    res.redirect(302, '/another-page');
  });
  ```

- **303 See Other**:
  ```javascript
  app.post('/submit-data', (req, res) => {
    // 处理数据的逻辑...
    res.redirect(303, '/confirmation');
  });
  ```

- **304 Not Modified**:
  ```javascript
  app.get('/cacheable-data', (req, res) => {
    // 判断数据是否已更改的逻辑...
    res.status(304).end();
  });
  ```

- **307 Temporary Redirect**:
  ```javascript
  app.get('/temp-redirect', (req, res) => {
    res.redirect(307, '/temporary-page');
  });
  ```

- **308 Permanent Redirect**:
  ```javascript
  app.get('/old-endpoint', (req, res) => {
    res.redirect(308, '/new-endpoint');
  });
  ```

### 4xx - 客户端错误

- **400 Bad Request**:
  ```javascript
  app.post('/users', (req, res) => {
    if (!req.body.name) {
      res.status(400).json({ error: '姓名是必需的' });
    }
    // 继续用户创建逻辑...
  });
  ```

- **401 Unauthorized**:
  ```javascript
  app.get('/private-data', (req, res) => {
    if (!req.headers.authorization) {
      res.status(401).json({ error: '未经授权的访问' });
    }
    // 继续数据检索逻辑...
  });
  ```

- **402 Payment Required**:
  ```javascript
  app.get('/premium-content', (req, res) => {
    if (!userHasSubscription(req.user)) {
      res.status(402).json({ error: '需要支付才能访问' });
    }
    // 继续提供内容...
  });
  ```

- **403 Forbidden**:
  ```javascript
  app.delete('/restricted-data', (req, res) => {
    if (!userHasPermission(req.user)) {
      res.status(403).json({ error: '禁止：权限不足' });
    }
    // 继续删除逻辑...
  });
  ```

- **404 Not Found**:
  ```javascript
  app.get('/users/:id', (req, res) => {
    const user = findUserById(req.params.id);
    if (!user) {
      res.status(404).json({ error: '未找到用户' });
    }
    // 继续逻辑操作...
  });
  ```

- **405 Method Not Allowed**:
  ```javascript
  app.post('/read-only-resource', (req, res) => {
    res.status(405).json({ error: '此资源不允许该方法' });
  });
  ```

- **406 Not Acceptable**:
  ```javascript
  app.get('/data', (req, res) => {
    if (!req.accepts('json')) {
      res.status(406).json({ error: '不可接受：需要 JSON' });
    }
    // 继续提供 JSON 数据...
  });
  ```

- **407 Proxy Authentication Required**:
  ```javascript
  app.get('/proxy-resource', (req, res) => {
    // 需要代理认证的逻辑...
    res.status(407).json({ error: '需要代理认证' });
  });
  ```

- **408 Request Timeout**:
  ```javascript
  app.post('/time-sensitive-operation', (req, res) => {
    // 操作逻辑...
    if (operationTimedOut) {
      res.status(408).send('请求超时');
    }
  });
  ```

- **409 Conflict**:
  ```javascript
  app.post('/users', (req, res) => {
    if (usernameExists(req.body.username)) {
      res.status(409).json({ error: '用户名已存在' });
    }
    // 继续用户创建...
  });
  ```

- **410 Gone**:
  ```javascript
  app.get('/old-resource', (req, res) => {
    res.status(410).json({ error: '资源不再可用' });
  });
  ```

- **411 Length Required**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (!req.headers['content-length']) {
      res.status(411).json({ error: '文件上传需要长度' });
    }
    // 继续上传逻辑...
  });
  ```

- **412 Precondition Failed**:
  ```javascript
  app.get('/conditional-request', (req, res) => {
    if (!meetsPrecondition(req)) {
      res.status(412).json({ error: '先决条件失败' });
    }
    // 继续逻辑操作...
  });
  ```

- **413 Payload Too Large**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (req.headers['content-length'] > MAX_SIZE) {
      res.status(413).json({ error: '有效载荷过大' });
    }
    // 继续上传逻辑...
  });
  ```

- **414 URI Too Long**:
  ```javascript
  app.get('/search', (req, res) => {
    if (req.url.length > MAX_URL_LENGTH) {
      res.status(414).json({ error: 'URI 太长' });
    }
    // 继续搜索逻辑...
  });
  ```

- **415 Unsupported Media Type**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (!isValidMediaType(req)) {
      res.status(415).json({ error: '不支持的媒体类型' });
    }
    // 继续上传逻辑...
  });
  ```

- **416 Requested Range Not Satisfiable**:
  ```javascript
  app.get('/large-file', (req, res) => {
    // 处理大文件的范围请求逻辑...
    if (rangeNotSatisfiable) {
      res.status(416).json({ error: '无法满足的请求范围' });
    }
  });
  ```

- **417 Expectation Failed**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (!req.is('multipart/form-data')) {
      res.status(417).json({ error: '期望失败：Content-Type 必须是 multipart/form-data' });
    }
    // 继续上传逻辑...
  });
  ```

- **418 I'm a Teapot**（这是在超文本咖啡壶控制协议中定义的新颖状态码）:
  ```javascript
  app.get('/teapot', (req, res) => {
    res.status(418).json({ message: "我是一个茶壶" });
  });
  ```

- **422 Unprocessable Entity**:
  ```javascript
  app.post('/user', (req, res) => {
    const errors = validateUser(req.body);
    if (errors) {
      res.status(422).json({ errors });
    }
    // 继续用户创建...
  });
  ```

- **429 Too Many Requests**:
  ```javascript
  app.get('/rate-limited-endpoint', (req, res) => {
    if (clientExceededRateLimit(req)) {
      res.status(429).json({ error: '请求过多' });
    }
    // 继续请求处理...
  });
  ```

### 5xx - 服务器错误

- **500 Internal Server Error**:
  ```javascript
  app.get('/data', (req, res) => {
    try {
      // 获取数据的逻辑...
    } catch (error) {
      res.status(500).json({ error: '内部服务器错误' });
    }
  });
  ```

- **503 Service Unavailable**:
  ```javascript
  app.get('/maintenance', (req, res) => {
    res.status(503).json({ error: '服务不可用' });
  });
  ```

- **504 Gateway Timeout**:
  ```javascript
  app.get('/proxy', (req, res) => {
    // 代理请求逻辑...
    if (proxyTimeout) {
      res.status(504).json({ error: '网关超时' });
    }
  });
  ```

- **505 HTTP Version Not Supported**:
  ```javascript
  app.use((req, res, next) => {
    if (req.httpVersion !== '1.1') {
      res.status(505).json({ error: '不支持的 HTTP 版本' });
    } else {
      next();
    }
  });
  ```

- **506 Variant Also Negotiates**:
  ```javascript
  app.get('/variant', (req, res) => {
    // 导致协商错误的逻辑...
    res.status(506).json({ error: '变体也协商' });
  });
  ```

- **507 Insufficient Storage**:
  ```javascript
  app.post('/upload', (req, res) => {
    // 处理文件上传的逻辑...
    if (insufficientServerStorage) {
      res.status(507).json({ error: '存储不足' });
    }
    // 继续上传逻辑...
  });
  ```

- **508 Loop Detected**:
  ```javascript
  app.get('/recursive-redirect', (req, res) => {
    // 检测到递归重定向的逻辑...
    res.status(508).json({ error: '检测到循环' });
  });
  ```

- **509 Bandwidth Limit Exceeded**:
  ```javascript
  app.get('/data', (req, res) => {
    // 监控带宽使用的逻辑...
    if (bandwidthLimitExceeded) {
      res.status(509).json({ error: '超出带宽限制' });
    }
    // 继续数据处理...
  });
  ```

- **510 Not Extended**:
  ```javascript
  app.get('/extended', (req, res) => {
    // 需要进一步扩展的逻辑...
    res.status(510).json({ error: '未扩展' });
  });
  ```

- **511 Network Authentication Required**:
  ```javascript
  app.get('/protected-resource', (req, res) => {
    // 检查网络级别认证的逻辑...
    if (!isAuthenticatedAtNetworkLevel(req)) {
      res.status(511).json({ error: '需要网络认证' });
    }
    // 继续请求处理...
  });
  ```

### Node.js API 服务器注意事项

- **验证**：使用像 Joi 这样的库来验证请求负载，并在验证失败时使用 `422 Unprocessable Entity` 状态码。
- **认证和授权**：对于需要用户登录的路由，如果用户未登录，使用 `401 Unauthorized` 状态码；如果用户没有正确的权限，使用 `403 Forbidden` 状态码。
- **内容协商**：如果您的 API 支持多种表示形式（如 JSON 和 XML），并且当请求的类型不可用时应返回 `406 Not Acceptable`。
- **弃用**：通过使用自定义头部或状态码（如果合适）来告知消费者弃用的端点或特性。
- **CORS**：通过设置适当的头部来处理跨源资源共享（CORS）。如果一个源不被允许，可以使用 `403 Forbidden`。

### 在 Node.js API 中使用状态码的提示

- 始终发送带有适当状态码的响应。
- 包括解释状态码的消息或主体，特别是对于错误代码。
- 在您的 API 中一致地使用状态码。
- 使用像 Express 这样的框架中的中间件来集中处理错误和设置状态码。
- 为了您自己的调试，在服务器上记录错误，特别是对于 5xx 错误。

在创建 Node.js API 时，推荐使用 Express 这样的框架，它简化了请求的处理和状态码的设置，正如示例中所示。

---
# English version
## 1. Basic Use
### 2xx - Success

- **200 OK**: The request has succeeded. Use this when your API successfully returns data for a GET request.
  ```javascript
  app.get('/users', (req, res) => {
    // Fetch users logic...
    res.status(200).json(users);
  });
  ```

- **201 Created**: The request has succeeded and a new resource has been created. Use this for successful POST requests where a new record is added.
  ```javascript
  app.post('/users', (req, res) => {
    // Create user logic...
    res.status(201).json(newUser);
  });
  ```

- **204 No Content**: The server successfully processed the request, but is not returning any content. Use this when a DELETE request completes.
  ```javascript
  app.delete('/users/:id', (req, res) => {
    // Delete user logic...
    res.status(204).send();
  });
  ```

### 4xx - Client Errors

- **400 Bad Request**: The server cannot process the request due to client error (e.g., malformed request syntax).
  ```javascript
  app.post('/users', (req, res) => {
    if (!req.body.name || !req.body.email) {
      res.status(400).json({ error: 'Name and email are required' });
    }
    // Continue with user creation...
  });
  ```

- **401 Unauthorized**: The request lacks valid authentication credentials for the target resource.
  ```javascript
  app.get('/private-data', (req, res) => {
    if (!req.headers.authorization) {
      res.status(401).json({ error: 'No credentials sent!' });
    }
    // Continue with data retrieval...
  });
  ```

- **403 Forbidden**: The server understood the request but refuses to authorize it.
  ```javascript
  app.delete('/users/:id', (req, res) => {
    if (!userCanDelete(req.user)) {
      res.status(403).json({ error: 'You do not have permission to delete this user' });
    }
    // Continue with user deletion...
  });
  ```

- **404 Not Found**: The server can't find the requested resource.
  ```javascript
  app.get('/users/:id', (req, res) => {
    const user = findUserById(req.params.id);
    if (!user) {
      res.status(404).json({ error: 'User not found' });
    }
    // Continue with returning the user...
  });
  ```

- **422 Unprocessable Entity**: The server understands the content type of the request entity, and the syntax of the request entity is correct, but it was unable to process the contained instructions.
  ```javascript
  app.post('/users', (req, res) => {
    const validationErrors = validateUser(req.body);
    if (validationErrors) {
      res.status(422).json({ errors: validationErrors });
    }
    // Continue with user creation...
  });
  ```

### 5xx - Server Errors

- **500 Internal Server Error**: A generic error message, given when an unexpected condition was encountered and no more specific message is suitable.
  ```javascript
  app.get('/users', (req, res) => {
    try {
      // Attempt to fetch users...
    } catch (error) {
      res.status(500).json({ error: 'Internal server error' });
    }
  });
  ```

- **503 Service Unavailable**: The server is not ready to handle the request. Common causes are a server that is down for maintenance or that is overloaded.
  ```javascript
  app.use((req, res) => {
    res.status(503).json({ error: 'Service is currently unavailable. Please try again later.' });
  });
  ```
  
## 2. Advanced Use

### 2xx - Success

- **202 Accepted**: The request has been received but not yet acted upon. It is non-committal, since there is no guarantee that the action will be carried out.
  ```javascript
  app.post('/tasks', async (req, res) => {
    const task = scheduleTask(req.body);
    res.status(202).json({ message: 'Task received and will be processed' });
  });
  ```

- **206 Partial Content**: The server is delivering only part of the resource due to a range header sent by the client.
  ```javascript
  app.get('/video', (req, res) => {
    // Logic to handle range requests...
    res.status(206).send(videoData);
  });
  ```

### 3xx - Redirection

- **301 Moved Permanently**: This response code means that the URI of the requested resource has been changed permanently.
  ```javascript
  app.get('/old-page', (req, res) => {
    res.redirect(301, '/new-page');
  });
  ```

- **304 Not Modified**: Indicates that the resource has not been modified since the last request.
  ```javascript
  app.get('/cacheable-data', (req, res) => {
    // Logic to determine if data has changed...
    res.status(304).end();
  });
  ```

### 4xx - Client Errors

- **405 Method Not Allowed**: A request method is not supported for the requested resource.
  ```javascript
  app.post('/read-only-resource', (req, res) => {
    res.status(405).json({ error: 'POST method is not allowed for this resource' });
  });
  ```

- **409 Conflict**: Indicates a request conflict with current state of the server.
  ```javascript
  app.post('/users', (req, res) => {
    const userExists = checkUserExists(req.body.email);
    if (userExists) {
      res.status(409).json({ error: 'User already exists' });
    }
    // Continue with user creation...
  });
  ```

- **429 Too Many Requests**: The user has sent too many requests in a given amount of time ("rate limiting").
  ```javascript
  app.get('/rate-limited', (req, res) => {
    // Logic to check rate limits...
    res.status(429).json({ error: 'Too many requests. Please try again later.' });
  });
  ```

### 5xx - Server Errors

- **501 Not Implemented**: The server either does not recognize the request method, or it lacks the ability to fulfill the request.
  ```javascript
  app.trace('/users', (req, res) => {
    res.status(501).json({ error: 'TRACE method is not implemented' });
  });
  ```

- **504 Gateway Timeout**: The server was acting as a gateway or proxy and did not receive a timely response from the upstream server.
  ```javascript
  app.get('/proxy', (req, res) => {
    // Proxy logic...
    res.status(504).json({ error: 'Gateway timeout' });
  });
  ```
  
## 3. Use in Practice
### 1xx - Informational Responses

- **100 Continue**:
  ```javascript
  app.post('/large-file-upload', (req, res) => {
    // Logic to handle file upload...
    res.status(100).send('Continue');
  });
  ```

- **101 Switching Protocols**:
  ```javascript
  app.get('/websocket', (req, res) => {
    // Logic for switching to WebSocket protocol...
    res.status(101).send('Switching Protocols');
  });
  ```

- **102 Processing**:
  ```javascript
  app.get('/long-process', (req, res) => {
    // Logic for a process that takes time...
    res.status(102).send('Processing');
  });
  ```

- **103 Checkpoint**:
  ```javascript
  app.get('/save-progress', (req, res) => {
    // Logic for saving current progress...
    res.status(103).send('Checkpoint');
  });
  ```

### 2xx - Success

- **200 OK**:
  ```javascript
  app.get('/users', (req, res) => {
    // Fetch users logic...
    res.status(200).json(users);
  });
  ```

- **201 Created**:
  ```javascript
  app.post('/users', (req, res) => {
    // Create user logic...
    res.status(201).json(newUser);
  });
  ```

- **202 Accepted**:
  ```javascript
  app.post('/tasks', (req, res) => {
    // Task scheduling logic...
    res.status(202).json({ message: 'Task accepted and processing' });
  });
  ```

- **203 Non-Authoritative Information**:
  ```javascript
  app.get('/proxy-data', (req, res) => {
    // Logic for fetching data through a proxy...
    res.status(203).json(proxyData);
  });
  ```

- **204 No Content**:
  ```javascript
  app.delete('/users/:id', (req, res) => {
    // Delete user logic...
    res.status(204).send();
  });
  ```

- **205 Reset Content**:
  ```javascript
  app.post('/reset-form', (req, res) => {
    // Logic to reset form data...
    res.status(205).send('Reset Content');
  });
  ```

- **206 Partial Content**:
  ```javascript
  app.get('/video', (req, res) => {
    // Logic for streaming video content...
    res.status(206).send(videoData);
  });
  ```

- **207 Multi-Status**:
  ```javascript
  app.get('/batch-request', (req, res) => {
    // Logic for handling batch requests...
    res.status(207).json(multiStatusResponse);
  });
  ```

- **208 Already Reported**:
  ```javascript
  app.get('/report-status', (req, res) => {
    // Logic for reporting status of a task...
    res.status(208).json({ message: 'Already Reported' });
  });
  ```

- **226 IM Used**:
  ```javascript
  app.get('/delta-encoding', (req, res) => {
    // Logic for handling delta encoding...
    res.status(226).json(deltaEncodedData);
  });
  ```

### 3xx - Redirection

- **300 Multiple Choices**:
  ```javascript
  app.get('/document', (req, res) => {
    // Logic for presenting multiple options...
    res.status(300).json({ options: ['PDF', 'Word', 'HTML'] });
  });
  ```

- **301 Moved Permanently**:
  ```javascript
  app.get('/old-page', (req, res) => {
    res.redirect(301, '/new-page');
  });
  ```

- **302 Found**:
  ```javascript
  app.get('/redirect', (req, res) => {
    res.redirect(302, '/another-page');
  });
  ```

- **303 See Other**:
  ```javascript
  app.post('/submit-data', (req, res) => {
    // Logic to process data...
    res.redirect(303, '/confirmation');
  });
  ```

- **304 Not Modified**:
  ```javascript
  app.get('/cacheable-data', (req, res) => {
    // Logic to determine if data has changed...
    res.status(304).end();
  });
  ```

- **307 Temporary Redirect**:
  ```javascript
  app.get('/temp-redirect', (req, res) => {
    res.redirect(307, '/temporary-page');
  });
  ```

- **308 Permanent Redirect**:
  ```javascript
  app.get('/old-endpoint', (req, res) => {
    res.redirect(308, '/new-endpoint');
  });
  ```

### 4xx - Client Errors

- **400 Bad Request**:
  ```javascript
  app.post('/users', (req, res) => {
    if (!req.body.name) {
      res.status(400).json({ error: 'Name is required' });
    }
    // Continue user creation logic...
  });
  ```

- **401 Unauthorized**:
  ```javascript
  app.get('/private-data', (req, res) => {
    if (!req.headers.authorization) {
      res.status(401).json({ error: 'Unauthorized access' });
    }
    // Continue data retrieval logic...
  });
  ```

- **402 Payment Required**:
  ```javascript
  app.get('/premium-content', (req, res) => {
    if (!userHasSubscription(req.user)) {
      res.status(402).json({ error: 'Payment required for access' });
    }
    // Continue serving content...
  });
  ```

- **403 Forbidden**:
  ```javascript
  app.delete('/restricted-data', (req, res) => {
    if (!userHasPermission(req.user)) {
      res.status(403).json({ error: 'Forbidden: insufficient permissions' });
    }
    // Continue deletion logic...
  });
  ```

- **404 Not Found**:
  ```javascript
  app.get('/users/:id', (req, res) => {
    const user = findUserById(req.params.id);
    if (!user) {
      res.status(404).json({ error: 'User not found' });
    }
    // Continue with logic...
  });
  ```

- **405 Method Not Allowed**:
  ```javascript
  app.post('/read-only-resource', (req, res) => {
    res.status(405).json({ error: 'Method not allowed for this resource' });
  });
  ```

- **406 Not Acceptable**:
  ```javascript
  app.get('/data', (req, res) => {
    if (!req.accepts('json')) {
      res.status(406).json({ error: 'Not acceptable: JSON required' });
    }
    // Continue serving JSON data...
  });
  ```

- **407 Proxy Authentication Required**:
  ```javascript
  app.get('/proxy-resource', (req, res) => {
    // Logic requiring proxy authentication...
    res.status(407).json({ error: 'Proxy authentication required' });
  });
  ```

- **408 Request Timeout**:
  ```javascript
  app.post('/time-sensitive-operation', (req, res) => {
    // Logic for operation...
    if (operationTimedOut) {
      res.status(408).send('Request Timeout');
    }
  });
  ```

- **409 Conflict**:
  ```javascript
  app.post('/users', (req, res) => {
    if (usernameExists(req.body.username)) {
      res.status(409).json({ error: 'Username already exists' });
    }
    // Continue user creation...
  });
  ```

- **410 Gone**:
  ```javascript
  app.get('/old-resource', (req, res) => {
    res.status(410).json({ error: 'Resource no longer available' });
  });
  ```

- **411 Length Required**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (!req.headers['content-length']) {
      res.status(411).json({ error: 'Length required for file upload' });
    }
    // Continue upload logic...
  });
  ```

- **412 Precondition Failed**:
  ```javascript
  app.get('/conditional-request', (req, res) => {
    if (!meetsPrecondition(req)) {
      res.status(412).json({ error: 'Precondition failed' });
    }
    // Continue with logic...
  });
  ```

- **413 Payload Too Large**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (req.headers['content-length'] > MAX_SIZE) {
      res.status(413).json({ error: 'Payload too large' });
    }
    // Continue upload logic...
  });
  ```

- **414 URI Too Long**:
  ```javascript
  app.get('/search', (req, res) => {
    if (req.url.length > MAX_URL_LENGTH) {
      res.status(414).json({ error: 'URI too long' });
    }
    // Continue with search logic...
  });
  ```

- **415 Unsupported Media Type**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (!isValidMediaType(req)) {
      res.status(415).json({ error: 'Unsupported media type' });
    }
    // Continue with upload logic...
  });
  ```

- **416 Requested Range Not Satisfiable**:
  ```javascript
  app.get('/large-file', (req, res) => {
    // Logic to handle range requests for a large file...
    if (rangeNotSatisfiable) {
      res.status(416).json({ error: 'Requested range not satisfiable' });
    }
  });
  ```

- **417 Expectation Failed**:
  ```javascript
  app.post('/upload', (req, res) => {
    if (!req.is('multipart/form-data')) {
      res.status(417).json({ error: 'Expectation Failed: Content-Type must be multipart/form-data' });
    }
    // Continue with upload logic...
  });
  ```

- **418 I'm a Teapot** (This is a novelty status code defined in the Hyper Text Coffee Pot Control Protocol):
  ```javascript
  app.get('/teapot', (req, res) => {
    res.status(418).json({ message: "I'm a teapot" });
  });
  ```

- **422 Unprocessable Entity**:
  ```javascript
  app.post('/user', (req, res) => {
    const errors = validateUser(req.body);
    if (errors) {
      res.status(422).json({ errors });
    }
    // Continue with user creation...
  });
  ```

- **429 Too Many Requests**:
  ```javascript
  app.get('/rate-limited-endpoint', (req, res) => {
    if (clientExceededRateLimit(req)) {
      res.status(429).json({ error: 'Too Many Requests' });
    }
    // Proceed with request handling...
  });
  ```

### 5xx - Server Errors

- **500 Internal Server Error**:
  ```javascript
  app.get('/data', (req, res) => {
    try {
      // Logic to fetch data...
    } catch (error) {
      res.status(500).json({ error: 'Internal Server Error' });
    }
  });
  ```

- **503 Service Unavailable**:
  ```javascript
  app.get('/maintenance', (req, res) => {
    res.status(503).json({ error: 'Service Unavailable' });
  });
  ```

- **504 Gateway Timeout**:
  ```javascript
  app.get('/proxy', (req, res) => {
    // Proxy request logic...
    if (proxyTimeout) {
      res.status(504).json({ error: 'Gateway Timeout' });
    }
  });
  ```

- **505 HTTP Version Not Supported**:
  ```javascript
  app.use((req, res, next) => {
    if (req.httpVersion !== '1.1') {
      res.status(505).json({ error: 'HTTP Version not supported' });
    } else {
      next();
    }
  });
  ```

- **506 Variant Also Negotiates**:
  ```javascript
  app.get('/variant', (req, res) => {
    // Logic that causes a negotiation error...
    res.status(506).json({ error: 'Variant Also Negotiates' });
  });
  ```

- **507 Insufficient Storage**:
  ```javascript
  app.post('/upload', (req, res) => {
    // Logic to handle file upload...
    if (insufficientServerStorage) {
      res.status(507).json({ error: 'Insufficient Storage' });
    }
    // Continue with upload logic...
  });
  ```

- **508 Loop Detected**:
  ```javascript
  app.get('/recursive-redirect', (req, res) => {
    // Logic detecting a recursive redirection...
    res.status(508).json({ error: 'Loop Detected' });
  });
  ```

- **509 Bandwidth Limit Exceeded**:
  ```javascript
  app.get('/data', (req, res) => {
    // Logic to monitor bandwidth usage...
    if (bandwidthLimitExceeded) {
      res.status(509).json({ error: 'Bandwidth Limit Exceeded' });
    }
    // Continue with data handling...
  });
  ```

- **510 Not Extended**:
  ```javascript
  app.get('/extended', (req, res) => {
    // Logic requiring further extensions...
    res.status(510).json({ error: 'Not Extended' });
  });
  ```

- **511 Network Authentication Required**:
  ```javascript
  app.get('/protected-resource', (req, res) => {
    // Logic to check network-level authentication...
    if (!isAuthenticatedAtNetworkLevel(req)) {
      res.status(511).json({ error: 'Network Authentication Required' });
    }
    // Continue with request handling...
  });
  ```

These examples demonstrate the potential use of each status code in specific situations within a Node.js API. It's important to choose the most appropriate status code for each situation to ensure clear communication of the server's state and the result of the client's request.


### Additional Tips for Node.js APIs

- **Validation**: Use a library like Joi to validate request payloads and use the `422 Unprocessable Entity` status code when validation fails.
- **Authentication and Authorization**: For routes that require a user to be logged in, use the `401 Unauthorized` status if they're not, or `403 Forbidden` if they don't have the right permissions.
- **Content Negotiation**: If your API supports multiple representations (like JSON and XML), and if a `406 Not Acceptable` should be returned when the requested type isn't available.
- **Deprecation**: Inform consumers of deprecated endpoints or features by using custom headers or status codes if appropriate.
- **CORS**: Handle Cross-Origin Resource Sharing (CORS) by setting the appropriate headers. If an origin is not allowed, you can use `403 Forbidden`.

By using status codes appropriately, you can make your API more intuitive and easier to use, which can greatly improve the developer experience for consumers of your API.

### Tips for Using Status Codes in Node.js APIs

- Always send a response with an appropriate status code.
- Include a message or body that explains the status code, especially for error codes.
- Be consistent in your use of status codes across your API.
- Use middleware in frameworks like Express to handle errors and set status codes centrally.
- Log your errors on the server for your own debugging, especially for 5xx errors.

For creating an API in Node.js, you may be using a framework like Express, which simplifies the handling of requests and the setting of status codes as shown in the examples. Make sure to familiarize yourself with the documentation of the framework you choose for more specific ways to handle responses and errors.
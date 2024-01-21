本文介绍在使用express框架时，res对象上11种常用方法的使用结合代码分别做了介绍。
#### 1. `res.send([body])`
- **功能**: 发送各种类型的数据响应，如`Buffer`、`String`、`Object`或`Array`。Express会根据响应类型自动分配正确的内容类型。
- **示例**:
  ```javascript
  app.get('/', (req, res) => {
    res.send('<h1>你好，世界</h1>'); // 发送text/html响应
  });
  ```

#### 2. `res.json([body])`
- **功能**: 发送JSON响应。这个方法类似于`res.send()`，但始终将非Buffer对象转换为JSON字符串后再发送。
- **示例**:
  ```javascript
  app.get('/user', (req, res) => {
    res.json({ name: '爱丽丝', age: 25 }); // 自动设置内容类型为application/json
  });
  ```

#### 3. `res.end([data[, encoding]])`
- **功能**: 结束响应过程。当不需要向客户端发送更多数据时使用此方法。
- **示例**:
  ```javascript
  app.get('/stream', (req, res) => {
    res.write('数据的第一部分 ');
    res.end('数据的最后部分'); // 结束响应，可以发送或不发送数据
  });
  ```

#### 4. `res.status(code)`
- **功能**: 设置响应的HTTP状态码。
- **示例**:
  ```javascript
  app.get('/not-found', (req, res) => {
    res.status(404).send('未找到'); // 可与其他res方法链式调用
  });
  ```

#### 5. `res.sendFile(path [, options] [, fn])`
- **功能**: 传输给定路径的文件。根据文件扩展名设置响应的Content-Type HTTP头字段。
- **示例**:
  ```javascript
  app.get('/download', (req, res) => {
    const filePath = __dirname + '/files/document.pdf';
    res.sendFile(filePath); // 自动设置内容处置为附件
  });
  ```

#### 6. `res.render(view [, locals] [, callback])`
- **功能**: 渲染视图并将渲染的HTML字符串发送到客户端。通常与模板引擎一起使用。
- **示例**:
  ```javascript
  app.get('/dashboard', (req, res) => {
    res.render('dashboard', { user: '爱丽丝' }); // 渲染带有用户数据的'dashboard'视图
  });
  ```

#### 7. `res.redirect([status,] path)`
- **功能**: 重定向到由指定路径派生的URL，可选状态码。
- **示例**:
  ```javascript
  app.get('/old-page', (req, res) => {
    res.redirect(301, '/new-page'); // 301永久移动状态码
  });
  ```

#### 8. `res.set(field [, value])`
- **功能**: 将响应的HTTP头 `field` 设置为 `value`。也可以传递一个对象以设置多个字段。
- **示例**:
  ```javascript
  app.get('/with-headers', (req, res) => {
    res.set({
      'Content-Type': 'application/json',
      'Custom-Header': 'HeaderValue'
    });
    res.send({ message: '头部设置' });
  });
  ```

#### 9. `res.cookie(name, value [, options])`
- **功能**: 设置名为 `name` 的cookie的值，附带一个选项对象。
- **示例**:
  ```javascript
  app.get('/set-cookie', (req, res) => {
    res.cookie('session_id', '12345', { maxAge: 900000, httpOnly: true });
    res.send('Cookie已设置');
  });
  ```

#### 10. `res.clearCookie(name [, options])`
- **功能**: 清除指定名字的cookie。
- **示例**:
  ```javascript
  app.get('/clear-cookie', (req, res) => {
    res.clearCookie('session_id');
    res.send('Cookie已清除');
  });
  ```

#### 11. `res.type(type)`
- **功能**: 将Content-Type HTTP头设置为由`type`确定的MIME类型。
- **示例**:
  ```javascript
  app.get('/text', (req, res) => {
    res.type('txt').send('文本响应'); // 设置内容类型为text/plain
  });
  ```

---

# English version: Expanded and Detailed Explanation of Express.js `res` Methods

#### 1. `res.send([body])`
- **Functionality**: Sends a response with various types of data, such as a `Buffer`, `String`, an `Object`, or an `Array`. Express automatically assigns the correct content-type based on the type of the response.
- **Example**:
  ```javascript
  app.get('/', (req, res) => {
    res.send('<h1>Hello World</h1>'); // Sends a text/html response
  });
  ```

#### 2. `res.json([body])`
- **Functionality**: Sends a JSON response. This method is similar to `res.send()` but always converts non-Buffer objects into JSON strings before sending them.
- **Example**:
  ```javascript
  app.get('/user', (req, res) => {
    res.json({ name: 'Alice', age: 25 }); // Automatically sets Content-Type to application/json
  });
  ```

#### 3. `res.end([data[, encoding]])`
- **Functionality**: Ends the response process. This method is used when no more data needs to be sent to the client.
- **Example**:
  ```javascript
  app.get('/stream', (req, res) => {
    res.write('Part 1 of the data ');
    res.end('Final part of the data'); // Ends the response with or without sending data
  });
  ```

#### 4. `res.status(code)`
- **Functionality**: Sets the HTTP status code of the response.
- **Example**:
  ```javascript
  app.get('/not-found', (req, res) => {
    res.status(404).send('404 Not Found'); // Chainable with other res methods
  });
  ```

#### 5. `res.sendFile(path [, options] [, fn])`
- **Functionality**: Transfers the file at the given path. Sets the Content-Type response HTTP header field based on the file’s extension.
- **Example**:
  ```javascript
  app.get('/download', (req, res) => {
    const filePath = __dirname + '/files/document.pdf';
    res.sendFile(filePath); // Automatically sets Content-Disposition for attachment
  });
  ```

#### 6. `res.render(view [, locals] [, callback])`
- **Functionality**: Renders a view and sends the rendered HTML string to the client. Typically used with template engines.
- **Example**:
  ```javascript
  app.get('/dashboard', (req, res) => {
    res.render('dashboard', { user: 'Alice' }); // Renders 'dashboard' view with the user data
  });
  ```

#### 7. `res.redirect([status,] path)`
- **Functionality**: Redirects to the URL derived from the specified path, with optional status code.
- **Example**:
  ```javascript
  app.get('/old-page', (req, res) => {
    res.redirect(301, '/new-page'); // 301 Moved Permanently status code
  });
  ```

#### 8. `res.set(field [, value])`
- **Functionality**: Sets the response’s HTTP header `field` to `value`. Can also pass an object to set multiple fields.
- **Example**:
  ```javascript
  app.get('/with-headers', (req, res) => {
    res.set({
      'Content-Type': 'application/json',
      'Custom-Header': 'HeaderValue'
    });
    res.send({ message: 'Headers set' });
  });
  ```

#### 9. `res.cookie(name, value [, options])`
- **Functionality**: Sets cookie `name` to `value`, with an options object.
- **Example**:
  ```javascript
  app.get('/set-cookie', (req, res) => {
    res.cookie('session_id', '12345', { maxAge: 900000, httpOnly: true });
    res.send('Cookie set');
  });
  ```

#### 10. `res.clearCookie(name [, options])`
- **Functionality**: Clears the cookie specified by `name`.
- **Example**:
  ```javascript
  app.get('/clear-cookie', (req, res) => {
    res.clearCookie('session_id');
    res.send('Cookie cleared');
  });
  ```

#### 11. `res.type(type)`
- **Functionality**: Sets the Content-Type HTTP header to the MIME type as determined by `type`.
- **Example**:
  ```javascript
  app.get('/text', (req, res) => {
    res.type('txt').send('Text response'); // Sets Content-Type to text/plain
  });
  ``
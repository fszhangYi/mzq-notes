### 介绍 `json-server` 🌐

`json-server` 是一个多功能的 JavaScript 库，提供了一个零编码的完整假 REST API。对于需要快速模拟后端的前端开发人员来说，这是理想的选择。使用 `json-server`，您可以在几分钟内搭建一个服务器，来服务自定义的 JSON 数据。

### 开始使用 `json-server` 🚀

#### 安装 📦

使用 npm 全局安装 `json-server`：

```bash
npm install -g json-server
```

#### 创建 JSON 文件 📄

创建一个名为 `db.json` 的文件，并填入样例数据：

```json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

#### 启动服务器 🌍

使用以下命令启动 `json-server`：

```bash
json-server --watch db.json
```

### 使用 Axios 操作服务器 🔄

Axios 是一个流行的 HTTP 客户端，用于发出请求。以下是如何将其与 `json-server` 结合使用。

#### 使用 Axios 获取数据 🔍

从服务器检索数据：

1. 在项目中安装 Axios：

   ```bash
   npm install axios
   ```

2. 使用 Axios 发出 GET 请求：

   ```javascript
   const axios = require('axios');

   axios.get('http://localhost:3000/posts')
        .then(response => {
            console.log(response.data);
        })
        .catch(error => {
            console.error('出现错误！', error);
        });
   ```

#### 使用 Axios 添加数据 ➕

添加新数据：

```javascript
axios.post('http://localhost:3000/posts', {
    title: '新帖子',
    author: 'Jane Doe'
})
.then(response => {
    console.log('创建帖子：', response.data);
})
.catch(error => {
    console.error('出现错误！', error);
});
```

#### 使用 Axios 更新数据 🔄

更新现有数据：

```javascript
axios.put('http://localhost:3000/posts/1', {
    title: '更新后的帖子',
    author: 'John Doe'
})
.then(response => {
    console.log('帖子更新：', response.data);
})
.catch(error => {
    console.error('出现错误！', error);
});
```

#### 使用 Axios 删除数据 ❌

删除数据：

```javascript
axios.delete('http://localhost:3000/posts/1')
.then(response => {
    console.log('帖子已删除');
})
.catch(error => {
    console.error('出现错误！', error);
});
```

### 结论 ✨

`json-server` 结合 Axios 提供了一个强大且灵活的设置，使前端开发人员能够快速模拟后端。这种方法加速了网页应用程序的开发和测试，特别是在早期阶段或实际后端尚未准备好时。


## English version
### Introduction to `json-server` 🌐

`json-server` is a versatile JavaScript library that provides a full fake REST API with no coding. It's ideal for front-end developers who need a quick mock back-end. Using `json-server`, you can set up a server in minutes to serve custom JSON data.

### Getting Started with `json-server` 🚀

#### Installation 📦

Install `json-server` globally using npm:

```bash
npm install -g json-server
```

#### Creating a JSON File 📄

Create a file named `db.json` with sample data:

```json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

#### Starting the Server 🌍

Start `json-server` with:

```bash
json-server --watch db.json
```

### Using the Server with Axios 🔄

Axios is a popular HTTP client for making requests. Here’s how you can use it with `json-server`.

#### Fetching Data with Axios 🔍

To retrieve data from the server:

1. Install Axios in your project:

   ```bash
   npm install axios
   ```

2. Use Axios to make a GET request:

   ```javascript
   const axios = require('axios');

   axios.get('http://localhost:3000/posts')
        .then(response => {
            console.log(response.data);
        })
        .catch(error => {
            console.error('There was an error!', error);
        });
   ```

#### Adding Data with Axios ➕

To add new data:

```javascript
axios.post('http://localhost:3000/posts', {
    title: 'New Post',
    author: 'Jane Doe'
})
.then(response => {
    console.log('Post created:', response.data);
})
.catch(error => {
    console.error('There was an error!', error);
});
```

#### Updating Data with Axios 🔄

To update existing data:

```javascript
axios.put('http://localhost:3000/posts/1', {
    title: 'Updated Post',
    author: 'John Doe'
})
.then(response => {
    console.log('Post updated:', response.data);
})
.catch(error => {
    console.error('There was an error!', error);
});
```

#### Deleting Data with Axios ❌

To delete data:

```javascript
axios.delete('http://localhost:3000/posts/1')
.then(response => {
    console.log('Post deleted');
})
.catch(error => {
    console.error('There was an error!', error);
});
```

### Conclusion ✨

`json-server` combined with Axios offers a powerful and flexible setup for front-end developers to mock a back-end quickly. This approach accelerates the development and testing of web applications, particularly in the early stages or when the actual back-end is not yet ready.
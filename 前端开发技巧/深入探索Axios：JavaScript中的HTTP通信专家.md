<p align=center><img src="https://axios-http.com/assets/logo.svg" alt="Axios Logo"  /></p>

## 🌟 简介

Axios是一个基于Promise的HTTP客户端，专为浏览器和node.js设计。它允许发出各种类型的HTTP请求，并提供丰富的接口处理响应。Axios的易用性、扩展性和丰富的功能，使其成为处理Web请求的首选工具。

## 🚀 核心特点

1. 🌐 **浏览器中创建`XMLHttpRequests`**
2. 💻 **在Node.js中发出HTTP请求**
3. ✅ **完全支持`Promise API`**
4. 🛠 **拦截请求和响应**
5. 🔄 **转换请求和响应数据**
6. 🚫 **支持取消请求**
7. 📄 **自动转换JSON数据**
8. 🛡 **客户端XSRF保护**

## ⚙️ 安装与配置

```bash
npm install axios
```

或

```bash
yarn add axios
```

### 基础配置

```javascript
const axios = require('axios');

// 基础配置实例
const instance = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```

## 📚 使用实例

### 发送GET请求

获取数据是Axios的常见用途。以下示例展示了如何发出GET请求：

```javascript
axios.get('https://api.example.com/data')
  .then(response => console.log(response.data))
  .catch(error => console.error('Error:', error));
```

### 发送POST请求

向服务器发送数据通常通过POST请求完成：

```javascript
axios.post('https://api.example.com/submit', {
  title: 'Axios Tutorial',
  body: 'Axios is easy to use',
  userId: 1
})
.then(response => console.log(response.data))
.catch(error => console.error('Error:', error));
```

## 🌐 使用拦截器

拦截器是Axios的一个强大功能，它允许您在请求或响应被处理之前，注入自定义逻辑。

### 请求拦截器

```javascript
// 添加请求拦截器
axios.interceptors.request.use(config => {
    config.headers['Authorization'] = 'Bearer your-token-here';
    return config;
}, error => {
    return Promise.reject(error);
});
```

### 响应拦截器

```javascript
// 添加响应拦截器
axios.interceptors.response.use(response => {
    if (response.status === 200) {
        console.log('Data received successfully');
    }
    return response;
}, error => {
    return Promise.reject(error);
});
```

## 📈 高级用法

### 并发请求

Axios支持同时发送多个请求：

```javascript
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

// 同时执行
axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread((acct, perms) => {
    // 两个请求都完成时
    console.log('Account', acct.data);
    console.log('Permissions', perms.data);
  }));
```

### 错误处理

良好的错误处理对于创建健壮的应用至关重要。Axios提供了简单的错误处理机制：

```javascript
axios.get('/user/12345')
  .catch(error => {
    if (error.response) {
      // 服务器响应状态码不在2xx范围内
      console.log(error.response.data);
      console.log(error.response.status);
      console.log(error.response.headers);
    } else if (error.request) {
      // 请求已发出，但没有收到响应
      console.log(error.request);
    } else {
      // 发送请求时出了点问题
      console.log('Error’, error.message);
}
});
```

## 🔗 结论

Axios以其简单、灵活且功能丰富的API，在JavaScript开发者中赢得了广泛的好评。它适用于从简单的数据获取到复杂的HTTP请求处理等各种场景。

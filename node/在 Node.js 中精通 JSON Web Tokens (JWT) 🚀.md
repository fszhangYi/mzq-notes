# 在 Node.js 中使用 JSON Web Tokens (JWT) 🚀

## 介绍 JSON Web Tokens (JWT) 📜

JSON Web Tokens (JWT) 是一种用于安全地在各方之间以 JSON 对象传输信息的紧凑且自包含的方式。在 Node.js 中，`jsonwebtoken` 包被广泛用于创建和验证 JWTs。本文将指导你了解 `jsonwebtoken` 的基本和高级用法，配合代码示例。😃

## 基本使用 🛠

### 安装

首先，安装 `jsonwebtoken` 包：

```sh
npm install jsonwebtoken
```

### 创建令牌

要创建 JWT，使用 `sign` 方法。你需要一个密钥和一个对象有效载荷。

```javascript
const jwt = require('jsonwebtoken');

const secretKey = 'your-256-bit-secret';
const payload = { username: 'exampleUser' };

const token = jwt.sign(payload, secretKey);
console.log(token); // JWT 令牌 🎉
```

### 验证令牌

要验证令牌并检索有效载荷，请使用 `verify` 方法。

```javascript
try {
  const decoded = jwt.verify(token, secretKey);
  console.log(decoded); // 解码后的有效载荷 😎
} catch (error) {
  console.error('无效的令牌 😞');
}
```

## 高级使用 🔍

### 设置过期时间

为了提高安全性，为令牌设置过期时间。

```javascript
const tokenWithExpiry = jwt.sign(payload, secretKey, { expiresIn: '1h' });
console.log(tokenWithExpiry); // 1 小时过期的令牌 ⏳
```

### 异步签名和验证

为了更好的性能，特别是在复杂操作或高负载环境中，使用 `sign` 和 `verify` 的异步版本。

#### 异步创建令牌

```javascript
jwt.sign(payload, secretKey, { expiresIn: '2h' }, (err, token) => {
  if (err) {
    console.error('令牌生成中的错误 🚨');
  } else {
    console.log(token); // 异步生成的令牌 🌟
  }
});
```

#### 异步验证令牌

```javascript
jwt.verify(token, secretKey, (err, decoded) => {
  if (err) {
    console.error('无效的令牌 🚫');
  } else {
    console.log(decoded); // 异步解码的有效载荷 👍
  }
});
```

### 处理错误

正确处理验证过程中可能出现的错误，例如令牌过期或签名不匹配。

```javascript
jwt.verify(token, secretKey, (err, decoded) => {
  if (err) {
    switch (err.name) {
      case 'TokenExpiredError':
        console.error('令牌过期 🕒');
        break;
      case 'JsonWebTokenError':
        console.error('无效的令牌 😡');
        break;
      // 处理其他类型的错误
    }
  } else {
    console.log(decoded); // 解码的有效载荷 👌
  }
});
```

## 结论

`jsonwebtoken` 包是 Node.js 中处理 JWTs 的强大工具。无论你是刚开始还是希望实现更多高级功能，理解如何创建和验证令牌对于安全的应用程序开发至关重要。始终确保你的密钥受到保护，并优雅地处理与令牌相关的错误，以构建健壮的应用程序。祝你使用 JWTs 编码愉快！🚀👨‍💻👩‍💻

---

# English version: Mastering JSON Web Tokens in Node.js 🚀

## Introduction to JSON Web Tokens (JWT) 📜

JSON Web Tokens (JWT) is a compact and self-contained way for securely transmitting information between parties as a JSON object. In Node.js, the `jsonwebtoken` package is widely used for creating and verifying JWTs. This article will guide you through the basics and advanced usage of `jsonwebtoken`, complete with code examples. 😃

## Basic Usage 🛠

### Installation

First, install the `jsonwebtoken` package:

```sh
npm install jsonwebtoken
```

### Creating a Token

To create a JWT, use the `sign` method. You'll need a secret key and an object payload.

```javascript
const jwt = require('jsonwebtoken');

const secretKey = 'your-256-bit-secret';
const payload = { username: 'exampleUser' };

const token = jwt.sign(payload, secretKey);
console.log(token); // JWT token 🎉
```

### Verifying a Token

To verify a token and retrieve the payload, use the `verify` method.

```javascript
try {
  const decoded = jwt.verify(token, secretKey);
  console.log(decoded); // Decoded payload 😎
} catch (error) {
  console.error('Invalid token 😞');
}
```

## Advanced Usage 🔍

### Setting Expiration

For enhanced security, set an expiration time for the token.

```javascript
const tokenWithExpiry = jwt.sign(payload, secretKey, { expiresIn: '1h' });
console.log(tokenWithExpiry); // Token with 1-hour expiry ⏳
```

### Asynchronous Sign and Verify

For better performance, especially with complex operations or in high-load environments, use the asynchronous versions of `sign` and `verify`.

#### Asynchronous Token Creation

```javascript
jwt.sign(payload, secretKey, { expiresIn: '2h' }, (err, token) => {
  if (err) {
    console.error('Error in token generation 🚨');
  } else {
    console.log(token); // Asynchronously generated token 🌟
  }
});
```

#### Asynchronous Token Verification

```javascript
jwt.verify(token, secretKey, (err, decoded) => {
  if (err) {
    console.error('Invalid token 🚫');
  } else {
    console.log(decoded); // Asynchronously decoded payload 👍
  }
});
```

### Handling Errors

Properly handle errors that might occur during verification, such as token expiration or signature mismatch.

```javascript
jwt.verify(token, secretKey, (err, decoded) => {
  if (err) {
    switch (err.name) {
      case 'TokenExpiredError':
        console.error('Token expired 🕒');
        break;
      case 'JsonWebTokenError':
        console.error('Invalid token 😡');
        break;
      // handle other types of errors
    }
  } else {
    console.log(decoded); // Decoded payload 👌
  }
});
```

## Conclusion

The `jsonwebtoken` package is a powerful tool for handling JWTs in Node.js. Whether you're just starting or looking to implement more advanced features, understanding how to create and verify tokens is crucial for secure application development. Always ensure your secret key is protected and handle token-related errors gracefully for robust applications. Happy coding with JWTs! 🚀👨‍💻👩‍💻
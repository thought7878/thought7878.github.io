跨域资源共享（Cross-Origin Resource Sharing，简称 CORS）是一种安全机制，**用于解决浏览器同源策略（Same-origin policy）带来的限制**。CORS 允许一个域上的网页请求另一个域上的资源，从而实现了跨域的数据交换。

### 同源策略

同源策略是浏览器的一种安全措施，它规定只有来自相同协议（http 或 https）、相同域名和相同端口号的请求才能访问到页面的 DOM 数据。具体来说，同源策略限制了一个网站只能请求和访问同一来源的资源。这是为了防止恶意站点访问或篡改其他站点的数据。

### CORS 的作用

CORS 通过*在 HTTP 响应头中设置特定的字段来放宽同源策略的限制，允许服务端指定哪些来源可以访问其资源*。这样，即使请求来自不同的域，只要服务端明确允许，浏览器就会允许这些请求。

### CORS 的工作原理

CORS 的实现涉及两个主要部分：浏览器发出的请求和服务器返回的响应。

#### 1. 简单请求（Simple Request）

简单请求是指满足以下条件的请求：

- 方法是 `GET`、`HEAD` 或 `POST`。
- 对于 `POST` 请求，请求头中的 `Content-Type` 必须是以下之一：
  - `application/x-www-form-urlencoded`
  - `multipart/form-data`
  - `text/plain`

对于简单请求，浏览器会直接发送请求，不会预先发送 OPTIONS 请求。如果服务端响应头中包含特定的 CORS 字段，浏览器将允许请求。

#### 2. 预检请求（Preflight Request）

预检请求是指不符合简单请求条件的请求，比如使用了 `PUT`、`DELETE` 等 HTTP 方法，或者请求头中有 `Content-Type` 以外的字段（如 `Authorization`）。

在这种情况下，浏览器会先发送一个 OPTIONS 请求（预检请求）来询问服务器是否允许实际请求。如果服务器允许，才会发送实际的请求。

##### OPTIONS 请求

预检请求是一个 OPTIONS 请求，包含以下头部信息：

- `Access-Control-Request-Method`：表示实际请求的方法。
- `Access-Control-Request-Headers`：表示实际请求的头部。

##### OPTIONS 响应

*服务器响应中包含以下头部信息：*

- `Access-Control-Allow-Origin`：指定允许访问的源，可以是特定的域名或通配符 `*`。
- `Access-Control-Allow-Methods`：指定允许的 HTTP 方法。
- `Access-Control-Allow-Headers`：指定允许的头部。
- `Access-Control-Max-Age`：指定预检请求的有效期（单位：秒），在此期间浏览器可以缓存预检请求的结果。

### CORS 响应头

以下是 CORS 响应头的一些重要字段：

- **Access-Control-Allow-Origin**: 指定允许访问的源，可以是特定的域名或通配符 `*`。
- **Access-Control-Allow-Credentials**: 指定是否允许携带凭据（如 cookie、HTTP 认证信息等）。
- **Access-Control-Allow-Methods**: 指定允许的 HTTP 方法。
- **Access-Control-Allow-Headers**: 指定允许的头部。
- **Access-Control-Expose-Headers**: 指定允许暴露给前端的响应头。
- **Access-Control-Max-Age**: 指定预检请求的有效期。

### 示例配置

#### 服务器端配置示例

以下是一个简单的 Node.js 服务器配置示例，使用 Express 框架：

```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
  // 设置允许访问的源
  res.header('Access-Control-Allow-Origin', '*');
  // 设置允许的 HTTP 方法
  res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');
  // 设置允许的头部
  res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept, Authorization');
  // 如果是预检请求，则直接返回成功响应
  if (req.method === 'OPTIONS') {
    res.sendStatus(200);
  } else {
    next();
  }
});

app.get('/api/data', (req, res) => {
  res.json({ message: 'Hello from the server!' });
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

#### 客户端请求示例

以下是一个简单的 JavaScript 请求示例：

```javascript
fetch('https://api.example.com/data')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### 总结

跨域资源共享（CORS）是一种机制，允许服务端指定哪些来源可以访问其资源，从而绕过浏览器的同源策略限制。通过在响应头中设置特定的 CORS 字段，服务端可以控制哪些跨域请求是允许的。CORS 包括简单请求和预检请求两种情况，通过适当配置服务器端的响应头，可以实现安全的跨域数据交换。

## 参考资料
[掌握CORS跨域请求](https://www.bilibili.com/video/BV13F411y7fy/?spm_id_from=333.337.search-card.all.click&vd_source=22af953ea4c09540ad1966711a2d53f0)

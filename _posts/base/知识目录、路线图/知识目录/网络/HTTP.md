学习 HTTP（HyperText Transfer Protocol）是理解 Web 开发和网络通信的基础。以下是 HTTP 的核心知识点总结，涵盖了从基础到高级的内容，帮助你系统地学习。

---
## 前置知识
- [[互联网是如何运作的？]]
- IP。参考：[[IP]]
- TCP。参考：[[TCP]]
- UDP。参考：[[UDP]]

## 一、HTTP 基础

1. **什么是 HTTP？**

   - HTTP 是一种应用层协议，用于*在客户端（如浏览器）和服务器之间传输超文本（HTML、图片等资源）*。
   - 它基于请求-响应模型：客户端发送请求，服务器返回响应。

2. **HTTP 的特点**

   - **无状态**：每次请求独立，服务器不会自动保存之前的请求信息。
   - **基于 TCP/IP**：HTTP *依赖于底层的 TCP 协议*来*保证数据的可靠传输*。
   - **明文传输**：默认情况下，HTTP 数据是*未加密的*（HTTPS 使用 TLS 加密）。

3. **URL 的组成**

- URL（Uniform Resource Locator）是*资源的地址*，格式如下：

```
scheme://host:port/path?query#fragment
```

- `scheme`：协议（如 `http` 或 `https`）。
- `host`：服务器域名或 IP 地址。
- `port`：端口号（默认 HTTP 为 80，HTTPS 为 443）。
- `path`：资源路径。
- `query`：查询参数（如 `?name=John&age=30`）。
- `fragment`：页面内的锚点（如 `#section1`）。

---

## 二、HTTP 请求与响应

### 请求

#### 请求方法（Methods）
参考：[[请求方法（HTTP Methods）]]

  - `GET`：获取资源。
  - `POST`：提交数据。
  - `PUT`：更新资源。
  - `DELETE`：删除资源。
  - 其他：`HEAD`、`OPTIONS`、`PATCH` 等。

#### 请求结构
- **请求行**：包含方法、URL 和 HTTP 版本。

```http
GET /index.html HTTP/1.1
```

- **请求头**：包含元信息（如 `Host`、`User-Agent`、`Accept` 等）。参考：[[请求头]]

```http
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

- **请求体**（可选）：用于传递数据（如表单数据、JSON）。参考：[[请求体]]

### 响应

#### 状态码（Status Codes）
参考：[[状态码（Status Codes）]]
- **1xx**：信息性状态（如 `100 Continue`）。
- **2xx**：成功（如 `200 OK`、`201 Created`）。
- **3xx**：重定向（如 `301 Moved Permanently`、`304 Not Modified`）。
- **4xx**：客户端错误（如 `400 Bad Request`、`404 Not Found`）。
- **5xx**：服务器错误（如 `500 Internal Server Error`）。

#### 响应结构
- **状态行**：包含 HTTP 版本、状态码和状态消息。

```http
HTTP/1.1 200 OK
```

- **响应头**：包含元信息（如 `Content-Type`、`Cache-Control` 等）。参考：[[响应头]]

```http
Content-Type: text/html
Cache-Control: max-age=3600
```

- **响应体**：实际返回的数据（如 HTML 文档、JSON 数据）。参考：[[响应体]]

---

## 三、HTTP 版本
参考：[[HTTP 1.0]]、[[HTTP 1.1]]、[[HTTP 2]]、[[HTTP 3]]

### HTTP/1.0、HTTP/1.1、HTTP/2、HTTP/3

1. **HTTP/1.0**

   - 每次请求都需要建立新的 TCP 连接。
   - 不支持持久连接。

2. **HTTP/1.1**

   - 支持持久连接（Keep-Alive），复用同一个 TCP 连接处理多个请求。
   - 支持管道化（Pipelining），允许客户端连续发送多个请求。
   - 新增了缓存控制机制（如 `Cache-Control`）。

3. **HTTP/2**

   - 引入了多路复用（Multiplexing），允许多个请求和响应同时在一个连接上传输。
   - 使用二进制帧（Binary Framing）提高传输效率。
   - 支持头部压缩（HPACK），减少头部大小。

4. **HTTP/3**
   - 基于 QUIC 协议（UDP），解决了 TCP 的队头阻塞问题。
   - 提供更快的连接建立和更高的可靠性。

### HTTP/2 和 HTTP/3 的对比

| 特性     | HTTP/2              | HTTP/3          |
| ------ | ------------------- | --------------- |
| 传输协议   | TCP                 | QUIC（基于 UDP）    |
| 队头阻塞问题 | 存在（底层 TCP 的限制）      | 不存在（QUIC 的多流设计） |
| 连接建立时间 | 较长（TCP 握手 + TLS 握手） | 较短（0-RTT 握手）    |
| 丢包恢复能力 | 较弱（依赖 TCP 的重传机制）    | 较强（QUIC 的内置机制）  |



[Top 8 Most Popular Network Protocols Explained](https://www.youtube.com/watch?v=P6SZLcGE4us "Top 8 Most Popular Network Protocols Explained")
[HTTP Status Codes Explained In 5 Minutes](https://www.youtube.com/watch?v=qmpUfWN7hh4 "HTTP Status Codes Explained In 5 Minutes")

---

## 四、HTTPS
参考：[[HTTPS]]、[SSL, TLS, HTTPS Explained](https://www.youtube.com/watch?v=j9QmMEWmcfo "SSL, TLS, HTTPS Explained")、[[36.HTTPS：让数据传输更安全]]

1. **什么是 HTTPS？**

   - HTTPS 是 HTTP 的安全版本，通过 TLS（Transport Layer Security）加密通信内容。
   - 默认使用端口 443。

2. **TLS 握手过程**

   - **客户端问候**：客户端*发送支持的加密算法列表*。
   - **服务器问候**：服务器*选择加密算法并返回证书*。
   - **密钥交换**：双方协商生成对称加密密钥。
   - **完成握手**：双方确认握手完成，开始加密通信。

3. **HTTPS 的优点**
   - 数据加密：防止窃听和篡改。
   - 身份验证：通过证书验证服务器身份。
   - 数据完整性：确保数据在传输过程中未被修改。

---

## 五、HTTP 缓存
[What Is A CDN? How Does It Work?](https://www.youtube.com/watch?v=RI9np1LWzqw "What Is A CDN? How Does It Work?")
参考：[[缓存]]、[[CDN]]

1. **缓存的作用**

   - 减少重复请求，提升性能。
   - 减轻服务器负载。

2. **缓存控制机制**

   - **强缓存**：
     - `Expires`：指定资源的过期时间。
     - `Cache-Control`：更灵活的缓存控制（如 `max-age=3600`）。
   - **协商缓存**：
     - `ETag`：唯一标识资源的*哈希值*。
     - `Last-Modified`：资源的*最后修改时间*。

3. **缓存策略**
   - 浏览器缓存、代理缓存、CDN 缓存。

---

## 六、HTTP 与 RESTful API

1. **RESTful API 的特点**

   - 基于 HTTP 协议。
   - 使用标准的 HTTP 方法（如 `GET`、`POST`、`PUT`、`DELETE`）。
   - 无状态设计。

2. **常见数据格式**
   - JSON（JavaScript Object Notation）。
   - XML（eXtensible Markup Language）。

---

## 七、HTTP 性能优化

1. **减少请求数量**

   - 合并文件、使用雪碧图（Sprite）。
   - 使用 HTTP/2 多路复用。

2. **启用缓存**

   - 使用 `Cache-Control` 和 `ETag`。
   - 配置 CDN 缓存。

3. **压缩资源**

   - 使用 Gzip 或 Brotli 压缩 HTML、CSS、JavaScript 和图片。

4. **延迟加载**

   - 对非关键资源（如图片、视频）使用懒加载。

5. **DNS 预解析**
   - 使用 `<link rel="dns-prefetch">` 提前解析域名。

---

## 八、HTTP 工具与调试

1. **常用工具**

   - **Postman**：测试 API 请求。
   - **cURL**：命令行工具，用于发送 HTTP 请求。
   - **浏览器开发者工具**：分析网络请求和响应。

2. **抓包工具**
   - **Wireshark**：捕获和分析网络流量。
   - **Fiddler**：调试 HTTP/HTTPS 请求。

---

## 九、扩展知识

1. **WebSocket**

   - 一种全双工通信协议，适用于实时应用（如聊天、游戏）。
   - 基于 HTTP 升级协议。

2. **CORS（跨域资源共享）**

   - 允许浏览器向不同域的服务器发起请求。
   - 通过 `Access-Control-Allow-Origin` 控制跨域访问。

3. **HSTS（HTTP Strict Transport Security）**
   - 强制浏览器使用 HTTPS 访问网站。

---

## 十、学习建议

1. **实践为主**

   - 动手搭建简单的 HTTP 服务器（如使用 Node.js 或 Python）。
   - 使用 Postman 或 cURL 测试 API。

2. **阅读规范**

   - 学习 [RFC 7230](https://tools.ietf.org/html/rfc7230) 等官方文档，深入了解 HTTP 协议。

3. **关注最新技术**
   - 学习 HTTP/3 和 QUIC 的原理。
   - 关注 Web 性能优化的最新趋势。

---

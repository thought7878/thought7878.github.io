**HTTP/1.0** 是 HTTP 协议的*第一个广泛使用的*版本，于 *1996 年*正式发布（RFC 1945）。它为现代 Web 的发展*奠定了基础*，但由于其设计上的局限性，逐渐被 HTTP/1.1 和更高版本所取代。

---

## **一、HTTP/1.0 的特点**

1. **无状态协议**：
   - 每次请求和响应都是独立的，服务器不会自动保存之前的请求信息。
2. **短连接**：
   - 默认情况下，_每次请求都需要建立新的 TCP 连接，请求完成后立即关闭连接_。
3. **简单易用**：
   - 设计简单，易于实现，适合早期的 Web 应用。
4. **不支持持久连接**：
   - 每次请求都需要重新建立连接，增加了网络开销。

---

## **二、HTTP/1.0 的工作流程**

1. **客户端发起请求**：
   - 客户端向服务器发送一个 HTTP 请求，包含请求行、请求头和可选的请求体。
   - 示例：
     ```
     GET /index.html HTTP/1.0
     Host: www.example.com
     ```
2. **服务器返回响应**：

   - 服务器处理请求后，返回一个 HTTP 响应，包含状态行、响应头和响应体。
   - 示例：

     ```
     HTTP/1.0 200 OK
     Content-Type: text/html
     Content-Length: 1234

     <html>
       <body>
         <h1>Hello, World!</h1>
       </body>
     </html>
     ```

3. **关闭连接**：
   - *响应完成后，TCP 连接立即关闭*。

---

## **三、HTTP/1.0 的核心特性**

### 1. **请求方法**

- 支持基本的 HTTP 方法：
  - `GET`：获取资源。
  - `POST`：提交数据。
  - `HEAD`：获取响应头，但不返回响应体。
  - `PUT` 和 `DELETE` 等方法在 HTTP/1.0 中未被广泛支持。

### 2. **状态码**

- 引入了标准的状态码，用于表示请求的处理结果：
  - **2xx 成功**：如 `200 OK`。
  - **3xx 重定向**：如 `301 Moved Permanently`。
  - **4xx 客户端错误**：如 `404 Not Found`。
  - **5xx 服务器错误**：如 `500 Internal Server Error`。

### 3. **头部字段**

- 支持基本的请求头和响应头：
  - **请求头**：如 `Host`、`User-Agent`、`Accept`。
  - **响应头**：如 `Content-Type`、`Content-Length`。

### 4. **内容类型**

- 通过 `Content-Type` 头字段指定响应的内容类型（如 `text/html`、`application/json`）。

---

## 四、HTTP/1.0 的局限性

### 1. 短连接问题

- **每次请求都需要建立新的 TCP 连接，增加了延迟和资源消耗**。
- 解决方案：HTTP/1.1 引入了*持久连接（Keep-Alive）*。

![[_posts/网络/HTTP/media/26c8625b1e51a215b5ee889899d7a89d_MD5.jpeg]]

### 2. 缺少 Host 头

- HTTP/1.0 不强制要求使用 `Host` 头字段，导致虚拟主机（共享 IP 地址的多个域名）无法区分。
- 解决方案：HTTP/1.1 要求必须包含 `Host` 头字段。

### 3. 缓存机制不足

- 缓存控制功能有限，缺乏对缓存失效和协商缓存的支持。
- 解决方案：HTTP/1.1 引入了更强大的缓存控制机制（如 `Cache-Control` 和 `ETag`）。

### 4. 性能瓶颈

- 由于短连接和缺乏优化机制，HTTP/1.0 在高并发场景下性能较差。

---

## 五、HTTP/1.0 的改进方向

HTTP/1.1 对 HTTP/1.0 进行了多项改进，解决了其主要局限性：

1. **持久连接**：
   - 支持 Keep-Alive，复用同一个 TCP 连接处理多个请求。
2. **管道化（Pipelining）**：
   - 允许客户端连续发送多个请求，而无需等待每个响应。
3. **增强的缓存机制**：
   - 引入了 `Cache-Control`、`ETag` 和 `If-None-Match` 等缓存控制机制。
4. **强制 Host 头**：
   - 要求所有请求必须包含 `Host` 头字段，支持虚拟主机。
5. **分块传输编码**：
   - 支持动态生成的内容通过分块方式传输。

---

## **六、HTTP/1.0 的应用场景**

尽管 HTTP/1.0 已被 HTTP/1.1 和更高版本取代，但在某些简单的场景中仍然可以看到它的身影：

1. **嵌入式设备**：
   - 如物联网设备或老旧系统，可能仍使用 HTTP/1.0。
2. **静态文件服务**：
   - 简单的静态文件服务器可能仅支持 HTTP/1.0。
3. **调试和学习**：
   - HTTP/1.0 的简单性使其成为学习 HTTP 协议的良好起点。

---

## **七、示例：完整的 HTTP/1.0 请求与响应**

### 请求：

```
GET /index.html HTTP/1.0
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
```

### 响应：

```
HTTP/1.0 200 OK
Content-Type: text/html
Content-Length: 1234

<html>
  <body>
    <h1>Hello, World!</h1>
  </body>
</html>
```

---

## **八、总结**

HTTP/1.0 是 HTTP 协议发展的重要里程碑，虽然存在一些局限性，但它为现代 Web 的发展奠定了基础。随着 HTTP/1.1 和更高版本的普及，HTTP/1.0 逐渐退出主流应用，但在某些简单场景中仍然具有一定的价值。

## 相关资料
- [HTTP/1 to HTTP/2 to HTTP/3](https://youtu.be/a-sBfyiXysI?si=lvH1QCawKRc-c5vo)
- [HTTP 1 Vs HTTP 2 Vs HTTP 3!](https://youtu.be/UMwQjFzTQXw?si=_nrvPzQ5QPgpQl58)
- 
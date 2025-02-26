**HTTP/1.1** 是 HTTP 协议的一个重要版本，于 *1997 年*正式发布（RFC 2068），并在后续的 RFC 2616 和 RFC 7230-7237 中进行了更新和完善。**它是 HTTP/1.0 的改进版**，解决了许多性能和功能上的局限性，成为现代 Web 的基础协议之一。

---

## 一、HTTP/1.1 的特点

1. **持久连接（Keep-Alive）**：
   - 默认情况下，`TCP 连接`在请求完成后不会立即关闭，而是保持打开状态，复用同一个连接处理多个请求。
   - 减少了建立和关闭连接的开销，提高了性能。
2. **管道化（Pipelining）**：
   - 允许客户端连续发送多个请求，而无需等待每个响应返回。
   - 提高了请求的并发能力。
3. **增强的缓存机制**：
   - 引入了更强大的缓存控制机制（如 `Cache-Control` 和 `ETag`）。
4. **强制 Host 头字段**：
   - 要求所有请求必须包含 `Host` 头字段，支持虚拟主机（共享 IP 地址的多个域名）。
5. **分块传输编码**：
   - 支持动态生成的内容通过分块方式传输，而无需提前知道内容的总长度。
6. **更好的错误处理**：
   - 引入了更多的状态码和错误处理机制。

---

## 二、HTTP/1.1 的核心特性

### 1. 持久连接（Keep-Alive）***

- **定义**：
  - 在 HTTP/1.0 中，默认每次请求都需要建立新的 TCP 连接，*而 HTTP/1.1 **默认**启用持久连接*。
- **优点**：
  - *减少了建立和关闭连接的开销*。
  - 提高了页面加载速度，*尤其是在需要加载多个资源（如图片、CSS、JavaScript）时*。
- **配置**：
  - 使用 `Connection: keep-alive` 显式启用持久连接。
  - 使用 `Connection: close` 关闭连接。

![[_posts/网络/media/dcdab1eb01d253416a681599991718c8_MD5.jpeg]]

---

### 2. 管道化（Pipelining）*

- **定义**：
  - 客户端可以*在一个持久连接上连续发送多个请求*，而无需等待每个响应返回，*响应必须按照请求的顺序接收*。
- **优点**：
  - 提高了请求的*并发能力*，减少了延迟。
- **缺点**：
  - 如果某个请求的响应被阻塞，后续请求也会被阻塞（*队头阻塞问题*）。
  - 实际应用中，*管道化并未被广泛采用，其支持最终从许多浏览器中移除*。

![[_posts/网络/media/08ab21c04d64e3c8a3610a3429915b44_MD5.jpeg]]

---

### 3. 增强的缓存机制

- **Cache-Control**：
  - 提供了更灵活的缓存控制策略，如 `no-cache`、`no-store`、`max-age`。
- **ETag 和 If-None-Match**：
  - 用于协商缓存，服务器通过 ETag 标识资源的版本，客户端通过 `If-None-Match` 请求验证资源是否已修改。
- **Last-Modified 和 If-Modified-Since**：
  - *基于时间戳*的缓存验证机制。

`条件请求（conditional request）`：使用类似 If-Modified-Since 的头部，让客户端仅在资源已更改时请求资源

![[_posts/网络/HTTP/media/afe4737ca1857b629be9488d8c745ec9_MD5.jpeg]]

![[_posts/网络/HTTP/media/0c1eff056c7f615b4cf53f1745a677e8_MD5.jpeg]]

![[_posts/网络/HTTP/media/238383db376d198cea380b19e5bc5fa5_MD5.jpeg]]


---

### 4. 强制 Host 头字段

- **背景**：
  - 在 HTTP/1.0 中，`Host` 头字段是可选的，导致虚拟主机无法区分。
- **改进**：
  - HTTP/1.1 要求所有请求必须包含 `Host` 头字段，支持在同一台服务器上托管多个域名。
  - 示例：
    ```
    GET /index.html HTTP/1.1
    Host: www.example.com
    ```

---

### 5. 分块传输编码（Chunked Transfer Encoding）

- **定义**：
  - 允许服务器*将响应体分成多个小块传输*，而无需提前知道内容的总长度。
- **应用场景**：
  - 动态生成的内容（如*流媒体、实时数据*）。
- **示例**：

  ```
  HTTP/1.1 200 OK
  Content-Type: text/html
  Transfer-Encoding: chunked

  7\r\n
  <html>\r\n
  9\r\n
  <body>\r\n
  11\r\n
  <h1>Hello, World!</h1>\r\n
  0\r\n
  \r\n
  ```

![[_posts/网络/HTTP/media/061a22caf07f935af1f4afd4ad3dbdf4_MD5.jpeg]]

---

### 6. 新增的状态码

- HTTP/1.1 引入了更多状态码，用于更精确地描述请求的处理结果：
  - **100 Continue**：客户端应继续发送请求。
  - **206 Partial Content**：返回资源的部分内容。
  - **303 See Other**：指示客户端通过 `GET` 方法访问另一个 URI。
  - **408 Request Timeout**：请求超时。
  - **416 Range Not Satisfiable**：请求的范围无效。
  - **505 HTTP Version Not Supported**：不支持的 HTTP 版本。

---

## 三、HTTP/1.1 的工作流程

1. **客户端发起请求**：
   - 客户端向服务器发送一个 HTTP 请求，包含请求行、请求头和可选的请求体。
   - 示例：
     ```
     GET /index.html HTTP/1.1
     Host: www.example.com
     Connection: keep-alive
     ```
2. **服务器返回响应**：

   - 服务器处理请求后，返回一个 HTTP 响应，包含状态行、响应头和响应体。
   - 示例：

     ```
     HTTP/1.1 200 OK
     Content-Type: text/html
     Content-Length: 1234
     Connection: keep-alive

     <html>
       <body>
         <h1>Hello, World!</h1>
       </body>
     </html>
     ```

3. **复用连接或关闭连接**：
   - 如果启用了持久连接，连接会保持打开状态以处理后续请求。
   - 如果显式指定 `Connection: close`，则关闭连接。

---

## 四、HTTP/1.1 的局限性

### 1. 队头阻塞（Head-of-Line Blocking）

- **问题**：
  - 即使使用持久连接，HTTP/1.1 仍然*要求按顺序处理请求和响应*。
  - *如果某个请求的响应被阻塞*，后续请求也无法被处理。
- **解决方案**：
  - HTTP/2 引入了*多路复用*（Multiplexing），解决了队头阻塞问题。

![[_posts/网络/media/f0b4b89e8cfb77c0bb86580a6b958c35_MD5.jpeg]]

### 2. 性能瓶颈

- 尽管 HTTP/1.1 提供了持久连接和管道化，但**在高并发场景下仍然存在性能瓶颈**。

---

## 五、HTTP/1.1 的改进方向

HTTP/2 对 HTTP/1.1 进行了多项改进，解决了其主要局限性：

1. **多路复用**：
   - 允许多个请求和响应同时在一个连接上传输。
2. **头部压缩**：
   - 使用 HPACK 算法压缩头部信息，减少传输开销。
3. **服务器推送**：
   - 服务器可以主动向客户端推送资源，减少请求次数。
4. **二进制帧**：
   - 使用二进制格式传输数据，提高解析效率。

---

## **六、示例：完整的 HTTP/1.1 请求与响应**

### 请求：

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
Connection: keep-alive
```

### 响应：

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234
Connection: keep-alive

<html>
  <body>
    <h1>Hello, World!</h1>
  </body>
</html>
```

---

## **七、总结**

HTTP/1.1 是 HTTP 协议发展的重要里程碑，通过引入持久连接、管道化、增强的缓存机制等功能，显著提高了 Web 性能和用户体验。尽管它存在一些局限性（如队头阻塞问题），但仍然是目前广泛使用的协议之一。

## 相关资料
- [HTTP 1 Vs HTTP 2 Vs HTTP 3!](https://youtu.be/UMwQjFzTQXw?si=_nrvPzQ5QPgpQl58)
- [[29.HTTP1：HTTP性能优化]]
- [HTTP/1 to HTTP/2 to HTTP/3](https://youtu.be/a-sBfyiXysI?si=lvH1QCawKRc-c5vo)

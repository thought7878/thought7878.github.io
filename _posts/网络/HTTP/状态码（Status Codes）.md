HTTP 状态码（Status Codes）是服务器在响应客户端请求时*返回的三位数字代码*，用于表示*请求的处理结果*。状态码分为五类，每类都有特定的含义。

---

## 一、状态码分类
HTTP 状态码*按首位数字*分为五类：
1. **1xx：信息性状态**
   - 表示请求已被接收，正在处理。
2. **2xx：成功**
   - 表示请求已成功处理。
3. **3xx：重定向**
   - 表示*需要进一步操作*才能完成请求。
4. **4xx：客户端错误**
   - 表示客户端请求有误。
5. **5xx：服务器错误**
   - 表示服务器在处理请求时出错。

---

## 二、常见状态码详解

### 1. 1xx：信息性状态
- **100 Continue**：
  - 客户端应继续发送请求。
  - 常用于*分块上传*或*大文件传输*。
- **101 Switching Protocols**：
  - 服务器同意切换协议（如从 HTTP 切换到 WebSocket）。
- **102 Processing（WebDAV）**：
  - 请求正在处理中，但尚未完成。

---

### 2. 2xx：成功
- **200 OK**：
  - 请求成功，返回响应数据。
  - 最常见的状态码。
- **201 Created**：
  - 资源已成功创建（通常*用于 `POST` 请求*）。
- **202 Accepted**：
  - 请求已被接受，但*尚未处理完成*。
- **204 No Content**：
  - 请求成功，但*响应体为空*。
  - 常用于 `DELETE` 或 `PUT` 请求。
- **206 Partial Content**：
  - 返回资源的部分内容（用于范围请求）。

---

### 3. 3xx：重定向
- **301 Moved Permanently**：
  - *资源已永久移动到新地址*。
  - 浏览器会*自动跳转*到新地址。
- **302 Found**：
  - 资源*临时*移动到新地址。
  - 浏览器会*自动跳转*到新地址。
- **303 See Other**：
  - 指示客户端通过 `GET` 方法访问另一个 URI。
- **304 Not Modified**：
  - *资源未修改，客户端可以使用缓存*。
- **307 Temporary Redirect**：
  - 类似于 `302`，但明确要求客户端保持原请求方法。
- **308 Permanent Redirect**：
  - 类似于 `301`，但明确要求客户端保持原请求方法。

---

### 4. 4xx：客户端错误
- **400 Bad Request**：
  - 请求*语法错误*或*参数无效*。
- **401 Unauthorized**：
  - 需要*身份验证*。
- **403 Forbidden**：
  - *服务器拒绝访问（权限不足）*。
- **404 Not Found**：
  - *请求的资源不存在*。
- **405 Method Not Allowed**：
  - 请求方法不被允许（如对静态资源使用 `POST`）。
- **408 Request Timeout**：
  - 请求超时。
- **409 Conflict**：
  - 请求与当前资源状态冲突（如重复创建资源）。
- **410 Gone**：
  - 资源已永久删除。
- **413 Payload Too Large**：
  - 请求体过大。
- **414 URI Too Long**：
  - URL 过长。
- **415 Unsupported Media Type**：
  - 请求的媒体类型不被支持。
- **429 Too Many Requests**：
  - 客户端请求过于频繁。

---

### 5. 5xx：服务器错误
- **500 Internal Server Error**：
  - *服务器内部错误*。
- **501 Not Implemented**：
  - 服务器*不支持该功能*。
- **502 Bad Gateway**：
  - 代理服务器从上游服务器收到无效响应。
- **503 Service Unavailable**：
  - 服务器暂时不可用（如*过载*或*维护*）。
- **504 Gateway Timeout**：
  - 代理服务器未能及时从上游服务器收到响应。
- **505 HTTP Version Not Supported**：
  - 不支持的 HTTP 版本。

---

## 三、状态码的选择原则
1. **语义明确**：
   - 根据请求的处理结果选择合适的状态码。
   - 避免滥用通用状态码（如 `200` 或 `500`）。
2. **遵循标准**：
   - 使用标准化的状态码，避免自定义状态码。
3. **结合业务逻辑**：
   - 在 RESTful API 中，状态码应与操作结果一致。

---

## 四、RESTful API 中的状态码
在 RESTful API 中，状态码通常与 CRUD 操作对应：
- **200 OK**：读取资源成功。
- **201 Created**：创建资源成功。
- **204 No Content**：更新或删除资源成功。
- **400 Bad Request**：客户端请求无效。
- **401 Unauthorized**：未授权访问。
- **403 Forbidden**：无权限访问。
- **404 Not Found**：资源不存在。
- **500 Internal Server Error**：服务器内部错误。

---

## 五、调试工具
1. **浏览器开发者工具**：
   - 查看网络请求的响应状态码。
2. **Postman**：
   - 测试 API 并查看返回的状态码。
3. **cURL**：
   - 使用命令行工具发送请求并检查状态码。
   ```bash
   curl -I http://www.example.com
   ```

---

## 六、注意事项
1. **状态码的意义**：
   - 状态码应准确反映请求的处理结果。
   - 避免误导客户端（如返回 `200` 却包含错误信息）。
2. **错误信息**：
   - 在返回错误状态码时，提供详细的错误描述（如 JSON 格式的错误消息）。
3. **缓存控制**：
   - 对于可缓存的状态码（如 `200`），设置适当的缓存头（如 `Cache-Control`）。

---

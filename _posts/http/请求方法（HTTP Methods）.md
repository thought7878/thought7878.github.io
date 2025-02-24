HTTP 请求方法（HTTP Methods）是 HTTP 协议中定义的*操作类型*，用于指定*客户端希望服务器执行的动作*。每种请求方法都*有特定的用途和语义*。

---

## 一、常见的 HTTP 请求方法

### 1. **GET**

- **用途**：
  - *获取*资源。
  - 常用于从服务器*读取数据*（如 HTML 页面、JSON 数据等）。
- **特点**：
  - *请求体为空*。
  - 参数通过 URL 查询字符串传递（如 `?name=John&age=30`）。
  - 是*幂等的*（多次请求不会改变服务器状态）。
  - 可被缓存。
- **示例**：
  ```
  GET /users?id=123 HTTP/1.1
  Host: www.example.com
  ```

---

### 2. **POST**

- **用途**：
  - *提交*数据到服务器。
  - 常用于*创建新资源*或*提交表单数据*。
- **特点**：
  - *请求体包含数据*。
  - 参数通常以 JSON、表单数据或文件的形式传递。
  - 不是幂等的（多次请求可能会创建多个资源）。
  - 不会被缓存。
- **示例**：

  ```
  POST /api/users HTTP/1.1
  Host: www.example.com
  Content-Type: application/json
  Content-Length: 36

  {
    "name": "John",
    "age": 30
  }
  ```

---

### 3. **PUT**

- **用途**：
  - *更新*资源。
  - 如果资源不存在，则创建资源。
- **特点**：
  - 请求体包含完整的资源数据。
  - 是幂等的（多次请求效果相同）。
  - 通常需要指定资源的唯一标识（如 ID）。
- **示例**：

  ```
  PUT /api/users/123 HTTP/1.1
  Host: www.example.com
  Content-Type: application/json
  Content-Length: 36

  {
    "name": "John",
    "age": 31
  }
  ```

---

### 4. **DELETE**

- **用途**：
  - *删除*资源。
- **特点**：
  - 请求体通常为空。
  - 是幂等的（多次请求效果相同）。
  - 需要指定资源的唯一标识（如 ID）。
- **示例**：
  ```
  DELETE /api/users/123 HTTP/1.1
  Host: www.example.com
  ```

---

### 5. **HEAD**

- **用途**：
  - *获取资源的元信息（如响应头），而不返回实际内容*。
- **特点**：
  - 类似于 `GET`，但响应体为空。
  - 常用于*检查资源是否存在*或*获取资源的大小、修改时间等信息*。
- **示例**：
  ```
  HEAD /index.html HTTP/1.1
  Host: www.example.com
  ```

---

### 6. **OPTIONS**

- **用途**：
  - *查询服务器支持的 HTTP 方法或其他功能*。
- **特点**：
  - 请求体通常为空。
  - 响应头中的 `Allow` 字段列出支持的方法。
- **示例**：
  ```
  OPTIONS /api/users HTTP/1.1
  Host: www.example.com
  ```

---

### 7. **PATCH**

- **用途**：
  - 部分更新资源。
- **特点**：
  - 请求体包含需要更新的部分数据。
  - 不是幂等的（多次请求可能会累积更新）。
- **示例**：

  ```
  PATCH /api/users/123 HTTP/1.1
  Host: www.example.com
  Content-Type: application/json
  Content-Length: 18

  {
    "age": 32
  }
  ```

---

## 二、HTTP 方法的特点对比
安全性：参考下面的定义

| 方法        | 是否幂等 | 是否安全 | 是否可缓存 | 请求体 |
| ----------- | -------- | -------- | ---------- | ------ |
| **GET**     | 是       | 是       | 是         | 否     |
| **POST**    | 否       | 否       | 否         | 是     |
| **PUT**     | 是       | 否       | 否         | 是     |
| **DELETE**  | 是       | 否       | 否         | 否     |
| **HEAD**    | 是       | 是       | 是         | 否     |
| **OPTIONS** | 是       | 是       | 否         | 否     |
| **PATCH**   | 否       | 否       | 否         | 是     |

---

## 三、幂等性和安全性

1. **幂等性（Idempotent）**：

   - 多次执行相同的请求，结果保持一致。
   - 幂等方法：`GET`、`HEAD`、`PUT`、`DELETE`、`OPTIONS`。
   - 非幂等方法：`POST`、`PATCH`。

2. **安全性（Safe）**：
   - *请求不会对服务器状态产生副作用（如修改数据）*。
   - 安全方法：`GET`、`HEAD`、`OPTIONS`。
   - 非安全方法：`POST`、`PUT`、`DELETE`、`PATCH`。

---

## 四、RESTful API 中的使用

*在 RESTful API 中，HTTP 方法通常与 CRUD 操作对应：*

- **GET**：读取资源（Read）。
- **POST**：创建资源（Create）。
- **PUT**：更新资源（Update）。
- **DELETE**：删除资源（Delete）。
- **PATCH**：部分更新资源。

---

## 五、注意事项

1. **正确选择方法**：
   - 根据操作的语义选择合适的 HTTP 方法。
   - 避免滥用 `GET` 或 `POST`。
2. **安全性**：
   - 对敏感操作（如 `DELETE`、`PUT`）进行身份验证和授权。
3. **幂等性设计**：
   - 确保幂等方法的行为符合预期，避免重复操作导致问题。

---

## 六、扩展知识

1. **自定义方法**：
   - 虽然 HTTP 协议定义了标准方法，但开发者可以定义自定义方法（*不推荐*）。
2. **WebDAV 扩展**：
   - WebDAV 协议扩展了 HTTP 方法，增加了如 `PROPFIND`、`MKCOL` 等方法。

---

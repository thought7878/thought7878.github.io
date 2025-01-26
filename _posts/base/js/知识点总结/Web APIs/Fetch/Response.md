在前端开发中，`Response` 对象是 `Fetch API` 里的关键部分，它代表了服务器对 `fetch()` 请求的响应。以下将从基本概念、属性、方法、使用示例等方面对其进行详细介绍。

### 基本概念

当使用 `fetch()` 方法发起一个网络请求时，*它会返回一个 `Promise`，这个 `Promise` 会在请求完成后解析为一个 `Response` 对象*。`Response` 对象封装了*服务器响应的各种信息*，包括状态码、响应头以及响应体等，开发者可以通过该对象来获取和处理这些信息。

### 属性

#### 1. `status`

- **含义**：表示服务器响应的 HTTP 状态码，如 200 代表请求成功，404 代表请求的资源未找到，500 代表服务器内部错误等。
- **示例**：

```javascript
fetch("https://api.example.com/data").then((response) => {
  console.log(response.status);
});
```

#### 2. `statusText`

- **含义**：与 `status` 对应的状态文本描述，例如当 `status` 为 200 时，`statusText` 通常为 "OK"。
- **示例**：

```javascript
fetch("https://api.example.com/data").then((response) => {
  console.log(response.statusText);
});
```

#### 3. `ok`

- **含义**：这是一个布尔值属性，用于快速判断请求是否成功。当 `status` 状态码在 200 - 299 范围内时，`ok` 为 `true`；否则为 `false`。
- **示例**：

```javascript
fetch("https://api.example.com/data").then((response) => {
  if (response.ok) {
    console.log("Request succeeded");
  } else {
    console.log("Request failed");
  }
});
```

#### 4. `headers`

- **含义**：包含了服务器响应的所有头部信息，它是一个 `Headers` 对象，可用于访问和操作响应头。
- **示例**：

```javascript
fetch("https://api.example.com/data").then((response) => {
  const contentType = response.headers.get("Content-Type");
  console.log(contentType);
});
```

#### 5. `url`

- **含义**：表示最终响应的 URL，在经过重定向后，该 URL 可能与最初请求的 URL 不同。
- **示例**：

```javascript
fetch("https://api.example.com/data").then((response) => {
  console.log(response.url);
});
```

### 方法

#### 1. `clone()`

- **作用**：克隆一个 `Response` 对象。由于 `Response` 对象的主体只能被读取一次，当需要多次处理响应体时，可以使用 `clone()` 方法创建一个副本。
- **示例**：

```javascript
fetch("https://api.example.com/data").then((response) => {
  const clonedResponse = response.clone();
  // 可以分别处理原始响应和克隆后的响应
});
```

#### 2. `json()`

- **作用**：将响应体解析为 JSON 格式，并返回一个 `Promise`，该 `Promise` 会在解析完成后解析为解析后的 JavaScript 对象。
- **示例**：

```javascript
fetch("https://api.example.com/data")
  .then((response) => response.json())
  .then((data) => {
    console.log(data);
  });
```

#### 3. `text()`

- **作用**：将响应体解析为文本格式，并返回一个 `Promise`，该 `Promise` 会在解析完成后解析为解析后的文本字符串。
- **示例**：

```javascript
fetch("https://api.example.com/data")
  .then((response) => response.text())
  .then((text) => {
    console.log(text);
  });
```

#### 4. `blob()`

- **作用**：将响应体解析为二进制大对象（Blob），常用于处理图片、音频等二进制数据，并返回一个 `Promise`，该 `Promise` 会在解析完成后解析为解析后的 `Blob` 对象。
- **示例**：

```javascript
fetch("https://api.example.com/image")
  .then((response) => response.blob())
  .then((blob) => {
    const imageUrl = URL.createObjectURL(blob);
    const img = document.createElement("img");
    img.src = imageUrl;
    document.body.appendChild(img);
  });
```

#### 5. `formData()`

- **作用**：将响应体解析为 `FormData` 对象，常用于处理表单数据，并返回一个 `Promise`，该 `Promise` 会在解析完成后解析为解析后的 `FormData` 对象。
- **示例**：

```javascript
fetch("https://api.example.com/form")
  .then((response) => response.formData())
  .then((formData) => {
    for (let [key, value] of formData.entries()) {
      console.log(`${key}: ${value}`);
    }
  });
```

### 注意事项

- **响应体只能读取一次**：`Response` 对象的响应体只能被读取一次，读取后再次尝试读取会导致错误。如果需要多次处理响应体，应使用 `clone()` 方法。
- **错误处理**：在处理 `Response` 对象时，要注意检查 `status` 或 `ok` 属性，确保请求成功。如果请求失败，可能需要根据不同的状态码进行相应的错误处理。

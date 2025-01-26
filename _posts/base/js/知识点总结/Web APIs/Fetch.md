`Fetch` 是现代 JavaScript 中用于*发起网络请求*的 API，它为开发者提供了一种更强大、更灵活且*更简洁*的方式来与服务器进行数据交互。

### 基本概念

*`Fetch API` 基于 `Promise` 实现，使得异步请求的处理更加直观和易于管理*。与传统的 `XMLHttpRequest` 相比，`Fetch` 的语法*更加简洁*，并且提供了*更多的功能*，例如可以更方便地处理请求和响应的头信息、支持 `Body` 对象对请求和响应体进行操作等。

### 基本用法

使用 `Fetch` 发起一个简单的 `GET` 请求只需要调用 `fetch()` 函数，并传入请求的 URL 作为参数，它会返回一个 `Promise` 对象，该 `Promise` 会在请求完成时被解析为一个 `Response` 对象，代表服务器的响应。

```javascript
fetch("https://api.example.com/data")
  .then((response) => {
    // 检查响应状态是否成功（状态码 200 - 299）
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    // 将响应体解析为 JSON 格式
    return response.json();
  })
  .then((data) => {
    // 处理解析后的数据
    console.log(data);
  })
  .catch((error) => {
    // 处理请求过程中出现的错误
    console.error("Fetch error:", error);
  });
```

### 请求配置

`fetch()` 函数除了可以传入 URL 外，还可以传入一个可选的配置对象，*用于定制请求的各个方面*，如请求方法、请求头、请求体等。

```javascript
const requestOptions = {
  method: "POST", // 请求方法
  headers: {
    "Content-Type": "application/json", // 请求头，指定请求体的格式
  },
  body: JSON.stringify({ key: "value" }), // 请求体，将 JavaScript 对象转换为 JSON 字符串
};

fetch("https://api.example.com/submit", requestOptions)
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    return response.json();
  })
  .then((data) => {
    console.log(data);
  })
  .catch((error) => {
    console.error("Fetch error:", error);
  });
```

### 响应处理

`Response` 对象包含了服务器响应的各种信息，如状态码、响应头和响应体等。可以使用 `Response` 对象的不同方法来处理响应体，常见的方法有：

- **`response.json()`**：将响应体解析为 JSON 格式。
- **`response.text()`**：将响应体解析为文本格式。
- **`response.blob()`**：将响应体解析为二进制大对象（Blob），常用于处理图片、音频等二进制数据。

```javascript
fetch("https://api.example.com/image")
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    // 将响应体解析为 Blob 对象
    return response.blob();
  })
  .then((blob) => {
    // 创建一个 URL 对象来表示 Blob 数据
    const imageUrl = URL.createObjectURL(blob);
    // 在页面中显示图片
    const img = document.createElement("img");
    img.src = imageUrl;
    document.body.appendChild(img);
  })
  .catch((error) => {
    console.error("Fetch error:", error);
  });
```

### 错误处理

虽然 `fetch()` 返回的 `Promise` 在网络请求失败（如网络中断、DNS 解析失败等）时会被拒绝，但需要注意的是，当服务器返回的状态码不是 200 - 299 时，`Promise` 并不会被拒绝，而是会正常解析为一个 `Response` 对象。因此，在处理响应时，需要手动检查 `response.ok` 属性来判断请求是否成功。

### 兼容性和 polyfill

`Fetch API` 在现代浏览器中得到了广泛支持，但在一些旧版本的浏览器中可能不被支持。为了确保代码的兼容性，可以使用 `whatwg-fetch` 这样的 polyfill 库，它可以在不支持 `Fetch API` 的浏览器中模拟其功能。

### 与 `XMLHttpRequest` 对比

- **语法简洁**：`Fetch` 基于 `Promise`，避免了 `XMLHttpRequest` 中复杂的回调嵌套，使代码更易于阅读和维护。
- **功能丰富**：`Fetch` 提供了更方便的请求和响应处理方式，如可以直接处理 `JSON` 数据、操作请求和响应头信息等。
- **兼容性**：`XMLHttpRequest` 兼容性更好，几乎支持所有浏览器；而 `Fetch` 在旧浏览器中需要使用 polyfill。

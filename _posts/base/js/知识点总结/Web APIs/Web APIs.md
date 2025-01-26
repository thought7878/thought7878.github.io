Web APIs（Web 应用程序编程接口）是*浏览器提供的一系列接口*，允许开发者通过 JavaScript 与浏览器和网页进行交互。以下为你详细介绍一些常见的前端 Web APIs：

### 文档对象模型（DOM）API

- **作用**：用于操作 HTML 和 XML 文档，允许开发者动态地改变文档的内容、结构和样式。
- **常见方法和属性**
  - **节点选择**：`document.getElementById`（通过 ID 选择元素）、`document.getElementsByTagName`（通过标签名选择元素）、`document.getElementsByClassName`（通过类名选择元素）、`document.querySelector`（通过 CSS 选择器选择第一个匹配的元素）、`document.querySelectorAll`（通过 CSS 选择器选择所有匹配的元素）。
  - **节点操作**：`document.createElement`（创建元素节点）、`appendChild`（在父节点末尾插入子节点）、`removeChild`（删除子节点）、`replaceChild`（替换子节点）。
  - **属性操作**：可以通过元素的属性（如 `id`、`className` 等）和 `setAttribute`、`getAttribute` 方法来操作元素的属性。
- **示例**

```javascript
// 选择元素
const element = document.getElementById("myElement");
// 修改元素内容
element.textContent = "New content";
// 创建新元素
const newElement = document.createElement("p");
newElement.textContent = "This is a new paragraph";
// 将新元素插入到页面中
element.appendChild(newElement);
```

### 浏览器对象模型（BOM）API

- **作用**：允许开发者与浏览器窗口进行交互，控制浏览器的行为和状态。
- **常见对象和方法**
  - **window 对象**：是浏览器窗口的全局对象，包含了许多属性和方法，如 `alert`（弹出警告框）、`confirm`（弹出确认框）、`prompt`（弹出提示框）、`setTimeout`（设置定时器）、`setInterval`（设置间隔定时器）等。
  - **location 对象**：用于获取和设置当前页面的 URL 信息，如 `location.href`（获取或设置当前页面的 URL）、`location.reload`（重新加载当前页面）等。
  - **history 对象**：用于操作浏览器的历史记录，如 `history.back`（返回上一页）、`history.forward`（前进到下一页）、`history.go`（跳转到指定的历史记录位置）等。
- **示例**

```javascript
// 弹出警告框
window.alert("Hello, world!");
// 设置定时器
setTimeout(() => {
  console.log("This message will appear after 3 seconds");
}, 3000);
// 重定向到另一个页面
location.href = "https://www.example.com";
```

### 事件 API

- **作用**：用于处理用户与页面的交互，如点击、鼠标移动、键盘输入等。
- **常见事件类型和方法**
  - **事件类型**：`click`（点击事件）、`mouseover`（鼠标悬停事件）、`keydown`（键盘按下事件）等。
  - **事件绑定方法**：`addEventListener`（添加事件监听器）、`removeEventListener`（移除事件监听器）。
- **示例**

```javascript
const button = document.getElementById("myButton");
button.addEventListener("click", () => {
  console.log("Button clicked!");
});
```

### 网络 API

- **作用**：用于在浏览器和服务器之间进行数据交互，实现异步通信。
- **常见 API**
  - **XMLHttpRequest**：是最早用于实现异步数据传输的 API，虽然现在使用较少，但了解它有助于理解异步通信的原理。
  - **Fetch API**：是现代的、更简洁的网络请求 API，基于 Promise 实现。
- **示例（使用 Fetch API）**

```javascript
fetch("https://api.example.com/data")
  .then((response) => response.json())
  .then((data) => console.log(data))
  .catch((error) => console.error("Error:", error));
```

### 存储 API

- **作用**：用于在浏览器中存储数据，分为会话存储（`sessionStorage`）和本地存储（`localStorage`）。
- **常见方法**
  - `setItem`（存储数据）、`getItem`（获取数据）、`removeItem`（删除数据）、`clear`（清空存储）。
- **示例**

```javascript
// 存储数据
localStorage.setItem("username", "JohnDoe");
// 获取数据
const username = localStorage.getItem("username");
console.log(username);
// 删除数据
localStorage.removeItem("username");
```

### 地理定位 API

- **作用**：允许网页获取用户的地理位置信息。
- **常见方法**：`navigator.geolocation.getCurrentPosition`（获取当前位置）、`navigator.geolocation.watchPosition`（实时监听位置变化）。
- **示例**

```javascript
navigator.geolocation.getCurrentPosition(
  (position) => {
    const latitude = position.coords.latitude;
    const longitude = position.coords.longitude;
    console.log(`Latitude: ${latitude}, Longitude: ${longitude}`);
  },
  (error) => {
    console.error("Error getting location:", error.message);
  }
);
```

### Web Worker API

- **作用**：*允许在浏览器中创建后台线程，执行复杂的计算任务，而不会阻塞主线程，提高页面的响应性能*。
- **示例**

```javascript
// main.js
if (typeof Worker !== "undefined") {
  const worker = new Worker("worker.js");
  worker.postMessage("Hello, worker!");
  worker.onmessage = function (event) {
    console.log("Received from worker:", event.data);
  };
} else {
  console.log("Web Workers are not supported.");
}

// worker.js
onmessage = function (event) {
  const message = event.data;
  postMessage(`You sent: ${message}`);
};
```

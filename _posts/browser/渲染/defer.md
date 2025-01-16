# 定义和基本原理
- **定义**：`defer`是 HTML 中`<script>`标签的一个属性。当脚本标签带有`defer`属性时，浏览器会*异步地下载脚本文件，并且会**在浏览器完成 HTML 解析后，在`DOMContentLoaded`事件触发之前**按照它们**在文档中的顺序依次执行***。
- **工作方式**：例如，有一个 HTML 文件包含以下代码：

```html
<html>
<head>
    <script defer src="script.js"></script>
</head>
<body>
    <h1>My Page</h1>
    <p>Some content here.</p>
</body>
</html>
```

当浏览器解析这个 HTML 文件时，遇到`<script defer src="script.js"></script>`，会继续解析 HTML，同时在后台下载`script.js`。在整个 HTML 解析完成后，才会执行`script.js`。

# 与 HTML 解析的关系
- **减少阻塞**：和`async`属性类似，`defer`属性也可以减少脚本对 HTML 解析的阻塞。不过与`async`不同的是，`defer`脚本的执行顺序是按照它们在文档中的先后顺序来的。这使得开发者可以更好地控制脚本的执行顺序，确保脚本在合适的时机执行，并且可以访问完整的 DOM 树。
- **执行顺序确定性**：如果页面中有多个带有`defer`属性的脚本，它们会按照在文档中的先后顺序执行。例如，有两个`defer`脚本`script1.js`和`script2.js`在 HTML 文件中：

```html
<html>
<head>
    <script defer src="script1.js"></script>
    <script defer src="script3.js"></script>
</head>
<body>
    <h1>My Page</h1>
    <p>Some content here.</p>
</body>
</html>
```

浏览器会先下载`script1.js`和`script3.js`，在 HTML 解析完成后，先执行`script1.js`，然后执行`script3.js`。

# 适用场景
- **依赖 DOM 的脚本**：`defer`属性适合用于那些需要操作 DOM 树，但不急于在页面渲染过程中立即执行的脚本。比如初始化页面交互功能的脚本，像表单验证脚本、菜单交互脚本等。例如，一个网页中有一个用于处理菜单点击事件的脚本和一个用于初始化表单验证的脚本，都可以使用`defer`属性：

```html
<head>
    <script defer src="menu - click - handler.js"></script>
    <script defer src="form - validation.js"></script>
</head>
```

- **多个脚本的有序执行**：当有多个脚本之间存在依赖关系，并且需要在 HTML 解析完成后按顺序执行时，`defer`是很好的选择。例如，一个脚本可能会初始化一些全局变量，另一个脚本依赖这些全局变量进行更复杂的操作，就可以通过`defer`属性来确保它们的执行顺序。

# defer属性和async属性有什么区别？


# 参考资料
[[1-q1-async-defer-execution-order]]
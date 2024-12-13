# 定义和基本原理
- **定义**：`async`是 HTML 中`<script>`标签的一个属性。当脚本标签设置了`async`属性后，*浏览器在解析 HTML 文档遇到该脚本标签时，会异步地下载脚本文件*。这意味着*浏览器不会停止 HTML 的解析来等待脚本下载，而是在后台进行下载操作*。
- **工作方式**：例如，有一个 HTML 文件包含以下代码：

```html
<html>
<head>
    <script async src="script.js"></script>
</head>
<body>
    <h1>My Page</h1>
    <p>Some content here.</p>
</body>
</html>
```

当浏览器解析到`<script async src="script.js"></script>`时，它会*一边继续解析 HTML（也就是构建 DOM 树），一边在后台下载`script.js`。一旦`script.js`下载完成，浏览器会立即暂停 HTML 解析，执行这个脚本*。

# 与 HTML 解析的关系

- **减少阻塞**：`async`属性的主要优势在于*减少脚本对 HTML 解析的阻塞*。在没有`async`属性的情况下，浏览器遇到`<script>`标签时通常会暂停 HTML 解析来加载和执行脚本。而使用`async`后，大部分时间 HTML 解析可以正常进行，只有在脚本下载完成后的短暂时间内会暂停，从而加快了页面的初始加载速度。
- **执行顺序不确定性**：由于`async`脚本是*在下载完成后就立即执行，不考虑其在文档中的位置顺序，所以如果页面中有多个`async`脚本，它们的执行顺序是不确定的*。这取决于脚本文件的大小和网络速度等因素，哪个脚本先下载完成就先执行。例如，有两个`async`脚本`script1.js`和`script2.js`在 HTML 文件中：

```html
<html>
<head>
    <script async src="script1.js"></script>
    <script async src="script2.js"></script>
</head>
<body>
    <h1>My Page</h1>
    <p>Some content here.</p>
</body>
</html>
```

如果`script1.js`文件较小且网络状况较好，它可能会先下载完成并执行，即使它在文档中的位置在`script2.js`之前。

# 适用场景

- **独立功能脚本**：`async`属性*适用于那些不依赖于页面 DOM 树状态且相互独立的脚本*。比如一些*统计代码或者不与页面元素交互的工具函数*。例如，一个网站的页面分析工具脚本，它只需要发送数据到服务器，不需要操作页面元素，就可以使用`async`属性。

```html
<head>
    <script async src="analytics-script.js"></script>
</head>
```

- **加载第三方脚本**：当引入第三方脚本，且这些脚本不影响页面主要内容的加载和显示，并且彼此之间没有严格的执行顺序要求时，`async`是一个很好的选择。例如，一些广告脚本或者社交媒体分享按钮的脚本。

```html
<head>
    <script async src="ad-script.js"></script>
    <script async src="social-share-script.js"></script>
</head>
```
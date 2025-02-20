# 基本概念
- **定义**：Preload 是一种资源预加载机制，它允许开发者通过*在 HTML 的`<link>`标签中添加`rel="preload"`属性，来告诉浏览器在页面加载的早期阶段就开始下载特定的资源*。这些资源可以是*脚本、样式表、字体、图像*等多种类型。
- **目的**：主要是为了优化网页性能。在传统的资源加载方式中，*浏览器是按照文档中资源出现的顺序来加载的，这可能导致关键资源加载延迟*，影响页面的渲染和交互体验。**Preload 可以让浏览器提前获取*当前页面*的重要资源，使得这些资源在真正需要的时候已经准备好**，从而减少等待时间，提高页面响应速度。

![[_posts/base/js/Advanced Web Development Quiz/media/541846fc552c896fc55a0c01ff050e6e_MD5.jpeg]]

![[_posts/base/js/Advanced Web Development Quiz/media/41d8a1a044ce5b67e04b4bb8809e05a3_MD5.jpeg]]

# 使用方式
- **语法格式**：`<link rel="preload" href="资源路径" as="资源类型">`。其中，`href`属性指定要预加载资源的 URL；`as`属性用于明确资源的类型，这是非常关键的一点，因为它帮助浏览器确定如何处理预加载的资源；`rel`属性（[relationship](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/link#rel)）命名被链接的文档与当前文档的关系。
- **资源类型示例**：
	- **脚本（`as="script"`）**：例如`<link rel="preload" href="main.js" as="script">`，告诉浏览器预加载一个 JavaScript 脚本。预加载的脚本在下载完成后不会立即执行，只有在文档解析到该脚本的正常`<script>`标签位置或者通过 JavaScript 手动触发执行时才会执行。
	- **样式（`as="style"`）**：`<link rel="preload" href="styles.css" as="style">`用于预加载 CSS 样式表。和脚本不同，预加载的样式表会在下载完成后立即应用到页面，帮助加快页面渲染。
	- **字体（`as="font"`）**：像`<link rel="preload" href="font.woff2" as="font" crossorigin>`可以预加载字体文件。需要注意的是，对于跨域字体，通常需要添加`crossorigin`属性来确保正确加载。
	- **图像（`as="image"`）**：例如`<link rel="preload" href="logo.png" as="image">`，用于预加载图像资源，使图像在需要显示的时候能够快速加载。

# 工作原理
- **浏览器解析阶段**：当浏览器开始解析 HTML 文档时，一旦遇到带有`rel="preload"`属性的`<link>`标签，就会将该资源的预加载请求添加到浏览器的预加载队列中。浏览器会根据当前网络状况、资源优先级以及其他正在进行的任务，在合适的时机开始下载预加载资源。
- **资源优先级判定**：不同类型的资源有不同的优先级。一般来说，CSS 和字体资源的优先级相对较高，因为它们直接影响页面的渲染和可读性；而脚本资源的优先级则根据其在页面中的功能重要性和执行顺序来确定。例如，用于页面初始化的关键脚本可能会有较高优先级。
- **与缓存的关系**：预加载的资源会被存储在浏览器缓存中。如果同一资源在后续的页面加载或者同一页面的其他地方再次被请求，浏览器可以直接从缓存中获取，进一步提高资源获取效率。

# 应用场景
- **关键脚本提前加载**：对于一些单页应用（SPA），通常有一个用于启动应用的主脚本，如`app.js`。通过`preload`提前获取这个脚本，可以让页面更快地进入可交互状态。例如：

```html
<head>
    <link rel="preload" href="app.js" as="script">
</head>
```

- **字体优化**：对于一些使用自定义字体的页面，字体文件的加载可能会导致文本在一段时间内无法正确显示。使用`preload`可以提前加载字体，确保页面文字能够及时渲染。

```html
<head>
    <link rel="preload" href="custom-font.woff2" as="font" crossorigin>
</head>
```

- **懒加载资源准备**：在一些采用懒加载策略的页面中，虽然某些图像或脚本不是立即需要的，但可以提前预加载，以便在需要时能够更快地显示或执行。例如，对于一个图片轮播组件，下一张即将显示的图片可以提前预加载。

```html
<head>
    <link rel="preload" href="next-carousel-image.jpg" as="image">
</head>
```
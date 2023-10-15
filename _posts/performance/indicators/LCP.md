最大内容绘制 (LCP) 是测量[感知加载速度](https://web.dev/user-centric-performance-metrics/#types-of-metrics)的一个以用户为中心的重要指标，因为该项指标会在页面的主要内容基本加载完成时，在页面加载时间轴中标记出相应的点，迅捷的 LCP 有助于让用户确信页面是[有效的](https://web.dev/user-centric-performance-metrics/#questions)。

## 指标解释

### LCP (Largest Contentful Paint )

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_4236953005c4279feaafab5a2677479a)

**最大内容绘制 (LCP)** 指标会根据页面[首次开始加载](https://w3c.github.io/hr-time/#timeorigin-attribute)的时间点来报告可视区域内可见的最大[图像或文本块](https://web.dev/lcp/#what-elements-are-considered)完成渲染的相对时间。

### 哪些元素在考量范围内？

根据当前[最大内容绘制 API](https://wicg.github.io/largest-contentful-paint/)中的规定，最大内容绘制考量的元素类型为：

- `<img>`元素

- 内嵌在`<svg>`元素内的`<image>`元素

- `<video>`元素（使用封面图像）

- 通过[url()](https://developer.mozilla.org/docs/Web/CSS/url())函数（而非使用[CSS 渐变](https://developer.mozilla.org/docs/Web/CSS/CSS_Images/Using_CSS_gradients)）加载的带有背景图像的元素

- 包含文本节点或其他行内级文本元素子元素的[块级元素](https://developer.mozilla.org/docs/Web/HTML/Block-level_elements)。

### 何时报告最大内容绘制？

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_cd17b009a9bca2b61f36f82a166cd095)

<u>Web 页面通常是分阶段加载的</u>，<u>因此，页面上最大的元素可能会发生变化</u>。例如，在一个带有文本和图像的页面上，浏览器最初可能只是呈现文本，而此时浏览器会分派一个 largest-contentful-paint entry。稍后，图像完成加载，会分派第二个 largest-contentful-paint entry。

### 对比FCP/FMP/SI

| 指标  | 定义                 | 存在的问题                                                                                                                               |
| --- | ------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| FCP | 首次内容绘制时间           | 通常与用户无关，对用户意义不大                                                                                                                     |
| FMP | 首次绘制有意义内容的时间点      | [无法在所有 Web 浏览器中实现](https://developer.chrome.com/docs/lighthouse/performance/first-meaningful-paint/)。非标准化并且难以在浏览器之间统一实现，约20%的情况下不准确 |
| SI  | 跟踪视口中的加载内容的视觉进程/进度 | 复杂的指标，难以解释。计算密集，不可用于线上监控。                                                                                                           |

### LCP的优点

- 对用户体验有意义，容易理解

- 给出与FMP相似的结果。有API标准。

- 容易计算和上报

## 指标获取实现细节

创建一个 [PerformanceObserver](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver) ，使用 [Largest Contentful Paint API](https://wicg.github.io/largest-contentful-paint/) 监听并上报。具体的结算时机可以参考下文的代码实现

```js
new PerformanceObserver((entryList) => {
    for (const entry of entryList.getEntries()) {
        console.log('LCP candidate:', entry.startTime, entry);
    }})
.observe({type: 'largest-contentful-paint', buffered: true});
```

## 浏览器兼容性说明

指标要求浏览器支持 [Largest Contentful Paint API](https://wicg.github.io/largest-contentful-paint/) 和 [PerformanceObserver](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver)，在不兼容的情况下，上报的指标中 isSupport 为false。

## 如何优化LCP指标

LCP 主要受四个因素影响：

- 缓慢的服务器响应速度

- JavaScript 和 CSS 渲染阻塞

- 资源加载时间

- 客户端渲染

如需深入了解如何改进 LCP，请参阅[优化 LCP](https://web.dev/optimize-lcp/)。有关其他能够改进 LCP 的单个性能技巧的进一步指导，请参阅：

- [使用 PRPL 模式做到即时加载](https://web.dev/apply-instant-loading-with-prpl/)

- [优化关键渲染路径](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/)

- 优化CSS
  
  - [优化：推迟非关键 CSS](https://web.dev/defer-non-critical-css/)
  
  - [优化：缩小CSS](https://web.dev/unminified-css/)
  
  - [Extract critical CSS](https://web.dev/extract-critical-css/)
  
  - [Optimize CSS background images with media queries](https://web.dev/optimize-css-background-images-with-media-queries/)

- 优化图像
  
  - [Choose the right image format](https://web.dev/choose-the-right-image-format/)
  
  - [Choose the correct level of compression](https://web.dev/compress-images/)
  
  - [Use Imagemin to compress images](https://web.dev/use-imagemin-to-compress-images/)
  
  - [Replace animated GIFs with video for faster page loads](https://web.dev/replace-gifs-with-videos/)
  
  - [Serve responsive images](https://web.dev/serve-responsive-images/)
  
  - [Serve images with correct dimensions](https://web.dev/serve-images-with-correct-dimensions/)
  
  - [Use WebP images](https://web.dev/serve-images-webp/)
  
  - [Use image CDNs to optimize images](https://web.dev/image-cdns/)

- 优化网页字体
  
  - [优化：确保文本在网页字体加载期间保持可见](https://web.dev/font-display)
  
  - [Avoid invisible text during font loading](https://web.dev/avoid-invisible-text/)
  
  - [Optimize WebFont loading and rendering](https://web.dev/optimize-webfont-loading/)
  
  - [Reduce WebFont Size](https://web.dev/reduce-webfont-size/)

- 优化JavaScript（针对客户端渲染的网站）
  
  - [优化：使用 PRPL 模式做到即时加载](https://web.dev/apply-instant-loading-with-prpl/)
  
  - [缩小 JavaScript 体积](https://web.dev/unminified-javascript/)
  
  - [优化：减少 JavaScript 执行时间](https://web.dev/bootup-time/)
  
  - [Reduce JavaScript payloads with code splitting](https://web.dev/reduce-javascript-payloads-with-code-splitting/)
  
  - [优化：移除未使用的代码](https://web.dev/remove-unused-code/)
  
  - [Minify and compress network payloads](https://web.dev/reduce-network-payloads-using-text-compression/)
  
  - [Serve modern code to modern browsers for faster page loads](https://web.dev/serve-modern-code-to-modern-browsers/)
  
  - [Publish, ship, and install modern JavaScript for faster applications](https://web.dev/publish-modern-javascript/)
  
  - [How CommonJS is making your bundles larger](https://web.dev/commonjs-larger-bundles/)

## 其他资源

[安妮·沙利文 (Annie Sullivan)](https://perfnow.nl/)在[performance.now()](https://youtu.be/ctavZT87syI)上发表的演讲：[《从 Chrome 的性能监测工具中吸取的教训》](https://anniesullie.com/)(2019)

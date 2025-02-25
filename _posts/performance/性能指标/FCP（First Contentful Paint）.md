**FCP（First Contentful Paint，首次内容绘制）** 是一个关键的性能指标，用于*衡量用户从页面开始加载到看到页面中第一个内容元素的时间*。这个指标是用户体验优化的重要参考之一，因为它直接影响用户对页面加载速度的感知。


---

## **一、什么是 FCP？**
1. **定义**：
   - FCP 表示*从页面开始加载到浏览器渲染出第一个内容元素的时间*。
   - 这个“*内容元素*”可以是文本、图片、非空白的 `<canvas>` 元素或 SVG。

![[_posts/performance/性能指标/media/c65074b192112832e1572ade3eb45ce6_MD5.png]]


2. **意义**：
   - FCP 是用户感知页面加载速度的第一个重要信号。
   - 它标志着用户从白屏到看到实际内容的时间点，直接影响用户的初始体验。

3. **与 FP 的区别**：
   - **FP（First Paint）**：表示页面从白屏到渲染出任何像素的时间，包括背景色或边框等非内容性元素。
   - **FCP**：更进一步，关注的是用户可以看到的实际内容（如文本或图片）。

---

## **二、如何测量 FCP？**

### 1. **工具支持**
以下是一些常用的工具，可以帮助测量和分析 FCP：
- **Lighthouse**：
  - Google 提供的性能分析工具，可以在 Chrome DevTools 中运行。
  - 报告中会显示 FCP 的具体时间以及优化建议。
- **PageSpeed Insights**：
  - 输入 URL 即可获取页面的性能评分和 FCP 数据。
- **Chrome DevTools**：
  - 在 Performance 面板中录制页面加载过程，查看 FCP 时间点。
- **Web Vitals Library**：
  - 使用 Google 提供的 [web-vitals](https://github.com/GoogleChrome/web-vitals) JavaScript 库，在代码中直接测量 FCP。
    ```javascript
    import { getFCP } from 'web-vitals';

    getFCP((metric) => {
      console.log('FCP:', metric.value);
    });
    ```

---

## **三、影响 FCP 的因素**
FCP 的时间受多种因素影响，主要包括以下几个方面：

### 1. **网络延迟**
- 如果服务器响应时间较长（TTFB，Time to First Byte），会延迟 FCP。
- 解决方案：
  - 优化服务器性能。
  - 使用 CDN 加速静态资源。

### 2. **关键资源的加载**
- HTML、CSS 和 JavaScript 等关键资源的加载速度直接影响 FCP。
- 解决方案：
  - 压缩 HTML、CSS 和 JavaScript 文件。
  - 使用 HTTP/2 或 HTTP/3 提高传输效率。

### 3. **阻塞渲染的资源**
- 如果 CSS 或 JavaScript 文件阻塞了页面的渲染，会导致 FCP 延迟。
- 解决方案：
  - 将非关键 CSS 移到外部文件，并异步加载。
  - 对 JavaScript 使用 `defer` 或 `async` 属性。

### 4. **图片和媒体资源**
- 大尺寸图片或未优化的媒体资源会增加加载时间。
- 解决方案：
  - 压缩图片大小。
  - 使用现代图片格式（如 WebP）。
  - 延迟加载非关键图片。

### 5. **客户端性能**
- 用户设备的性能（如 CPU 和内存）也会影响 FCP。
- 解决方案：
  - 减少主线程的工作量。
  - 避免复杂的布局和绘制操作。

---

## **四、优化 FCP 的方法**

### 1. **减少服务器响应时间（TTFB）**
- 使用高性能的服务器或托管服务。
- 启用缓存机制（如浏览器缓存、CDN 缓存）。
- 优化后端逻辑，减少数据库查询时间。

### 2. **优化关键渲染路径**
- **内联关键 CSS**：
  - 将首屏需要的 CSS 内联到 HTML 中，避免额外的请求。
- **延迟非关键资源**：
  - 使用 `preload` 或 `prefetch` 优先加载关键资源。
  - 将非关键的 JavaScript 和 CSS 放在页面底部或异步加载。

### 3. **压缩和优化资源**
- **HTML/CSS/JS**：
  - 使用工具（如 Gzip 或 Brotli）压缩文件。
- **图片**：
  - 使用工具（如 ImageOptim 或 Squoosh）压缩图片。
  - 使用懒加载技术（`loading="lazy"`）。

### 4. **减少渲染阻塞**
- **CSS**：
  - 避免使用过多的外部样式表。
  - 将非关键 CSS 标记为非阻塞（如通过 `media` 属性）。
- **JavaScript**：
  - 使用 `defer` 或 `async` 属性加载脚本。
  - 避免在 `<head>` 中放置阻塞渲染的脚本。

### 5. **使用预加载和预连接**
- **预加载**：
  - 使用 `<link rel="preload">` 提前加载关键资源。
    ```html
    <link rel="preload" href="style.css" as="style">
    ```
- **预连接**：
  - 使用 `<link rel="preconnect">` 提前建立与第三方资源的连接。
    ```html
    <link rel="preconnect" href="https://example.com">
    ```

---

## **五、FCP 的目标值**
根据 Google 的 Core Web Vitals 标准：
- **良好**：FCP ≤ 1.8 秒。
- **需要改进**：1.8 秒 < FCP ≤ 3 秒。
- **较差**：FCP > 3 秒。

---

## **六、FCP 的局限性**
虽然 FCP 是一个重要的性能指标，但它也有一定的局限性：
1. **不反映交互性**：
   - FCP 只关注内容的可见性，而不涉及页面是否可用（如按钮是否可点击）。
   - 更全面的指标包括 **LCP（Largest Contentful Paint）** 和 **TTI（Time to Interactive）**。
2. **无法捕捉所有内容**：
   - 如果页面的内容主要是动态生成的（如通过 JavaScript 渲染），FCP 可能无法准确反映用户体验。

---

## **七、总结**
FCP 是衡量页面加载性能的重要指标之一，直接影响用户的初始体验。通过优化服务器响应时间、减少阻塞渲染的资源、压缩文件等方式，可以显著提升 FCP 的表现。同时，结合其他性能指标（如 LCP 和 TTI），可以更全面地优化用户体验。

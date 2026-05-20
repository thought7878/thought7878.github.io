在浏览器首次渲染 HTML 的过程中，会触发一系列关键的**生命周期事件**和**资源加载事件**。理解这些事件的触发时机和相互关系，对于控制脚本执行顺序、优化首屏加载性能以及进行前端性能监控至关重要。

以下是浏览器首次渲染 HTML 过程中触发事件的详细总结：

---

### 一、 核心文档生命周期事件

这是前端开发中最常接触的两个全局事件，标志着页面加载的不同阶段。

#### 1. `DOMContentLoaded` (简称 DCL)
*   **触发时机**：当 HTML 文档被**完全加载和解析完成**（即 DOM 树构建完成）之后触发，**无需等待**样式表（CSS）、图片（Images）和子框架（Iframes）完成加载。
*   **核心特点**：
    *   标志着 DOM 结构已经准备好，可以安全地进行 DOM 操作。
    *   **同步 `<script>` 会阻塞此事件**：因为浏览器必须等待同步 JS 执行完毕才能确认 DOM 没有被修改。
    *   **CSS 会间接阻塞此事件**：虽然 CSS 不直接阻塞 DOM 解析，但如果 CSS 后面跟着同步 `<script>`，浏览器会等待 CSS 加载并解析完（构建 CSSOM）后，才执行 JS，进而导致 `DOMContentLoaded` 被推迟。
*   **监听方式**：
    ```javascript
    document.addEventListener('DOMContentLoaded', (event) => {
        console.log('DOM 完全加载和解析完成');
    });
    // 或 jQuery 的 $(document).ready()
    ```

#### 2. `load`
*   **触发时机**：当整个页面及**所有依赖资源**（包括样式表、图片、字体、Iframes 等）都已完成加载时触发。
*   **核心特点**：
    *   标志着页面**完全加载完毕**，所有资源都已就位。
    *   通常用于需要获取图片实际尺寸、或者依赖所有外部资源才能执行的初始化逻辑。
    *   触发时间晚于 `DOMContentLoaded`。
*   **监听方式**：
    ```javascript
    window.addEventListener('load', (event) => {
        console.log('页面及所有资源加载完成');
    });
    ```

---

### 二、 文档状态变化事件 (`readystatechange`)

`document.readyState` 属性描述了文档的加载状态，当该状态改变时，会触发 `readystatechange` 事件。它提供了比上述两个事件更细粒度的控制。

#### 状态流转与事件触发：
1.  **`loading`（加载中）**：
    *   文档正在加载，HTML 正在解析。
2.  **`interactive`（可交互）**：
    *   **触发时机**：文档已被解析完成，DOM 树构建完毕。
    *   **对应关系**：此时触发 `readystatechange`，且状态变为 `interactive`。**这在时间点上几乎等同于 `DOMContentLoaded` 事件触发的前一刻**。此时子资源（图片、CSS）可能仍在加载。
3.  **`complete`（完成）**：
    *   **触发时机**：文档和所有子资源已完成加载。
    *   **对应关系**：此时触发 `readystatechange`，且状态变为 `complete`。**这在时间点上几乎等同于 `load` 事件触发的前一刻**。

*   **监听方式**：
    ```javascript
    document.addEventListener('readystatechange', (event) => {
        if (document.readyState === 'interactive') {
            console.log('DOM 解析完成 (类似 DOMContentLoaded)');
        } else if (document.readyState === 'complete') {
            console.log('所有资源加载完成 (类似 load)');
        }
    });
    ```

---

### 三、 资源加载相关事件

在解析 HTML 构建 DOM 的过程中，遇到外部资源标签时，会触发特定的资源事件。

#### 1. `<script>` 标签事件
*   **`load`**：外部脚本文件下载并**执行完毕**后触发。
*   **`error`**：脚本加载失败时触发。
*   *注意：内联脚本不会触发 `load` 和 `error` 事件。*

#### 2. `<link>` (CSS) 标签事件
*   **`load`**：外部样式表下载并**解析完毕**后触发。
*   **`error`**：样式表加载失败时触发。

#### 3. `<img>` / `<video>` / `<audio>` 等媒体标签事件
*   **`load`**：媒体资源完全加载完毕后触发。
*   **`error`**：媒体资源加载失败时触发。

---

### 四、 现代渲染性能指标事件 (Performance API)

在现代前端工程中，传统的 `load` 和 `DOMContentLoaded` 已不足以衡量用户的**真实视觉体验**。浏览器提供了 Performance API，通过 `PerformanceObserver` 可以监听页面首次渲染过程中的关键“视觉事件”（里程碑）。

#### 1. First Paint (FP - 首次绘制)
*   **含义**：浏览器将任何像素渲染到屏幕上的第一时刻（例如渲染背景色）。
*   **意义**：标志着浏览器**开始**在屏幕上绘制内容，打破了白屏。

#### 2. First Contentful Paint (FCP - 首次内容绘制)
*   **含义**：浏览器渲染出**第一个来自 DOM 的内容**（文本、图像、SVG 或非白色的 canvas）的时刻。
*   **意义**：用户开始看到页面的实际内容，是衡量首屏体验的核心指标。

#### 3. Largest Contentful Paint (LCP - 最大内容绘制)
*   **含义**：视口内**最大的内容元素**（通常是大图、视频封面或大段文本块）渲染完成的时刻。
*   **意义**：标志着页面的**主要内容**已经加载完毕，是 Web Vitals 核心指标之一。

*   **监听方式**：
    ```javascript
    const observer = new PerformanceObserver((list) => {
        for (const entry of list.getEntries()) {
            console.log(`${entry.name}: ${entry.startTime}ms`);
        }
    });
    // 监听 FCP 和 LCP
    observer.observe({ type: 'paint', buffered: true });
    observer.observe({ type: 'largest-contentful-paint', buffered: true });
    ```

---

### 五、 `<script>` 标签属性对事件时间轴的影响（核心考点）

JS 的加载和执行会严重影响 `DOMContentLoaded` 的触发。`<script>` 标签的 `defer` 和 `async` 属性会改变这一行为：

1.  **普通 `<script>`（无属性）**：
    *   阻塞 HTML 解析 -> 下载 JS -> 执行 JS -> 恢复 HTML 解析。
    *   **必须在所有普通同步脚本执行完毕后，才会触发 `DOMContentLoaded`。**
2.  **`<script defer>`**：
    *   异步下载 JS，**不阻塞** HTML 解析。
    *   等待 HTML 解析完成（DOM 树构建完毕）后，**按照在文档中出现的顺序执行**。
    *   **关键点**：`defer` 脚本的执行时机，**正好在 `DOMContentLoaded` 事件触发之前**。
3.  **`<script async>`**：
    *   异步下载 JS，**不阻塞** HTML 解析。
    *   下载完成后**立即执行**（会暂停 HTML 解析），执行顺序不可控。
    *   **关键点**：`async` 脚本的执行与 `DOMContentLoaded` **没有必然的先后关系**，可能在它之前，也可能在它之后。

---

### 六、 首次渲染事件时间轴全景图

按照时间先后顺序，首次渲染过程中的事件触发流如下：

```text
1. 开始解析 HTML (document.readyState = 'loading')
   │
   ├── 遇到外部 CSS/JS/图片，发起异步下载
   │
2. HTML 解析完成，DOM 树构建完毕
   │
   ├── [如果有 defer 脚本] 按顺序执行 defer 脚本
   │
   ├── 触发 document.readyState = 'interactive'
   ├── 触发 readystatechange 事件
   │
3. 触发 DOMContentLoaded 事件 (DCL)  <-- DOM 操作的安全起点
   │
   ├── 浏览器开始计算样式、布局、绘制 (触发 FP / FCP)
   │
4. 所有外部资源 (图片、CSS、iframe等) 下载并加载完成
   │
   ├── 触发 document.readyState = 'complete'
   ├── 触发 readystatechange 事件
   │
5. 触发 window.load 事件  <-- 所有资源就绪
   │
   ├── 持续渲染，最大元素绘制完成 (触发 LCP)
```

### 总结与实战建议

1.  **DOM 操作时机**：绝大多数 DOM 操作和框架（Vue/React）的初始化，都应该放在 `DOMContentLoaded`（或框架自身的 `mounted`/`useEffect`）中，而不是 `load`，以尽早交互。
2.  **性能优化**：将非关键 JS 加上 `defer` 属性，避免阻塞 DOM 解析和 `DOMContentLoaded` 的触发；将非首屏图片懒加载，避免推迟 `load` 事件。
3.  **监控埋点**：使用 `PerformanceObserver` 监听 FCP 和 LCP，比传统的 `load` 时间更能真实反映用户的首屏等待体验。
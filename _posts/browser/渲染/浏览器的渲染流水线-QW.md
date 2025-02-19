浏览器的 **渲染流水线（Rendering Pipeline）** 是指*从接收到 HTML、CSS 和 JavaScript 资源，到最终将内容绘制到屏幕上的整个过程*，也称为*关键渲染路径（Critical Rendering Path）*，是指*浏览器将 HTML、CSS 和 JavaScript 代码转换为屏幕上可见像素的一系列步骤*。这个过程涉及多个步骤，每个步骤都有特定的任务和优化点。理解渲染流水线对于前端开发者来说非常重要，*因为它直接影响页面的性能和用户体验*。

以下是浏览器渲染流水线的详细解析：

---

## **1. 渲染流水线的主要阶段**
参考：[【前端性能】浏览器渲染管道](https://www.bilibili.com/video/BV1zQ2zYeEjh/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)、 [An Introduction to the Browser Rendering Pipeline](https://webperf.tips/tip/browser-rendering-pipeline/)

浏览器的渲染流水线可以分为以下几个主要阶段：

### (1) **解析 HTML 并构建 DOM 树**
- **任务**：浏览器从网络中接收 HTML 文档，并逐步解析它。
- **结果**：生成一个 **DOM 树**，表示文档的结构化内容。
- **关键点**：
	- **遇到 `<script>` 标签:**
	    - **同步脚本:** 浏览器会暂停 HTML 解析，立即下载并执行 JavaScript 代码（除非脚本标记为 `async` 或 `defer`）。JavaScript 代码可能会修改 DOM 树和 CSSOM 树。
	    - **异步脚本 (`async`):** 浏览器会异步下载脚本，下载完成后立即执行。执行期间会阻塞 HTML 解析。
	    - **延迟脚本 (`defer`):** 浏览器会异步下载脚本，在 HTML 解析完成后、`DOMContentLoaded` 事件触发前执行。
	- **遇到 `<link rel="stylesheet">` 标签:** 浏览器会开始下载 CSS 文件。
  - 如果遇到 `<script>` 标签且没有 `async` 或 `defer` 属性，HTML 解析会被暂停，直到脚本加载和执行完成。
  - 如果遇到外部样式表（如 `<link rel="stylesheet">`），*HTML 解析不会暂停，但会阻塞渲染树的构建*。

#### 示例：
```html
<html>
  <head>
    <link rel="stylesheet" href="styles.css">
  </head>
  <body>
    <div>Hello World</div>
  </body>
</html>
```
在这个例子中，浏览器会先解析 HTML，生成 DOM 树。

---

### (2) **解析 CSS 并构建 CSSOM 树**
- **任务**：浏览器解析 CSS 文件或内联样式，生成 **CSSOM 树**（CSS Object Model）。
- **结果**：CSSOM 树描述了每个元素的样式规则。
- **关键点**：
  - CSS 是阻塞渲染的资源，必须在渲染树构建之前完成解析。
  - 外部样式表会阻塞渲染，因此建议尽量减少关键路径上的 CSS 文件大小。

#### 示例：
```css
div {
  color: red;
}
```
在这个例子中，浏览器会解析 CSS，生成 CSSOM 树。

---

### (3) **合并 DOM 和 CSSOM，生成渲染树**
- **任务**：将 DOM 树和 CSSOM 树合并，生成 **渲染树（Render Tree）**。
- **结果**：渲染树包含*所有可见*的节点及其样式信息。
- **关键点**：
  - 不可见的元素（如 `display: none`）不会出现在渲染树中。
  - 可见性隐藏的元素（如 `visibility: hidden`）会出现在渲染树中，但不会占用布局空间。

#### 示例：
```html
<div style="display: none;">Hidden</div>
<div style="visibility: hidden;">Invisible</div>
```
在这个例子中，第一个 `<div>` 不会出现在渲染树中，而第二个 `<div>` 会出现在渲染树中。

---

### (4) **布局（Layout）**
- **任务**：计算每个元素在屏幕上的*位置和大小*。
- **结果**：生成 **布局树（Layout Tree）**。
- **关键点**：
  - 布局是递归的过程，从根节点开始，逐层计算子节点的位置和大小。
  - 布局的复杂度与 DOM 树的深度和宽度成正比。

#### 示例：
```html
<div style="width: 100px; height: 50px;"></div>
```
在这个例子中，浏览器会计算 `<div>` 的宽高和位置。

---

### (5) **绘制（Paint）**
- **任务**：将渲染树中的每个节点绘制到屏幕上。
- **结果**：生成 **绘制层（Paint Layers）**。
- **关键点**：
  - 绘制包括填充颜色、绘制边框、应用阴影等操作。
  - 如果某些属性发生变化（如颜色、背景），只会触发重绘（Repaint）。

#### 示例：
```html
<div style="background-color: blue;"></div>
```
在这个例子中，浏览器会绘制蓝色背景。

---

### (6) **合成（Composite）**
- **任务**：将多个图层合并为最终的屏幕显示。
- **结果**：生成最终的屏幕图像。
- **关键点**：
  - 合成是 GPU 加速的过程，性能较高。
  - 某些属性（如 `transform` 和 `opacity`）只会影响合成阶段，不会触发布局或绘制。

#### 示例：
```html
<div style="transform: translateX(100px);"></div>
```
在这个例子中，浏览器会利用 GPU 加速，直接在合成阶段移动元素。

---

## **2. 渲染流水线的关键概念**

### (1) **回流（Reflow）**
- **定义**：当元素的几何属性（如宽高、位置）发生变化时，浏览器需要重新计算布局。
- **影响**：回流是一个*昂贵*的操作，因为它会递归影响所有子节点。
- **优化**：
  - 避免频繁修改布局属性。
  - 使用 `transform` 和 `opacity` 实现动画。

#### 示例：
```javascript
element.style.width = '200px'; // 触发回流
```

---

### (2) **重绘（Repaint）**
- **定义**：当元素的外观属性（如颜色、背景）发生变化时，浏览器需要重新绘制该元素。
- **影响**：*重绘比回流便宜*，但仍需避免频繁触发。
- **优化**：
  - 减少不必要的样式变化。
  - 使用 GPU 加速的属性（如 `transform` 和 `opacity`）。

#### 示例：
```javascript
element.style.backgroundColor = 'red'; // 触发重绘
```

---

### (3) **分层与合成**
- **定义**：浏览器会将页面分成多个图层，每个图层独立绘制和合成。
- **影响**：分层可以提升性能，因为只有受影响的图层需要重新绘制或合成。
- **优化**：
  - 使用 `will-change` 提示浏览器创建独立图层。
  - 避免过多的图层，以免增加内存消耗。

#### 示例：
```css
div {
  will-change: transform;
}
```

---

## **3. 渲染流水线的优化技巧**

### (1) **减少回流和重绘**
- 避免频繁修改布局属性（如 `width`、`height`）。
- 使用 `transform` 和 `opacity` 实现动画。

### (2) **使用 GPU 加速**
- 利用 GPU 加速的属性（如 `transform` 和 `opacity`）。
- 使用 `will-change` 提示浏览器创建独立图层。

### (3) **优化关键渲染路径**
- 减少关键路径上的资源数量和大小。
- 使用 `async` 或 `defer` 加载非关键脚本。

### (4) **压缩和缓存**
- 压缩 HTML、CSS 和 JavaScript 文件。
- 使用浏览器缓存减少重复加载。

---

## **4. 总结**
浏览器的渲染流水线是一个复杂的多阶段过程，每个阶段都有特定的任务和优化点。以下是关键点总结：
1. **解析 HTML 和 CSS**：生成 DOM 树和 CSSOM 树。
2. **合并生成渲染树**：过滤不可见元素，保留可见元素。
3. **布局和绘制**：计算位置和大小，绘制到屏幕上。
4. **合成**：将多个图层合并为最终的屏幕显示。
5. **优化技巧**：减少回流和重绘，利用 GPU 加速，优化关键渲染路径。

通过深入理解渲染流水线，开发者可以更好地优化代码，提升页面性能和用户体验。
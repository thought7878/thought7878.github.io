**CLS（Cumulative Layout Shift，累计布局偏移）** 是一个关键的性能指标，用于衡量页面加载过程中内容布局的稳定性。它是 Google 的 **Core Web Vitals** 中的核心指标之一，反映了用户在浏览页面时是否因布局突然变化而感到不适或困惑。

以下是关于 CLS 的详细总结：

---

## **一、什么是 CLS？**
1. **定义**：
   - CLS 衡量的是页面生命周期中所有意外布局偏移的累积分数。
   - 布局偏移是指页面上的可见元素突然移动，导致用户正在交互的内容发生变化。
   - 例如：图片加载完成后突然撑开页面、广告插入导致内容下移等。

2. **意义**：
   - 高 CLS 会导致用户体验变差，例如：
     - 用户点击了错误的按钮（因为按钮位置突然移动）。
     - 用户正在阅读的内容被推到屏幕之外。
   - 优化 CLS 可以提高页面的稳定性和可用性。

3. **计算方式**：
   - 每次布局偏移的分数 = 影响区域的面积 × 距离偏移的距离。
   - CLS 是页面加载过程中所有布局偏移分数的总和。

---

## **二、如何测量 CLS？**

### 1. **工具支持**
以下是一些常用的工具，可以帮助测量和分析 CLS：
- **Lighthouse**：
  - Google 提供的性能分析工具，可以在 Chrome DevTools 中运行。
  - 报告中会显示 CLS 的具体分数以及优化建议。
- **PageSpeed Insights**：
  - 输入 URL 即可获取页面的性能评分和 CLS 数据。
- **Chrome DevTools**：
  - 在 Performance 面板中录制页面加载过程，查看布局偏移的时间点和原因。
- **Web Vitals Library**：
  - 使用 Google 提供的 [web-vitals](https://github.com/GoogleChrome/web-vitals) JavaScript 库，在代码中直接测量 CLS。
    ```javascript
    import { getCLS } from 'web-vitals';

    getCLS((metric) => {
      console.log('CLS:', metric.value);
    });
    ```

---

## **三、影响 CLS 的因素**
CLS 的分数受多种因素影响，主要包括以下几个方面：

### 1. **未指定尺寸的媒体资源**
- 如果图片、视频或 iframe 等媒体资源没有明确的宽度和高度，加载完成后可能会撑开页面，导致布局偏移。
- 解决方案：
  - 为所有媒体资源设置固定的宽高比例（如 `aspect-ratio` 或 `width/height` 属性）。
    ```html
    <img src="example.jpg" width="600" height="400" alt="Example">
    ```

### 2. **动态插入内容**
- 动态插入的内容（如广告、弹窗、推荐内容）可能会挤占现有内容的位置，导致布局偏移。
- 解决方案：
  - 为动态内容预留空间（如占位符）。
  - 尽量避免在页面加载后插入内容。

### 3. **字体加载**
- 如果自定义字体加载较慢，浏览器可能会使用备用字体，导致文本重排。
- 解决方案：
  - 使用 `font-display: swap`，确保字体加载不会阻塞渲染。
    ```css
    @font-face {
      font-family: 'CustomFont';
      src: url('custom-font.woff2') format('woff2');
      font-display: swap;
    }
    ```

### 4. **CSS 动画和过渡**
- 不合理的 CSS 动画（如改变元素大小或位置）可能会导致布局偏移。
- 解决方案：
  - 使用 `transform` 和 `opacity` 实现动画，避免影响布局。
    ```css
    .animate {
      transition: transform 0.3s ease;
    }
    ```

---

## **四、优化 CLS 的方法**

### 1. **为媒体资源设置固定尺寸**
- 确保所有图片、视频和 iframe 都有明确的宽度和高度。
- 使用现代 CSS 属性（如 `aspect-ratio`）保持宽高比。
  ```html
  <img src="example.jpg" width="600" height="400" alt="Example">
  ```

### 2. **预留动态内容的空间**
- 为动态插入的内容（如广告、弹窗）预留空间。
- 使用占位符（Placeholder）避免内容加载后挤占现有空间。
  ```html
  <div style="height: 250px;">
    <!-- 广告内容 -->
  </div>
  ```

### 3. **优化字体加载**
- 使用 `font-display: swap`，确保字体加载不会阻塞渲染。
- 预加载关键字体文件。
  ```html
  <link rel="preload" href="custom-font.woff2" as="font" type="font/woff2" crossorigin>
  ```

### 4. **避免非必要的 DOM 操作**
- 减少页面加载过程中对 DOM 的频繁修改。
- 避免在页面加载后插入或删除大量内容。

### 5. **使用 CSS 动画而非布局更改**
- 使用 `transform` 和 `opacity` 实现动画，避免影响布局。
  ```css
  .fade-in {
    opacity: 0;
    transition: opacity 0.3s ease;
  }
  .fade-in.active {
    opacity: 1;
  }
  ```

---

## **五、CLS 的目标值**
根据 Google 的 Core Web Vitals 标准：
- **良好**：CLS ≤ 0.1。
- **需要改进**：0.1 < CLS ≤ 0.25。
- **较差**：CLS > 0.25。

---

## **六、CLS 的局限性**
虽然 CLS 是一个重要的性能指标，但它也有一定的局限性：
1. **仅适用于视觉变化**：
   - CLS 只关注布局的变化，不涉及其他性能问题（如加载速度或交互性）。
2. **依赖真实用户数据**：
   - CLS 更适合通过真实用户的数据（Field Data）测量，实验室数据（Lab Data）可能无法完全模拟用户行为。

---

## **七、总结**
CLS 是衡量页面布局稳定性的重要指标，直接影响用户的浏览体验。通过为媒体资源设置固定尺寸、预留动态内容的空间、优化字体加载等方式，可以显著降低 CLS 分数。同时，结合其他性能指标（如 LCP 和 FID），可以更全面地优化用户体验。

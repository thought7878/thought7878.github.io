累积布局偏移 (CLS) 是测量[视觉稳定性](https://web.dev/user-centric-performance-metrics/#types-of-metrics)的一个以用户为中心的重要指标，因为该项指标有助于量化用户经历意外布局偏移的频率，较低的 CLS 给用户呈现的效果是交互流程自然、没有延迟和卡顿。

## CLS 指标介绍(Cumulative Layout Shift)

CLS 测量整个页面生命周期内发生的所有[意外布局偏移量](https://web.dev/cls/#expected-vs.-unexpected-layout-shifts)中最大一连串的布局偏移分数。每当一个可见元素的位置从一个已渲染帧变更到下一个已渲染帧时，就发生了布局偏移 。更多信息，请参见[布局偏移分数](https://web.dev/cls/#layout-shift-score)。

## CLS 分数如何计算？

浏览器在计算布局偏移分数时，会查看可视区域大小和两个已渲染帧之间的可视区域中不稳定元素的位移。布局偏移分数是该位移的两个度量的乘积：影响分数和距离分数（两者定义如下）。

```
布局偏移分数 = 影响分数 * 距离分数
```

## 影响分数

[影响分数](https://github.com/WICG/layout-instability#Impact-Fraction)测量不稳定元素对两帧之间的可视区域产生的影响。前一帧和当前帧的所有不稳定元素的可见区域集合（占总可视区域的部分）就是当前帧的影响分数。

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_a74d8687f6ef005f028db94684b2059d)

在上图中，有一个元素在一帧中占据了一半的可视区域。接着，在下一帧中，元素下移了可视区域高度的 25%。红色虚线矩形框表示两帧中元素的可见区域集合，在本示例中，该集合占总可视区域的 75%，因此其影响分数为`0.75` 。

## 距离分数

布局偏移分数计算公式的另一部分测量不稳定元素相对于可视区域位移的距离。距离分数指的是任何不稳定元素在一帧中位移的最大距离（水平或垂直）除以可视区域的最大尺寸维度（宽度或高度，以较大者为准）。

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_a65b8a25ed702b70a8020f37453a2ed6)

在上方的示例中，最大的可视区域尺寸维度是高度，不稳定元素的位移距离为可视区域高度的 25%，因此距离分数为 0.25。  
所以，在这个示例中，影响分数是`0.75` ，距离分数是`0.25` ，所以布局偏移分数是`0.75 * 0.25 = 0.1875` 。

最初，布局偏移分数只根据影响分数进行计算。引入距离分数是为了避免在大元素发生微小位移的情况下进行过度惩罚的情况。

## 预期布局偏移 vs. 意外布局偏移

布局偏移并不总是坏事。事实上，许多动态网络应用程序经常更改页面元素的起始位置。

### 由用户发起的布局偏移

布局偏移只有在用户并不期望其发生时才算是坏事。换言之，对用户交互（单击链接、点选按钮、在搜索框中键入信息等）进行响应的布局偏移通常没有问题，前提是偏移发生的时机与交互时机足够接近，使用户对前后关系一目了然。  
例如，如果某次用户交互触发了一个网络请求，而该请求可能需要一段时间才能完成，那么最好立即留出一些空间并显示加载指示器，避免在请求完成时出现令用户不快的布局偏移。如果用户没有意识到当前正在加载某些内容，或者不知道资源什么时候能够准备就绪，他们就可能会在等待期间尝试单击其他内容（来打破僵局）。  
在用户输入 500 毫秒内发生的布局偏移会带有`[hadRecentInput](https://wicg.github.io/layout-instability/#dom-layoutshift-hadrecentinput)`标志，便于在计算中排除这些偏移。

注意

hadRecentInput`标志仅适用于不连续输入事件，如轻触、点击或按键操作。滚动、拖动或捏拉缩放手势等连续性交互操作不算作"最近输入"。详情请参见[布局不稳定性规范](https://github.com/WICG/layout-instability#recent-input-exclusion)。

### [怎样算是良好的 CLS 分数？](https://web.dev/cls/#cls-2)

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_9ac0509dd757b1af64c4ed9cae7c74c6)

为了提供良好的用户体验，网站应该将 CLS 分数控制在0.1 或以下。为了确保您能够在大部分用户的访问期间达成建议目标值，一个良好的测量阈值为页面加载的第 75 个百分位数，且该阈值同时适用于移动和桌面设备。

## 指标获取实现细节

Layout Shift 是由 [Layout Instability API](https://github.com/WICG/layout-instability) 定义的，当视图中可见的元素在两个帧之间改变其起始位置时，该API就会报告布局偏移项。这些元素被认为是不稳定元素。  
创建一个 [PerformanceObserver](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver) ，使用 [](https://wicg.github.io/largest-contentful-paint/)[Layout Instability API](https://github.com/WICG/layout-instability) 来监听意外的布局变化。

```javascript
let clsValue = 0;
let clsEntries = [];
let sessionValue = 0;
let sessionEntries = [];
new PerformanceObserver((entryList) => {
    for (const entry of entryList.getEntries()) {
        // Only count layout shifts without recent user input.
        if (!entry.hadRecentInput) {
            const firstSessionEntry = sessionEntries[0];
            const lastSessionEntry = sessionEntries[sessionEntries.length - 1];
        // If the entry occurred less than 1 second after the previous entry and      
        // less than 5 seconds after the first entry in the session, include the      
        // entry in the current session. Otherwise, start a new session.  
         if (sessionValue
         && entry.startTime - lastSessionEntry.startTime < 1000
         && entry.startTime - firstSessionEntry.startTime < 5000) {
             sessionValue += entry.value;
             sessionEntries.push(entry);
          } else {
              sessionValue = entry.value;
              sessionEntries = [entry];
           }
           // If the current session value is larger than the current CLS value
           // update CLS and the entries contributing to it.
           if (sessionValue > clsValue) {
               clsValue = sessionValue;
               clsEntries = sessionEntries;
               // Log the updated value (and its entries) to the console. 
               console.log('CLS:', clsValue, clsEntries)
           }
       }
}}).observe({type: 'layout-shift', buffered: true});
```

## 浏览器兼容性说明

指标要求浏览器支持 [Largest Contentful Paint](https://wicg.github.io/largest-contentful-paint/)、[Layout Instability API](https://github.com/WICG/layout-instability) 和 [PerformanceObserver](https://developer.mozilla.org/en-US/docs/Web/API/PerformanceObserver)，在不兼容的情况下，上报的指标中 isSupport 为false。

## 如何优化CLS指标

1. 在图片和视频元素中包含大小属性，或者用 CSS 长宽比框之类的东西保留所需的空间。

2. 不要在现有内容之上插入内容，除非是为了响应用户交互。

3. 多用 transform animations，而不是触发布局变化的 animations properties。

深入优化请参见[优化 Cumulative Layout Shift 累积布局偏移](https://web.dev/optimize-cls/)。



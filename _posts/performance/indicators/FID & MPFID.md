首次输入延迟 (FID) 是测量[加载响应度](https://web.dev/user-centric-performance-metrics/#types-of-metrics)的一个以用户为中心的重要指标，因为该项指标将用户尝试与无响应页面进行交互时的体验进行了量化，低 FID 有助于让用户确信页面是[有效的](https://web.dev/user-centric-performance-metrics/#questions)

## 什么是 FID 和 MPFID 指标

### 概念

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_8ac5e5936747e16e990a6d5f7c7b07b5)

Long Task，在浏览器主线程执行时间超过 50ms 的 Task。

Input Delay，输入延时，记录用户和页面进行交互操作所花费的时间。例如，<u>从用户点击一个按钮，到浏览器正确处理这个按钮的行为，并反馈给用户所花费的时间</u>。 通常情况下，<u>Input Delay 是因为浏览器主线程在忙于执行其他操作，无暇处理用户的交互操作</u>。

FID（ [First Input Delay]([First Input Delay (FID) &nbsp;|&nbsp; Articles &nbsp;|&nbsp; web.dev](https://web.dev/articles/fid))），**从交互开始到开始执行事件处理函数的时间**，测量从用户第一次与页面交互，例如当他们单击链接、点按按钮或使用由 JavaScript 驱动的自定义控件，直到浏览器对交互作出响应，并实际能够开始执行事件处理程序所经过的时间。

MPFID（Max Potential First Input Delay），记录在页面加载过程中用户和页面进行首次交互操作可能花费的最长时间。

FID 发生在 FCP 和 TTI 之间，因为这个阶段虽然页面已经显示出部分内容，但尚不具备完全的可交互性。这个阶段用户和页面交互，往往会有较大延迟。如下图所示，浏览器接收到用户输入操作时，主线程正在忙于执行一个 Long Task，只有当这个 Task 执行完成后，浏览器才能响应用户的输入操作。

### 通用指标

尽管任何输入的延迟都会破坏用户体验，我们只选取 FID 作为通用指标是基于以下原因：

- FID 反映用户对页面交互性和响应性的第一印象，良好的第一印象有助于用户建立对整个应用的良好印象。
- 页面加载阶段，资源的处理任务最重，也最容易产生输入延迟。因此关注 FID 指标对于提升页面的可交互性有更大收益。
- FID 和页面加载完成后的 Input Delay 具有不同的解决方案。针对 FID，我们一般建议通过 Code Splitting 等方式减少页面加载阶段 JS 的加载、解析和执行时间。而页面加载完成后的 Input Delay，通常是由于开发人员代码编写不当、引起 JS 执行时间过长而产生的。

## 怎样算是良好的 FID 分数？

为了提供良好的用户体验，网站应该努力将首次输入延迟设控制在100 毫秒或以内。为了确保您能够在大部分用户的访问期间达成建议目标值，一个良好的测量阈值为页面加载的第 75 个百分位数，且该阈值同时适用于移动和桌面设备。

## 指标计算

### FID 指标

FID 的计算需要用户真实操作页面，可以借助 [Event Timing API](https://wicg.github.io/event-timing) 进行测量：创建 PerformanceObserver 对象，监听 `*first-input*` 事件，监听到 `*first-input*` 事件后，利用 Event Timing API，通过事件的开始处理时间，减去事件的发生时间，即为 FID。  
因为 FID 的值严重依赖用户触发操作的时机，需要考虑 FID 值的分布曲线。一般情况下，建议采用 95 分位的指标值，能反映最差的用户交互体验对应的 FID 值。

### MPFID 指标

借助 [Long Tasks API](https://w3c.github.io/longtasks/) 收集页面加载过程中的 Long Task，寻找耗时最长的 Long Task，这个 Long Task 的耗时即为 MPFID。

### 浏览器兼容性说明

- FID 指标要求浏览器支持 [Event Timing API](https://wicg.github.io/event-timing) ，在不兼容的情况下，上报的指标中 isSupport 为false。
- MPFID 指标要求浏览器支持 [Long Tasks API](https://w3c.github.io/longtasks/) ，在不兼容的情况下，上报的指标中 isSupport 为false。

## 如何优化 FID 和 MPFID 指标

了解如何改进某个特定网站的 FID，您可以运行一次 Lighthouse 性能审计，并留心查看审计建议的各种具体[机会](https://web.dev/lighthouse-performance/#opportunities)。  
虽然 FID 是一项实际指标，灯塔是一个实验室指标工具，但改进 FID 的指导方向与改进[总阻塞时间 (TBT)](https://web.dev/tbt/)这项实验室指标的指导方向相同。  
深入了解如何改进 FID，请参阅[优化 FID](https://web.dev/optimize-fid/)。有关其他能够改进 FID 的单个性能技巧的进一步指导，请参见：

- [优化：减少第三方代码的影响](https://web.dev/third-party-summary/)
- [优化：减少 JavaScript 执行时间](https://web.dev/bootup-time/)
- [优化：最小化主线程工作](https://web.dev/mainthread-work-breakdown/)
- [优化：保持较少的请求数以及较小的传输大小](https://web.dev/resource-summary/)

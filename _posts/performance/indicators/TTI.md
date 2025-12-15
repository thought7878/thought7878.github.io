可交互时间 (TTI) 是测量[加载响应度](https://web.dev/user-centric-performance-metrics/#types-of-metrics)的重要[实验室指标](https://web.dev/user-centric-performance-metrics/#in-the-lab)。该指标有助于识别*看起来*具备交互性但实际上并非如此的页面情况。迅捷的 TTI 有助于确保页面的[有效性](https://web.dev/user-centric-performance-metrics/#questions)。

## 什么是 TTI 指标

![](https://lf3-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_bf1f73dc0b8a1771ebf0afb7bf6855db)

`TTI（Time To Interactive）`，即**从页面加载开始到页面处于*完全可交互状态*所花费的时间**。<u>页面处于完全可交互状态时，满足以下 3 个条件：</u>

1. 页面已经显示有用内容。
2. 页面上的可见元素关联的事件响应函数已经完成注册。
3. 事件响应函数可以在事件发生后的 50ms 内开始执行。

很多情况下，开发者往往只关注页面渲染相关的指标，如 FP、FCP 等，而<u>忽视了页面的可用性指标。TTI 即是反映页面可用性的重要指标</u>。**TTI 值越小，代表用户可以更早地操作页面**，用户体验就更好。

## 如何获取 TTI 指标

用户访问 Web 页面，通常会有两种模式:

- 直接通过服务端路由切换的同步跳转场景
- 通过客户端路由跳转的 SPA 页面切换场景

### 算法描述

Long Task ：在浏览器主线程执行时间超过 50ms 的 Task。

静默窗口期：窗口所对应的时间内没有 Long Task，且进行中的网络请求数不超过 2 个。

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_b10594201c466064aba4c97f0562ed68)

参考上述示意图（图中的 First Consistently Interactive 即为 TTI ）。

1. 从起始点（一般选择 FCP 或 FMP）时间开始，向前搜索一个不小于 5s 的静默窗口期。静默窗口期：窗口所对应的时间内没有 Long Task，且进行中的网络请求数不超过 2 个。
2. 找到静默窗口期后，从静默窗口期向后搜索到最近的一个 Long Task，Long Task 的结束时间即为 TTI。
3. 如果没有找到 Long Task，以起始点时间作为 TTI。
4. 如果 2、3 步骤得到的 TTI < DOMContentLoadedEventEnd，以 DOMContentLoadedEventEnd 作为TTI。

### 浏览器兼容性说明

TTI 指标要求浏览器支持 [Long Tasks API](https://www.w3.org/TR/2017/WD-longtasks-1-20170907/) 和 [Resource Timing API](https://www.w3.org/TR/resource-timing-2/)，在浏览器不兼容的情况下，上报的指标中 isSupport 为false。

## 如何优化 TTI 指标

如需了解如何改进某个特定网站的 TTI，您可以运行一次 Lighthouse 性能审计，并留心查看审计建议的各种具体[机会](https://web.dev/lighthouse-performance/#opportunities)。  
如需了解改进 TTI 的常见方式（针对任何网站），请参见以下性能指南：

- [优化：缩小 JavaScript 体积](https://web.dev/unminified-javascript/)
- [优化：预连接到所需的来源](https://web.dev/uses-rel-preconnect)
- [优化：预加载关键请求](https://web.dev/uses-rel-preload/)
- [优化：减少第三方代码的影响](https://web.dev/third-party-summary/)
- [优化：最小化关键请求深度](https://web.dev/critical-request-chains/)
- [优化：减少 JavaScript 执行时间](https://web.dev/bootup-time/)
- [优化：最小化主线程工作](https://web.dev/mainthread-work-breakdown/)
- [优化：保持较低的请求数和较小的传输大小](https://web.dev/resource-summary/)

## 参考资料

- https://docs.google.com/document/d/1GGiI9-7KeY3TPqS3YT271upUVimo-XiL5mwWorDUD4c
- https://web.dev/tti

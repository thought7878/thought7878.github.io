## 指标解释

[First Contentful Paint (FCP)-google](https://web.dev/articles/fcp?hl=zh-cn)

https://www.volcengine.com/docs/6431/107441

### FP (First Paint)

- **首次渲染的时间点**。 在性能统计指标中，从用户开始访问 Web 页面的时间点到 FP 的时间点这段时间可以被视为**白屏时间**，也就是说在用户访问 Web 网页的过程中，<u>FP 时间点之前，用户看到的都是没有任何内容的白色屏幕</u>，用户在这个阶段感知不到任何有效的工作在进行。
- 所以通常会反映页面的白屏时间，而白屏时间会反映当前 Web 页面的网络加载性能情况，<u>当加载性能非常良好的情况下，白屏的时间就会越短，用户等待内容的时间就会越短</u>，流失的概率就会降低。

### FCP (First Contentful Paint)

![](https://lf3-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_57b8d622713192ceec0f35f1e8c1ff10)

- **首次内容绘制 (FCP)** 指标测量<u>页面从开始加载到页面内容的任何部分在屏幕上完成渲染的时间</u>。对于该指标，"内容"指的是文本、图像（包括背景图像）、`<svg>`元素或非白色的`<canvas>`元素。
- **首次有内容渲染的时间点**。 在性能统计指标中，从用户开始访问 Web 页面的时间点到 FCP 的时间点这段时间可以被视为 <u>无内容时间</u>，也就是说<u>在用户访问 Web 网页的过程中，FCP 时间点之前，用户看到的都是没有任何实际内容的屏幕</u>，<u>用户在这个阶段获取不到任何有用的信息</u>。
- 所以通常会反映页面的首次出现内容的时间，而<u>首次出现内容时间会反映当前 Web 页面的网络加载性能情况、页面 DOM 结构复杂度情况、inline script 的执行效率的情况</u>，当所有的阶段性能做的非常好的情况下，首次出现内容的时间就会越短，用户等待的时间就会越短，流失的概率就会降低。

### FP与FCP这两个指标之间的主要区别是：

- FP是当浏览器开始绘制内容到屏幕上的时候，只要在视觉上开始发生变化，无论是什么内容触发的视觉变化，在这一刻，这个时间点，叫做FP。
- FCP指的是浏览器首次绘制来自DOM的内容。例如：文本，图片，SVG，canvas元素等，这个时间点叫FCP。

## 怎样算是良好的 FCP 分数？

为了提供良好的用户体验，网站应该努力将首次内容绘制控制**在1.8 秒或以内**。为了确保您能够在大部分用户的访问期间达成建议目标值，一个良好的测量阈值为页面加载的第 75 个百分位数，且该阈值同时适用于移动和桌面设备。

## 如何优化FP && FCP指标

- [优化：消除阻塞资源](https://web.dev/render-blocking-resources/)
- [优化：缩小 CSS](https://web.dev/minify-css/)
- [优化：移除未使用的CSS](https://web.dev/unused-css-rules/)
- [优化：预连接到所需的来源](https://web.dev/uses-rel-preconnect)
- [优化：减少服务端响应时间（TTFB）](https://web.dev/time-to-first-byte/)
- [优化：避免多个页面重定向](https://web.dev/redirects/)
- [优化：预加载关键请求](https://web.dev/uses-rel-preload/)
- [优化：避免巨大的网络负载](https://web.dev/total-byte-weight/)
- [优化：如何使用高效缓存策略去缓存静态资源](https://web.dev/uses-long-cache-ttl/)
- [优化：避免 DOM 过大](https://web.dev/dom-size/)
- [优化：最小化关键请求深度](https://web.dev/critical-request-chains/)
- [优化：确保文本在网页字体加载期间保持可见](https://web.dev/font-display)
- [优化：保持较少的请求数以及较小的传输大小](https://web.dev/resource-summary/)

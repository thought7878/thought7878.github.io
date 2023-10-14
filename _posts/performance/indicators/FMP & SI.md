> [Lighthouse 6.0 中已弃用首次有意义的绘制 (FMP)。实际上，FMP 对页面加载的微小差异过于敏感，导致结果不一致（双峰）。此外，该指标的定义依赖于特定于浏览器的实现细节，这意味着它无法标准化，也无法在所有 Web 浏览器中实现。展望未来，请考虑使用 Largest Contentful Paint]([First Meaningful Paint - Chrome for Developers](https://developer.chrome.com/docs/lighthouse/performance/first-meaningful-paint/))

## 什么是 FMP 指标

FMP（First Meaningful Paint），即**首次绘制有意义内容的时间**，如下图所示，**当整体页面的布局和文字内容全部渲染完成后**，即可认为是完成了首次有意义内容的绘制。所以 <u>FMP 衡量了用户看到网页的主要内容的时间，是用户体验角度的一种重要的衡量指标</u>。

![](https://lf3-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_e633d54fa79c53768583cb7f9e9ca2c2)

**通常业界会将 FMP 的时间当成是首屏时间**，虽然在绝对准确度方面不会相等，但是都可以精准的反映出当前页面的加载和渲染的性能情况，<u>FMP 通常被认为是用户获取到了页面主要信息的时刻</u>，也就是说此时用户的需求是得到了满足的，所以产品通常也会关注 FMP 指标。

## 如何计算 FMP 指标

随着网页的加载与解析，浏览器会将布局对象（Layout Object）逐步添加到布局树（Layout Tree）上进行布局。

![](https://lf3-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_68ed35ebc4ac4ed403b5b5a79fe3fbdf)![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_022453e312bca966853b57914fb7c84d)

- 图一和图二从两个不同的角度展示了加载Google Search 结果页的过程。
- 图一展示了加载Google Search 结果页过程中，<u>随时间变化，添加到布局树中的布局对象的个数的变化</u>。
- 图二展示了加载Google Search 结果页在加载和渲染过程中的可视化过程。

详细通过图一和图二解读整个Google Search 结果页页面加载和渲染过程如下：

1. 在 1.577 秒，此时已经有 60 个布局对象被添加到了布局树中( 图一 )，这时候页面只渲染了一个 Header( 图二 )。
2. 在 1.760 秒，此时布局对象总数是 103 个( 图一 )，这时候页面头部整体渲染完成( 图二 )。
3. 在 1.907 秒，此时搜索结果数据返回并渲染，有 261 个布局对象被添加到布局树中( 图一 )。而此时页面主体内容已经绘制完成( 图二 )，从用户体验的角度看，此时的时间点就是 FMP。
4. 随后其他搜索结果和页面底部的布局对象继续被添加到布局树中并进行绘制。页面在 2.425 秒完成最终加载和渲染( 图二 )。

结合对图一和图二的解读，从以上对于Google Search 结果页加载过程的例子中可以看出，<u>布局对象的数量与页面完成度高度相关</u>。前端业界现在**比较认可的一个计算 FMP 的方式**就是认定页面在加载和渲染过程中最大布局变动之后的那个绘制时间即为当前页面的 FMP 。

### 代码实现原理

代码实现的理论依据在于：认为DOM 结构变化的时间点与之对应渲染的时间点近似相同。则 FMP 的时间点为 DOM 结构变化最剧烈的时间点。

DOM 结构变化的时间点可以利用 [MutationObserver API](https://dom.spec.whatwg.org/#mutationobserver) 来获得。  通过 MutationObserver 监听每一次页面整体的 DOM 变化，触发 MutationObserver 的回调，在回调计算出当前 DOM 树的分数，分数变化最剧烈的时刻，即为 FMP 的时间点。

### 浏览器兼容性说明

FMP 指标要求浏览器支持 [MutationObserver API](https://dom.spec.whatwg.org/#mutationobserver)，在浏览器不支持 MutationObserver API 的情况下，上报的指标中 isSupport 为false。

### 数据准确性说明

通过 FMP 的计算方法我们可以得知，当前无法通过浏览器提供的 API 获取浏览器绘制时间以及 FMP 的具体时间（不同于 FP，FCP），因此业界通常以最大布局变化的时间点作为 FMP，然而通常这个值会比真实的 FMP 略小（实验数据中，发现的最大误差范围约为 300ms 内）。

关于 SDK 对于 FMP 指标的实现，这里需要重点说明的是：FMP 计算的绝对值并不是绝对精准的，但是是相对精准的，计算方法对于所有接入 SDK 的 Web 页面也都是一致的，也就是说，所有的页面的数据维度都是一致的，口径也是一致的。在当前 FMP 指标计算方式下，FMP 是能够相对稳定和正确的的给出 Web 页面的加载和渲染情况。所以我们可以更多的关注 FMP 在报表消费时候的趋势变化。

## 如何优化 FMP 指标

优化 FMP 指标，关键是缩短页面关键路径的渲染时间。常见的优化方法有：

1. 优化页面的[关键路径](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/)。
2. 减少外链 CSS 资源的数量 。
3. 减少同步加载的外链 JS 资源数量。
4. 合理使用 HTTP 缓存。
5. 压缩静态资源。
6. 优化页面加载过程中的 JS 执行时间。
7. [引入外部字体文件时，避免因为等待字体文件的加载而产生的文字不可见问题](https://web.dev/font-display/)。

## SI

Speed Index，衡量页面可视区域加载速度，帮助检测页面的加载体验差异。

[速度指数]([Speed Index - Chrome for Developers](https://developer.chrome.com/docs/lighthouse/performance/speed-index/))衡量页面加载期间内容视觉显示的速度。 Lighthouse 首先捕获浏览器中页面加载的视频，并计算帧之间的视觉进度。然后，Lighthouse 使用 Speedline Node.js 模块生成速度指数分数。

## 参考资料

- https://web.dev/first-meaningful-paint/
- https://docs.google.com/document/d/1BR94tJdZLsin5poeet0XoTW60M0SjvOJQttKT-JK8HI/view
- https://scotch.io/courses/10-web-performance-audit-tips-for-your-next-billion-users-in-2018/FMP-first-meaningful-paint

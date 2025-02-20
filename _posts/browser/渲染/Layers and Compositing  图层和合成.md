Web developers typically aren't exposed to low-level browser rendering primitives, yet the concepts of Layers and Compositing are foundational to how the browser renders web applications as pixels on screen! In this tip, I'll explain what Layers are, why they exist, and how they are Composited together to produce on-screen Frames to the user's display.  
网页开发者通常不会接触到低级浏览器渲染原语，然而*图层和合成概念是浏览器如何将网页应用作为屏幕上的像素渲染的基础*！在本提示中，我将解释层是什么，为什么存在，以及它们是如何组合在一起以向用户显示屏幕上的帧的。

## [](https://webperf.tips/tip/layers-and-compositing/#prerequisites)Prerequisites  先决条件

- You should understand [the basics on the Browser Rendering Pipeling](https://webperf.tips/tip/browser-rendering-pipeline)  
    您应该了解浏览器渲染管道的基本知识
- You should understand [the basics on the Browser Process Model](https://webperf.tips/tip/browser-process-model)  
    您应该了解浏览器进程模型的基本知识

## [](https://webperf.tips/tip/layers-and-compositing/#layers)Layers  层级

Layers are groups of visual elements that share the same coordinate plane. They are rendered onto a 2-dimensional mesh and retained in GPU memory. Below is an example of Layers (and we'll dive into detail later):  
图层是共享同一坐标平面的视觉元素组。它们被渲染到二维网格上并保留在 GPU 内存中。下面是图层的一个示例（我们稍后会详细介绍）：

![[_posts/browser/渲染/media/35789c95a0dfc29f88019569a0b131c2_MD5.gif]]

All modern web browsers support Layers, yet the exact implementation details may differ and evolve as browsers are continually changing.  
所有现代网络浏览器都支持层，但具体的实现细节可能不同，并且随着浏览器的不断变化而发展和演变。

> I encourage you to read the Layer source code in Chromium, [found here](https://source.chromium.org/chromium/chromium/src/+/main:cc/layers/layer.h;l=87;drc=69e6dc49684309c8b375c4dcd724c6ae61878ecd)!  
> 我鼓励你阅读位于这里的 Chromium 中的 Layer 源代码！

## [](https://webperf.tips/tip/layers-and-compositing/#why-do-layers-exist)Why do Layers exist?  为什么层存在？

Layers exist as an optimization. The browser will render (draw pixels for) multiple Layers and retain the results in GPU memory for subsequent use. When a Frame needs to be produced (i.e. scrolling, animating), it's faster to transform and compose multiple pre-rendered Layers rather than re-drawing the viewport on demand.  
层作为优化存在。浏览器将渲染（为多个层绘制像素）并保留结果在 GPU 内存中供后续使用。当需要生成帧（即滚动、动画）时，转换和组合多个预渲染的层比按需重新绘制视口更快。

Furthermore, Layers can be visually updated independently by a separate thread, the _Compositor Thread_ without competition from heavy JavaScript or other activity taking place on the Main Thread.  
此外，层可以通过独立的线程（合成线程）进行视觉更新，而不会受到主线程上运行的重量级 JavaScript 或其他活动的竞争。

This is a tradeoff, where GPU memory is traded to optimize rendering performance, but generally the tradeoff pays significant dividends for end user experience.  
这是权衡，以优化渲染性能为代价换取 GPU 内存，但通常这种权衡对用户体验有显著回报。

## [](https://webperf.tips/tip/layers-and-compositing/#when-are-layers-created)When are Layers created?  层何时创建？

The conditions for when a Layer is created will vary by browser implementation. Common examples where the browse will create Layers include:  
层创建的条件因浏览器实现而异。浏览器创建层的常见示例包括：

- Scrollable areas  可滚动区域
- Regions driven by CSS animations  
    由 CSS 动画驱动的区域
- Videos rendered through `<video>`  
    通过 `<video>` 渲染的视频
- Canvas elements through `<canvas>`  
    画布元素通过 `<canvas>`
- Regions explicitly promoted by CSS via `will-change: transform`  
    CSS 通过 `will-change: transform` 明确推广的区域

In Chromium, [here is the list of conditions](https://source.chromium.org/chromium/chromium/src/+/refs/heads/main:third_party/blink/renderer/platform/graphics/compositing_reasons.h;l=18;drc=4e8e81f6eeb6969973f3ec97132d80339b92d227) that promote a group of visual elements to dedicated layer.  
在 Chromium 中，以下是将一组视觉元素提升到专用层的条件列表。

## [](https://webperf.tips/tip/layers-and-compositing/#how-do-layers-operate-during-runtime)How do Layers operate during Runtime?  
如何运行时层工作？

Consider the following diagram:  
考虑以下图示：

![[_posts/browser/渲染/media/28d7e8385c8ef51cfae1bb5c43f7f120_MD5.png|"A diagram showing the Compositor Thread, Raster Threads, and Main Thread coordinating the steps described below."]]

Each browser operates differently, but at a high level, it works like this:  
每个浏览器的工作方式都不同，但大体上，它的工作原理是这样的：

1. At some cadence, the Browser's Main Thread will run the [Rendering Steps of Style and Layout](https://webperf.tips/tip/browser-rendering-pipeline), and begin trying to visually represent the DOM and CSSOM  
    在某个节奏上，浏览器的主线程将运行样式和布局的渲染步骤，并开始尝试视觉表示 DOM 和 CSSOM
2. The Render Tree is updated as a result with positioning and styling information  
    渲染树因此更新，包含定位和样式信息
3. The Paint phase runs. Despite its name, this doesn't draw anything on the screen but identifies graphical regions that need to be drawn and how to draw them. These instructions are transmitted to a separate thread, the _Compositor Thread_.  
    油漆阶段运行。尽管其名称如此，但这并不在屏幕上绘制任何内容，而是识别需要绘制的图形区域以及如何绘制它们。这些指令被传输到另一个线程，即合成线程。
4. On the Compositor Thread, visual regions of the Render Tree are grouped into Layers, forming a Layer Tree  
    在合成线程上，渲染树的视觉区域被分组为层，形成层树
5. The Compositor thread coordinates with a dedicated GPU process and other Raster worker threads draw each Layer in the Layer Tree as textures / bitmaps in GPU memory (this is called Rasterization)  
    合成线程与专用 GPU 进程协调，其他光栅化工作线程将层树中的每一层作为纹理/位图绘制到 GPU 内存中（这称为光栅化）
6. The resulting Layers + pointers to the newly produced bitmaps are spliced together during the Composite phase to produce a Frame generation instruction  
    结果层和指向新产生的位图的指针在合成阶段拼接在一起，以生成帧生成指令
7. The Frame generation instruction is sent to the GPU process and drawn onto the display  
    帧生成指令发送到 GPU 进程并在显示屏上绘制

Below, I visualize some of the key data structures in this proces and their various transformations:  
以下，我可视化了这个过程中的关键数据结构及其各种转换：

![[_posts/browser/渲染/media/42b6bfd5a842aee5377001d9638c691f_MD5.png|"A visualization showing the various data structures ordered as described above."]]

> **Note**: There is a lot of nuance and depth in this complex process! I highly recommend the most recent (and evolving!) Chromium rendering architecture overview, which can be [found here](https://developer.chrome.com/docs/chromium/renderingng-architecture).  
> 注意：这个复杂的过程有很多细微之处和深度！我强烈推荐最新的（并且不断发展的！）Chromium 渲染架构概述，可以在以下链接找到。

## [](https://webperf.tips/tip/layers-and-compositing/#how-are-layers-different-from-z-index)How are Layers different from `z-index`?  
层与 `z-index` 有何不同？

`z-index` is used to signal _stacking context_ to the browser, which signals the order used by the Paint phase to draw visual objects, (usually) within a single layer. For example, multiple visual elements with different `z-index` values will likely be stacked visually and drawn onto _one_ Layer.  
`z-index` 用于向浏览器指示堆叠上下文，这表示绘制视觉对象时使用的顺序，通常在单个层内。例如，具有不同 `z-index` 值的多个视觉元素可能会在视觉上堆叠并绘制到单个层上。

Layers aren't used to order visual elements on-screen. They are lower-level graphics primitive and reside closer to the actual GPU/Rastering pipeline than to CSS styling.  
层不是用来在屏幕上对视觉元素进行排序的。它们是更低级的图形原语，并且位于实际 GPU/光栅化管道比 CSS 样式更接近的位置。

## [](https://webperf.tips/tip/layers-and-compositing/#examples)Examples  示例

### [](https://webperf.tips/tip/layers-and-compositing/#animations)Animations  动画

If authored correctly, CSS animations can be completely offloaded to the compositor thread, allowing a smooth, 60 FPS animation to take place despite any heavy activity on the main thread.  
如果编写正确，CSS 动画可以完全卸载到合成器线程，即使在主线程上有任何重负载，也能实现流畅的 60 FPS 动画。


![[_posts/browser/渲染/media/LayersAndCompositing02-05618cbf0be08d56924929541ab5a675.mp4]]

This is by processing the animation instructions completely on the compositor thread on the in-memory layer. I have [a dedicated tip on this mechanism](https://webperf.tips/tip/animate-on-compositor-thread).  
这是通过在内存层上的合成器线程上完全处理动画指令来实现的。我对这个机制有一个专门的提示。

### [](https://webperf.tips/tip/layers-and-compositing/#scrolling)Scrolling  滚动

Scrolling can be processed entirely by transforming in-memory Layers!  
滚动可以完全通过转换内存中的层来处理！

The browser will process scroll events on the Compositor thread first, translating the in-memory graphical Layer, before letting the Main Thread execute any `scroll` JavaScript event handlers (thereby ensuring a smooth scroll, even if JavaScript were executing).  
浏览器将首先在合成线程上处理滚动事件，将内存中的图形层进行转换，然后再让主线程执行任何 `scroll` JavaScript 事件处理器（从而确保即使 JavaScript 正在执行，滚动也能平滑进行）。

### [](https://webperf.tips/tip/layers-and-compositing/#expensive-frequently-drawn-regions)Expensive, Frequently Drawn Regions  
昂贵、频繁绘制区域

While unusual, certain groups of visual elements may be expensive to draw / re-drawn depending on the scenario (like [in this example with SVGs and OneDrive](https://webperf.tips/tip/onedrive-case-study)).  
虽然不常见，但在某些场景下，某些视觉元素可能因绘制/重新绘制而变得昂贵（如本例中的 SVG 和 OneDrive）。

A web developer can explicitly signal to the browser that they'd like a set of visual elements grouped into a dedicated layer. This can be done with the following line of CSS:  
网页开发者可以明确地向浏览器指示，他们希望将一组视觉元素组合到一个专用层中。这可以通过以下 CSS 行实现：

```css
will-change: transform
```

## [](https://webperf.tips/tip/layers-and-compositing/#exploring-layers-at-runtime)Exploring Layers at Runtime  
探索运行时层

### [](https://webperf.tips/tip/layers-and-compositing/#chrome-layers-view)Chrome Layers View  Chrome 层视图

You can view Layers at runtime using the Chromium F12 DevTools!  
您可以在运行时使用 Chromium F12 开发者工具查看图层！

It can be found here (it's hidden behind a few menus):  
它可以在以下位置找到（隐藏在几个菜单后面）：

1. Right-click, _Inspect_ to open the DevTools  
    右键单击，检查以打开开发者工具
2. Three dot menu in the top-right corner  
    右上角三点菜单
3. _More Tools  更多工具_
4. _Layers  层级_

![[_posts/browser/渲染/media/8910428320635abaff3b71553b8039b3_MD5.png|"A screenshot of the Menus view in Chromium for the Layers tool."]]

### [](https://webperf.tips/tip/layers-and-compositing/#microsoft-edge-3d-view)Microsoft Edge 3D View  微软 Edge 3D 视图

Microsoft Edge has a powerful 3D viewer. Details on this tool can be found [here](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/3d-view/).  
Microsoft Edge 拥有强大的 3D 查看器。有关此工具的详细信息，请在此处查找。

## [](https://webperf.tips/tip/layers-and-compositing/#should-i-optimize-for-layers)Should I optimize for Layers?  
我应该优化层吗？

In general, I would suggest only optimizing for Layers when they become problematic. In most cases, if the HTML is semantic, the DOM is reasonably sized, and the CSS is well designed, the browser will automatically do the right thing for you; Layering issues likely won't become a performance bottleneck.  
一般来说，我建议只有在层出现问题时才对其进行优化。在大多数情况下，如果 HTML 是语义化的，DOM 大小合理，CSS 设计良好，浏览器会自动为你做正确的事情；分层问题不太可能成为性能瓶颈。

However, if during your regular profiling of your web application you identify degradations in Rendering phases or in Layering and Compositing, you now have the tools to understand what's going on behind the scenes!  
然而，如果在您对 Web 应用的常规分析中发现了渲染阶段或分层和合成阶段的退化，您现在有了工具来理解幕后发生了什么！

That's all for this tip! Thanks for reading! _Discover more similar tips matching [Browser Internals](https://webperf.tips/topic/browser-internals)._  
这是本提示的全部内容！感谢阅读！发现更多匹配浏览器内幕的类似提示。
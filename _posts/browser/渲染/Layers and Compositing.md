Web developers typically aren't exposed to low-level browser rendering primitives, yet the concepts of Layers and Compositing are foundational to how the browser renders web applications as pixels on screen! In this tip, I'll explain what Layers are, why they exist, and how they are Composited together to produce on-screen Frames to the user's display.
Web 开发者通常不会接触到低级别的浏览器渲染原语，但*图层（Layers）和合成（Compositing）的概念是浏览器如何将 Web 应用程序渲染为屏幕上像素的基础*！在本技巧中，我将解释什么是图层、它们为何存在，以及它们是如何通过合成（Compositing）组合在一起以生成显示在用户屏幕上的帧（Frames）。
## [](https://webperf.tips/tip/layers-and-compositing/#prerequisites)Prerequisites
先决条件
- You should understand [the basics on the Browser Rendering Pipeling](https://webperf.tips/tip/browser-rendering-pipeline)
  您应该了解浏览器渲染管道的基础知识 。
- You should understand [the basics on the Browser Process Model](https://webperf.tips/tip/browser-process-model)
  您应该了解浏览器进程模型的基础知识 。

## [](https://webperf.tips/tip/layers-and-compositing/#layers)Layers

Layers are groups of visual elements that share the same coordinate plane. They are rendered onto a 2-dimensional mesh and retained in GPU memory. Below is an example of Layers (and we'll dive into detail later):
图层是一组共享相同坐标平面的视觉元素。它们被渲染到一个二维网格中，并保留在 GPU 内存中。以下是图层的一个示例（我们将在后面深入探讨细节）：

![[_posts/browser/渲染/media/35789c95a0dfc29f88019569a0b131c2_MD5.gif]]

All modern web browsers support Layers, yet the exact implementation details may differ and evolve as browsers are continually changing.
所有现代的浏览器都支持图层（Layers），不过，由于浏览器在不断变化，其确切的实现细节可能会有所不同，并且还会不断演进。

> I encourage you to read the Layer source code in Chromium, [found here](https://source.chromium.org/chromium/chromium/src/+/main:cc/layers/layer.h;l=87;drc=69e6dc49684309c8b375c4dcd724c6ae61878ecd)!
   我鼓励你阅读 Chromium 中关于图层的源代码，[在此处可以找到](https://source.chromium.org/chromium/chromium/src/+/main:cc/layers/layer.h;l=87;drc=69e6dc49684309c8b375c4dcd724c6ae61878ecd)！
   
## [](https://webperf.tips/tip/layers-and-compositing/#why-do-layers-exist)Why do Layers exist?
为什么会有图层？

Layers exist as an optimization. The browser will render (draw pixels for) multiple Layers and retain the results in GPU memory for subsequent use. When a Frame needs to be produced (i.e. scrolling, animating), it's faster to transform and compose multiple pre-rendered Layers rather than re-drawing the viewport on demand.
图层的存在是一种*优化手段*。浏览器会渲染（为其绘制像素）多个图层，并*将渲染结果保留在 GPU 内存中*，以便后续使用。*当需要生成一帧画面时（比如在滚动页面、进行动画效果展示时）*，对多个已预先渲染好的图层进行变换和合成，要比根据需求重新绘制视口区域的速度更快。

Furthermore, Layers can be visually updated independently by a separate thread, the _Compositor Thread_ without competition from heavy JavaScript or other activity taking place on the Main Thread.
此外，**图层可以由一个单独的线程 —— 合成线程（_Compositor Thread_）独立地进行视觉更新**，这样就*不会与主线程上运行的占用大量资源的 JavaScript 代码或其他活动产生冲突*。

This is a tradeoff, where GPU memory is traded to optimize rendering performance, but generally the tradeoff pays significant dividends for end user experience.
这是一种权衡，*用 GPU 内存来换取渲染性能的优化*，但总的来说，这种权衡能为终端用户的体验带来显著的好处。

## [](https://webperf.tips/tip/layers-and-compositing/#when-are-layers-created)When are Layers created?
图层在什么时候创建？

The conditions for when a Layer is created will vary by browser implementation. Common examples where the browse will create Layers include:
图层创建的条件会因浏览器的实现方式而异。浏览器通常会在以下这些情况下创建图层：

- Scrollable areas - 可滚动区域
- Regions driven by CSS animations - 由 CSS 动画驱动的区域
- Videos rendered through `<video>` - 通过 `<video>` 标签渲染的视频
- Canvas elements through `<canvas>` - 通过 `<canvas>` 标签创建的画布元素
- Regions explicitly promoted by CSS via `will-change: transform` - 通过 CSS 的 `will-change: transform` 属性显式提升的区域

In Chromium, [here is the list of conditions](https://source.chromium.org/chromium/chromium/src/+/refs/heads/main:third_party/blink/renderer/platform/graphics/compositing_reasons.h;l=18;drc=4e8e81f6eeb6969973f3ec97132d80339b92d227) that promote a group of visual elements to dedicated layer.
在 Chromium 中，[这里列出了](https://source.chromium.org/chromium/chromium/src/+/refs/heads/main:third_party/blink/renderer/platform/graphics/compositing_reasons.h;l=18;drc=4e8e81f6eeb6969973f3ec97132d80339b92d227) 将一组视觉元素提升为专用图层的条件。

## [](https://webperf.tips/tip/layers-and-compositing/#how-do-layers-operate-during-runtime)How do Layers operate during Runtime?
图层在运行时是如何工作的？

Consider the following diagram:
请参考以下图表：

![[_posts/browser/渲染/media/28d7e8385c8ef51cfae1bb5c43f7f120_MD5.png|"A diagram showing the Compositor Thread, Raster Threads, and Main Thread coordinating the steps described below."]]
			
			该图表展示了合成线程、光栅化线程、主线程如何协调以下所述的步骤

Each browser operates differently, but at a high level, it works like this:
每个浏览器的工作方式都有所不同，但大致的工作原理如下：

1. At some cadence, the Browser's Main Thread will run the [Rendering Steps of Style and Layout](https://webperf.tips/tip/browser-rendering-pipeline), and begin trying to visually represent the DOM and CSSOM
	1. 浏览器的主线程会按照一定的节奏运行 [样式与布局的渲染步骤](https://webperf.tips/tip/browser-rendering-pipeline)，并开始尝试以可视化的方式呈现 DOM（文档对象模型）和 CSSOM（CSS 对象模型）。
2. The Render Tree is updated as a result with positioning and styling information
	2. 作为结果，渲染树会随着位置和样式信息的更新而更新。
3. The Paint phase runs. Despite its name, this doesn't draw anything on the screen but identifies graphical regions that need to be drawn and how to draw them. These instructions are transmitted to a separate thread, the _Compositor Thread_.
	3. 进入绘制阶段。*尽管名为 “绘制”，但在此阶段并不会在屏幕上绘制任何内容，而是识别出需要绘制的图形区域以及绘制的方法*。***这些指令**会被传输到一个单独的线程*，即`合成线程`。
4. On the Compositor Thread, visual regions of the Render Tree are grouped into Layers, forming a Layer Tree
	4. 在合成线程上，渲染树的可视区域会被分组到各个图层中，从而形成一个图层树。
5. The Compositor thread coordinates with a dedicated GPU process and other `Raster worker threads` draw each Layer in the Layer Tree as textures / bitmaps in GPU memory (this is called `Rasterization`)
	5. 合成线程会与一个专用的 GPU 进程进行协调，其他`光栅化工作线程`会将图层树中的每个图层绘制为 GPU 内存中的纹理或位图（这一过程称为`光栅化`）。
6. The resulting Layers + pointers to the newly produced bitmaps are spliced together during the Composite phase to produce a Frame generation instruction
	6. 在合成阶段，生成的图层以及指向新生成的位图的指针会被拼接在一起，从而生成一帧画面的生成指令。
7. The Frame generation instruction is sent to the GPU process and drawn onto the display
	7. 这一帧画面的生成指令会被发送到 GPU 进程，并绘制到显示屏上。

Below, I visualize some of the key data structures in this proces and their various transformations:
下面，我将展示这一过程中的一些关键数据结构以及它们的各种转换情况：

![[_posts/browser/渲染/media/42b6bfd5a842aee5377001d9638c691f_MD5.png|"A visualization showing the various data structures ordered as described above."]]

> **Note**: There is a lot of nuance and depth in this complex process! I highly recommend the most recent (and evolving!) Chromium rendering architecture overview, which can be [found here](https://developer.chrome.com/docs/chromium/renderingng-architecture).
> **注意**：这个复杂的过程中有很多微妙且深入的细节！我强烈推荐阅读最新的（且仍在不断发展的）Chromium 渲染架构概述，[可在此处找到](https://developer.chrome.com/docs/chromium/renderingng-architecture)。

## [](https://webperf.tips/tip/layers-and-compositing/#how-are-layers-different-from-z-index)How are Layers different from `z-index`?
图层与 `z-index` 有何不同？

`z-index` is used to signal _stacking context_ to the browser, which signals the order used by the Paint phase to draw visual objects, (usually) within a single layer. For example, multiple visual elements with different `z-index` values will likely be stacked visually and drawn onto _one_ Layer.
`z-index` 用于向浏览器指示**堆叠上下文**，它决定了在绘制阶段（通常情况下）在单个图层内绘制可视化对象的顺序。例如，多个具有不同 `z-index` 值的可视化元素可能会在视觉上进行堆叠，并绘制在**一个**图层上。

Layers aren't used to order visual elements on-screen. They are lower-level graphics primitive and reside closer to the actual GPU/Rastering pipeline than to CSS styling.
图层并非用于对屏幕上的可视化元素进行排序。它们是更低级别的图形基元，相比于 CSS 样式，它们更接近实际的 GPU（图形处理器）/ 光栅化流水线。

## [](https://webperf.tips/tip/layers-and-compositing/#examples)Examples

### [](https://webperf.tips/tip/layers-and-compositing/#animations)Animations
动画

If authored correctly, CSS animations can be completely offloaded to the compositor thread, allowing a smooth, 60 FPS animation to take place despite any heavy activity on the main thread.
如果编写正确，*CSS 动画可以完全交给合成线程处理，这样即便主线程上有任何繁重的任务，也能实现流畅的、每秒 60 帧的动画效果*。

![[_posts/browser/渲染/media/LayersAndCompositing02-05618cbf0be08d56924929541ab5a675.mp4]]


This is by processing the animation instructions completely on the compositor thread on the in-memory layer. I have [a dedicated tip on this mechanism](https://webperf.tips/tip/animate-on-compositor-thread).
这是通过在内存中的图层上由合成线程完全处理动画指令来实现的。我有一篇关于这种机制的[专门的技巧文章](https://webperf.tips/tip/animate-on-compositor-thread)。

### [](https://webperf.tips/tip/layers-and-compositing/#scrolling)Scrolling
滚动

Scrolling can be processed entirely by transforming in-memory Layers!
滚动操作可以完全通过对内存中的图层进行变换来处理！

The browser will process scroll events on the Compositor thread first, translating the in-memory graphical Layer, before letting the Main Thread execute any `scroll` JavaScript event handlers (thereby ensuring a smooth scroll, even if JavaScript were executing).
浏览器会首先*在合成线程上处理滚动事件*，对内存中的图形图层进行平移，然后才会让主线程执行任何 `scroll` 相关的 JavaScript 事件处理程序（这样即使 JavaScript 正在执行，也能确保滚动流畅）。

### [](https://webperf.tips/tip/layers-and-compositing/#expensive-frequently-drawn-regions)Expensive, Frequently Drawn Regions
绘制成本高且需频繁绘制的区域

While unusual, certain groups of visual elements may be expensive to draw / re-drawn depending on the scenario (like [in this example with SVGs and OneDrive](https://webperf.tips/tip/onedrive-case-study)).
虽然不常见，但根据具体情况，某些可视化元素组的绘制或重新绘制成本可能较高（就像[这个关于 SVG 和 OneDrive 的示例](https://webperf.tips/tip/onedrive-case-study) 那样）。

A web developer can explicitly signal to the browser that they'd like a set of visual elements grouped into a dedicated layer. This can be done with the following line of CSS:
Web 开发者可以*明确地向浏览器发出信号，表示希望将一组可视化元素归为一个专用图层*。这可以通过以下这行 CSS 代码来实现：

```css
will-change: transform
```

## [](https://webperf.tips/tip/layers-and-compositing/#exploring-layers-at-runtime)Exploring Layers at Runtime


### [](https://webperf.tips/tip/layers-and-compositing/#chrome-layers-view)Chrome Layers View

You can view Layers at runtime using the Chromium F12 DevTools!

It can be found here (it's hidden behind a few menus):

1. Right-click, _Inspect_ to open the DevTools
2. Three dot menu in the top-right corner
3. _More Tools_
4. _Layers_

![[_posts/browser/渲染/media/8910428320635abaff3b71553b8039b3_MD5.png|"A screenshot of the Menus view in Chromium for the Layers tool."]]

### [](https://webperf.tips/tip/layers-and-compositing/#microsoft-edge-3d-view)Microsoft Edge 3D View

Microsoft Edge has a powerful 3D viewer. Details on this tool can be found [here](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/3d-view/).

## [](https://webperf.tips/tip/layers-and-compositing/#should-i-optimize-for-layers)Should I optimize for Layers?
我是否应该针对图层进行优化呢？

In general, I would suggest only optimizing for Layers when they become problematic. In most cases, if the HTML is semantic, the DOM is reasonably sized, and the CSS is well designed, the browser will automatically do the right thing for you; Layering issues likely won't become a performance bottleneck.
一般来说，我建议*只有当图层出现问题时才对其进行优化*。在大多数情况下，如果 HTML 具有语义性，DOM 的规模合理，并且 CSS 设计良好，浏览器会自动为你做出正确的处理；图层相关的问题很可能不会成为性能瓶颈。

However, if during your regular profiling of your web application you identify degradations in Rendering phases or in Layering and Compositing, you now have the tools to understand what's going on behind the scenes!
然而，如果在对 Web 应用程序进行常规性能分析时，你发现渲染阶段或图层与合成方面出现了性能下降的情况，那么你现在就有办法去了解幕后发生了什么！

That's all for this tip! Thanks for reading! _Discover more similar tips matching [Browser Internals](https://webperf.tips/topic/browser-internals)._
以上就是这个技巧的全部内容啦！感谢阅读！ _发现更多与 [浏览器内部原理](https://webperf.tips/topic/browser-internals) 相关的类似技巧。_

## 相关资料
- [Layers and Compositing](https://webperf.tips/tip/layers-and-compositing/)
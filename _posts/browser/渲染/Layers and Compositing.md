Web developers typically aren't exposed to low-level browser rendering primitives, yet the concepts of Layers and Compositing are foundational to how the browser renders web applications as pixels on screen! In this tip, I'll explain what Layers are, why they exist, and how they are Composited together to produce on-screen Frames to the user's display.
Web 开发者通常不会接触到低级别的浏览器渲染原语，但图层（Layers）和合成（Compositing）的概念是浏览器如何将 Web 应用程序渲染为屏幕上像素的基础！在本技巧中，我将解释什么是图层、它们为何存在，以及它们是如何通过合成（Compositing）组合在一起以生成显示在用户屏幕上的帧（Frames）。
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

> I encourage you to read the Layer source code in Chromium, [found here](https://source.chromium.org/chromium/chromium/src/+/main:cc/layers/layer.h;l=87;drc=69e6dc49684309c8b375c4dcd724c6ae61878ecd)!

## [](https://webperf.tips/tip/layers-and-compositing/#why-do-layers-exist)Why do Layers exist?

Layers exist as an optimization. The browser will render (draw pixels for) multiple Layers and retain the results in GPU memory for subsequent use. When a Frame needs to be produced (i.e. scrolling, animating), it's faster to transform and compose multiple pre-rendered Layers rather than re-drawing the viewport on demand.

Furthermore, Layers can be visually updated independently by a separate thread, the _Compositor Thread_ without competition from heavy JavaScript or other activity taking place on the Main Thread.

This is a tradeoff, where GPU memory is traded to optimize rendering performance, but generally the tradeoff pays significant dividends for end user experience.

## [](https://webperf.tips/tip/layers-and-compositing/#when-are-layers-created)When are Layers created?

The conditions for when a Layer is created will vary by browser implementation. Common examples where the browse will create Layers include:

- Scrollable areas
- Regions driven by CSS animations
- Videos rendered through `<video>`
- Canvas elements through `<canvas>`
- Regions explicitly promoted by CSS via `will-change: transform`

In Chromium, [here is the list of conditions](https://source.chromium.org/chromium/chromium/src/+/refs/heads/main:third_party/blink/renderer/platform/graphics/compositing_reasons.h;l=18;drc=4e8e81f6eeb6969973f3ec97132d80339b92d227) that promote a group of visual elements to dedicated layer.

## [](https://webperf.tips/tip/layers-and-compositing/#how-do-layers-operate-during-runtime)How do Layers operate during Runtime?

Consider the following diagram:

![[_posts/browser/渲染/media/28d7e8385c8ef51cfae1bb5c43f7f120_MD5.png|"A diagram showing the Compositor Thread, Raster Threads, and Main Thread coordinating the steps described below."]]

Each browser operates differently, but at a high level, it works like this:

1. At some cadence, the Browser's Main Thread will run the [Rendering Steps of Style and Layout](https://webperf.tips/tip/browser-rendering-pipeline), and begin trying to visually represent the DOM and CSSOM
2. The Render Tree is updated as a result with positioning and styling information
3. The Paint phase runs. Despite its name, this doesn't draw anything on the screen but identifies graphical regions that need to be drawn and how to draw them. These instructions are transmitted to a separate thread, the _Compositor Thread_.
4. On the Compositor Thread, visual regions of the Render Tree are grouped into Layers, forming a Layer Tree
5. The Compositor thread coordinates with a dedicated GPU process and other Raster worker threads draw each Layer in the Layer Tree as textures / bitmaps in GPU memory (this is called Rasterization)
6. The resulting Layers + pointers to the newly produced bitmaps are spliced together during the Composite phase to produce a Frame generation instruction
7. The Frame generation instruction is sent to the GPU process and drawn onto the display

Below, I visualize some of the key data structures in this proces and their various transformations:

![[_posts/browser/渲染/media/42b6bfd5a842aee5377001d9638c691f_MD5.png|"A visualization showing the various data structures ordered as described above."]]

> **Note**: There is a lot of nuance and depth in this complex process! I highly recommend the most recent (and evolving!) Chromium rendering architecture overview, which can be [found here](https://developer.chrome.com/docs/chromium/renderingng-architecture).

## [](https://webperf.tips/tip/layers-and-compositing/#how-are-layers-different-from-z-index)How are Layers different from `z-index`?

`z-index` is used to signal _stacking context_ to the browser, which signals the order used by the Paint phase to draw visual objects, (usually) within a single layer. For example, multiple visual elements with different `z-index` values will likely be stacked visually and drawn onto _one_ Layer.

Layers aren't used to order visual elements on-screen. They are lower-level graphics primitive and reside closer to the actual GPU/Rastering pipeline than to CSS styling.

## [](https://webperf.tips/tip/layers-and-compositing/#examples)Examples

### [](https://webperf.tips/tip/layers-and-compositing/#animations)Animations

If authored correctly, CSS animations can be completely offloaded to the compositor thread, allowing a smooth, 60 FPS animation to take place despite any heavy activity on the main thread.

This is by processing the animation instructions completely on the compositor thread on the in-memory layer. I have [a dedicated tip on this mechanism](https://webperf.tips/tip/animate-on-compositor-thread).

### [](https://webperf.tips/tip/layers-and-compositing/#scrolling)Scrolling

Scrolling can be processed entirely by transforming in-memory Layers!

The browser will process scroll events on the Compositor thread first, translating the in-memory graphical Layer, before letting the Main Thread execute any `scroll` JavaScript event handlers (thereby ensuring a smooth scroll, even if JavaScript were executing).

### [](https://webperf.tips/tip/layers-and-compositing/#expensive-frequently-drawn-regions)Expensive, Frequently Drawn Regions

While unusual, certain groups of visual elements may be expensive to draw / re-drawn depending on the scenario (like [in this example with SVGs and OneDrive](https://webperf.tips/tip/onedrive-case-study)).

A web developer can explicitly signal to the browser that they'd like a set of visual elements grouped into a dedicated layer. This can be done with the following line of CSS:

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

In general, I would suggest only optimizing for Layers when they become problematic. In most cases, if the HTML is semantic, the DOM is reasonably sized, and the CSS is well designed, the browser will automatically do the right thing for you; Layering issues likely won't become a performance bottleneck.

However, if during your regular profiling of your web application you identify degradations in Rendering phases or in Layering and Compositing, you now have the tools to understand what's going on behind the scenes!

That's all for this tip! Thanks for reading! _Discover more similar tips matching [Browser Internals](https://webperf.tips/topic/browser-internals)._
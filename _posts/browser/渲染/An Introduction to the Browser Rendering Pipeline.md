Users love pixels (frames) delivered on screen as fast as possible; that's what makes a web application feel _fast_!  
用户喜欢尽可能快地在屏幕上显示像素（帧）;这就是Web应用程序感觉速度快的原因！

HTML and CSS are the foundational building blocks web developers use to build visual experiences on the web. But how does the browser convert HTML and CSS into pixels?  
HTML和CSS是Web开发人员用来在Web上构建视觉体验的基本构建块。但是，浏览器如何将HTML和CSS转换为像素？

The browser orchestrated transformation process of HTML and CSS into pixels is fairly opaque, and as a result, most web developers don't consider how or when it runs.  
浏览器编排的HTML和CSS到像素的转换过程是相当不透明的，因此，大多数Web开发人员不考虑如何或何时运行。

Understanding this process is key to building high performance web applications, and in this tip, I'll help demystify it.  
理解这个过程是构建高性能Web应用程序的关键，在本技巧中，我将帮助您揭开这个过程的神秘面纱。

## [](https://webperf.tips/tip/browser-rendering-pipeline/#the-overall-process)The Overall Process  总体过程

The journey from raw HTML and CSS text all the way to visual on-screen pixels has many steps and intermediate states along the way. For this beginner-friendly tip, we'll discuss the following:  
从RAW HTML和CSS文本一直到视觉上的屏幕像素的旅程具有许多步骤和中间状态。对于此初学者友好的提示，我们将讨论以下内容：

![[_posts/browser/渲染/media/5bdf702c27ece50de3ea6dfa12986e80_MD5.png|"A diagram showing HTML Text input to the DOM Tree, CSS Text becoming the CSSOM. CSSOM and DOM become the Styled Render Tree and the styled Render tree gets positioned via Layout. Finally, Pixels are drawn via Paint"]]

## [](https://webperf.tips/tip/browser-rendering-pipeline/#html-parsing)HTML Parsing 

When a user navigates to a web page, the entry to the application is the HTML document served to the user as a text file.  
当用户导航到网页时，*应用程序的入口*是作为文本文件提供给用户的HTML文档。

The browser utilizes its [HTML Parser](https://webperf.tips/tip/html-parser) to convert this HTML text into the **Document Object Model**, or **DOM**.  
*浏览器利用其HTML解析器**将此HTML文本转换为文档对象模型**（DOM）*。

The DOM is a tree that defines the structure of the document. Each node in the DOM tree is a browser object that maps back to items specified in the HTML text file.  
*DOM是一棵树，它定义了文档的结构*。DOM树中的每个节点都是一个浏览器对象，它映射回HTML文本文件中指定的项。

For example, consider the following HTML text:  
例如，考虑以下HTML文本：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Example Document</title>
    <link rel="stylesheet" href="styles.css" />
</head>

<body>
    <div>Example Div 1</div>
    <div>Example Div 2</div>
    <div>Example Div 3</div>
</body>
</html>
```

Once the HTML parser finishes its job, the following DOM tree would be produced:  
一旦HTML解析器完成工作，就会产生以下DOM树：

![[_posts/browser/渲染/media/e4a41423807f02d2eb025020100dea4e_MD5.png|"The produced DOM Tree from the HTML text above."]]

Parsing HTML into the DOM takes place as a [Task](https://webperf.tips/tip/event-loop) on the Main Thread. It is presented visually in [a trace](https://webperf.tips/tip/collect-a-trace) like this:  
*将HTML解析为DOM是作为**主线程上的任务**进行的*。它在视觉上呈现为这样的轨迹：

![[_posts/browser/渲染/media/ba9ae733f355d9745d556ecf7edb067e_MD5.png|"The DOMParser running as a Task on the Main Thread in the Profiler."]]

## [](https://webperf.tips/tip/browser-rendering-pipeline/#css-object-model)CSS Object Model  

While HTML defines the structure of the document, it may also reference CSS Stylesheets to define the visual presentation of the document.  
虽然HTML定义了文档的结构，但它也可以引用CSS样式表来定义文档的视觉表示。

These stylesheets are are typically defined via `<link rel="stylesheet" />` tags, but may also be defined through inline `<style>` nodes.  
这些样式表通常通过 `<link rel="stylesheet" />` 标记定义，但也可以通过内联节点定义

A CSS stylesheet defines _rules_ composed of _selectors_ and _declarations_. For example, consider `styles.css`:  
CSS样式表定义了由*选择器*和*声明*组成的*规则*。例如，考虑styles.css：

```css
div {
    background-color: red;
}
```

In this example, `div` is a selector and `background-color: red` is a declaration. The entire block is considered a rule:  
在这个例子中，div是一个选择器，background-color：red是一个声明。整个块被视为一条规则：

![[_posts/browser/渲染/media/3c25b5e8a506c986772b0d43118f156a_MD5.png|"A diagram highlighting the differences between a rule, selector, and declaration."]]

A web application may reference many stylesheets, and a stylesheet may define many rules (often with many declarations!).  
*一个Web应用程序可能引用许多样式表，一个样式表可能定义许多规则*（通常有许多声明！）。

The browser will parse these stylesheets into a memory-efficient, lookup-efficient data structure, called the **CSS Object Model**, or **CSSOM**. Its primary purpose is to aggregate rules and provide an efficient lookup to match selectors to their declared styles.  
**浏览器将把这些样式表解析成一个*内存效率高*、*查找效率高*的*数据结构***，称为**CSS对象模型**，或**CSSOM**。它的**主要目的**是*聚合规则*，并提供一个有效的查找来匹配选择器和它们声明的样式。

![[_posts/browser/渲染/media/ed44d8a246cfc6279ac8ccd68697beaf_MD5.png|"A diagram showing CSS transforming from Text to CSSOM Aggregation"]]

Downloaded text stylesheets get parsed and aggregated into the CSSOM on the Main Thread within a [Task](https://webperf.tips/tip/event-loop). This will manifest as a _Parse Stylesheet_ Task in [a trace](https://webperf.tips/tip/collect-a-trace):  
下载的文本样式表被解析并聚合到任务内**主线程上**的CSSOM中。这将在跟踪中表现为解析样式表任务：

![[_posts/browser/渲染/media/c28cf6c3498f914fa9ee327dffa9255b_MD5.png|"A screenshot of the Chromium F12 Profiler showing a Parse Stylesheet task"]]

> **Note:** The CSSOM's exact backing structure is browser-specific and worth a dedicated article on its own. While I won't cover it in this tip, you can imagine it's a memory-efficient tree optimized for fast lookup. You can explore the [StyleEngine source code](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/css/style_engine.cc;drc=ebf6be4eaa77a47b813aece108e06523a3f7ddde) for in-depth details.  
注意：CSSOM的确切支持结构是特定于浏览器的，值得专门撰写一篇文章。虽然我不会在这篇技巧文章中介绍它，但您可以想象它是一个为快速查找而优化的内存高效树。您可以浏览StyleEngine源代码以获得更深入的详细信息。

## [](https://webperf.tips/tip/browser-rendering-pipeline/#style-and-the-render-tree)Style and The Render Tree  
样式和渲染树

Let's take a moment to examine the overall process flowchart:  
让我们花一点时间检查整个过程流程图：

![[_posts/browser/渲染/media/c29f39b76ce8325b1e4b25e01411dc60_MD5.png|"A diagram showing the overall browser rendering phases, with Style phase circled."]]

Once the DOM and CSSOM are constructed, the browser can begin the next phase of the pipeline: _Style_. This phase is sometimes called _Recalculate Style_ or a _Render Tree Update_.  
一旦DOM和CSSOM构造完成，浏览器就可以开始管道的下一个阶段：*样式*。此阶段有时称为“*重新计算样式*”或“*渲染树更新*”。

### [](https://webperf.tips/tip/browser-rendering-pipeline/#the-render-tree)The Render Tree  渲染树

The **Render Tree** (sometimes called the **Layout Tree**) is a browser-internal C++ data structure that web developers don't directly modify.  
**渲染树**（有时称为**布局树**）是一种*浏览器内部的C++数据结构*，Web开发人员不会直接修改。

It is a separate tree from the DOM but often mirrors its structure. Each node typically references a _DOM node_ and a _Computed Style_. It's essentially composed of every DOM node that should be presented visually on the user's screen.  
*它是一个独立于DOM的树*，但通常反映其结构。**每个节点通常引用一个DOM节点和一个被计算的样式**。它基本上由每个DOM节点组成，这些节点应该在用户的屏幕上以可视方式呈现。

Consider this simplified Render Tree node, called a `RenderObject`:  
考虑此简化的渲染树节点（称为`渲染对象`）：

```cpp
class RenderObject {
    private:
        // Reference to the DOM Node being represented
        DOMNode* domNode;

        // Reference to the CSS Styles resolved for this DOM node.
        ComputedStyle* style
        
        // RenderTree structural pointers
        RenderObject* parent;
        RenderObjectList* children;
}
```

![[_posts/browser/渲染/media/dac5a369af6587747b983bbbc1269bb8_MD5.png|"A diagram showing a RenderObject referencing a DOMNode and a ComputedStyle"]]

The Render Tree is also responsible for other, non-DOM related visual elements, such as scroll bars and text selection.  
渲染树还负责其他与DOM无关的可视元素，如滚动条和文本选择。

> Consider reading through Chromium's [LayoutTree implementation](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/layout/layout_object.h;l=184-279;drc=f7f2dcfbd24f7ee74a0b306043bc757da65f64a6) for more in-depth details on this data structure.  
考虑阅读Chromium的LayoutTree实现，以获得关于此数据结构的更深入的细节。
>**Note:** Notable exclusions from the Render Tree include `<head>` and its children, anything that is marked as `display: none`, and `<script>` elements because they are not presented to the user's screen.  
注意：渲染树中不包括 `<head>` 及其子项、 `display: none`元素、`<script>`元素，因为它们没有呈现在用户的屏幕上。

### [](https://webperf.tips/tip/browser-rendering-pipeline/#computedstyle)ComputedStyle 

A ComputedStyle is effectively the list of CSS declarations that apply to that DOM node, considering the DOM node's selector, CSS specificity, and the aggregated rules in the CSSOM.  
ComputedStyle实际上是应用于该DOM节点的CSS声明的列表，考虑到DOM节点的选择器、CSS特性和CSSOM中的聚合规则。

For example, if I have an example HTML Element:  
例如，如果我有一个示例HTML元素：

```html
<div class="center-text">Example</div>
```

and styles defined like this:  
样式定义如下：

```css
.center-text {
    text-align: center;
}

div {
    background-color: red;
}
```

The ComputedStyle for my element would be constructed via:  
我的元素的ComputedStyle将通过以下方式构造：

1. Querying the CSS selectors against the aggregated rules in the CSSOM for the `div` element to get the applicable rules  
    根据CSSOM中`div`元素的聚合规则查询CSS选择器，以获取适用的规则
2. Resolving any CSS specificity conflicts to the final set of declarations applied, in this case:  
    解决与应用的最终声明集的任何CSS特定性冲突，在这种情况下：
    - `text-align: center`  
    - `background-color: red`  
    - any of the default styles defined by the browser  
        浏览器定义的任何默认样式

>Consider reading through Chromium's [ComputedStyle implementation](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/style/computed_style.h;l=153-207;drc=f7f2dcfbd24f7ee74a0b306043bc757da65f64a6) and [StyleResolver](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/css/resolver/style_resolver.cc;drc=42112cd2b5164c7410121180d173556d9c03ffdb) for more in-depth details on this process.  
> 考虑通过Chromium的计算机实现和Styleresolver阅读以了解此过程的更深入的详细信息。

### [](https://webperf.tips/tip/browser-rendering-pipeline/#tree-construction)Tree Construction  树建造

To build the Render Tree, the browser will:  
为了构建渲染树，浏览器将：

1. Recursively traverse the DOM, searching for visual elements  
    递归遍历DOM，搜索视觉元素
2. Construct / update the Render Tree node pointing back to the DOM node  
    构造 /更新指向DOM节点的渲染树节点
3. Derive ComputedStyles for that DOM node, and associate with the DOM node and Render Tree node  
    得出该DOM节点的计算施法，并与DOM节点和渲染树节点相关联

In the end, we end up with a styled Render Tree of visual elements to present the user:  
最后，我们最终得到了风格的视觉元素树，以呈现用户：

![[_posts/browser/渲染/media/36eabd04746aa3e1381296ddc2df8284_MD5.png|"A diagram showing the Render Tree referencing back its DOM Nodes"]]

> If this diagram is too small to read (I tried to include everything 😅), try to right-click and open in new tab.  
> 如果此图太小而无法阅读（我尝试包含所有内容😅），请尝试右键单击并在新标签中打开。

In the Chromium Profiler, this will appear as a _Recalculate Style_ task:  
在Chromium Profiler中，这将是_重新计算的样式_任务：

![[_posts/browser/渲染/media/0ef96f53c36129b21b8913785b52874c_MD5.png|"A screenshot of the Chromium Profiler referencing Recalculate Style"]]

## [](https://webperf.tips/tip/browser-rendering-pipeline/#layout)Layout  布局

Although the Render Tree contains all the CSS declarations for widths, heights, colors, etc. for each visual element on the page, the browser has not assigned any geometry or coordinates to the elements.  
尽管渲染树包含有关宽度，高度，颜色等的所有CSS声明。对于页面上的每个视觉元素，浏览器尚未将任何几何形状或坐标分配给元素。

The **Layout** process (sometimes called **Reflow**) recursively traverses the newly constructed / updated Render Tree, and assigns each node precise floating-point positions and geometry.  
布局过程（有时称为反流）会递归地穿越新构造 /更新的渲染树，并分配每个节点精确的浮点位置和几何形状。

Layout is a very deep and complex topic. For the purposes of this tip, what's important to know is that Layout will create and position boxes for each node in the Render Tree.  
布局是一个非常深刻而复杂的话题。出于本提示的目的，重要的是要知道的是，布局将为渲染树中的每个节点创建和定位框。

For example, in our example Render Tree:  
例如，在我们的示例中渲染树：

1. The browser creates and assigns a box for the `body` element. Its width is the full width of the screen, because it's a `block`  
    浏览器为`body`元素创建并分配一个框。它的宽度是屏幕的完整宽度，因为它是一个`block`
2. To get the height, the browser traverses to the `body` element's children (the three `div` elements, also `block` boxes)  
    为了达到高度，浏览器遍历`body`元素的孩子（三个`div`元素，也`block`盒子）
3. The height of each of these `div` `block`s is derived from their child, the `TextNode`  
    这些`div` `block` s的高度源自他们的孩子， `TextNode`
4. The heights are aggregated recursively up, and precise coordinates and heights are assigned  
    高度递归地汇总，并分配精确的坐标和高度

![[_posts/browser/渲染/media/127a4da02f420348a485ca20d9444355_MD5.png|"A screenshot of the body element's height."]]

![[_posts/browser/渲染/media/538c9066d13e1fe7ca5ccbd9c4d44d33_MD5.png|"A screenshot of the div element's height."]]

This very cool video shows the browser assigning geometry recursively through the Layout process:  
这个非常酷的视频显示了浏览器通过布局过程递归分配的几何形状：

One thing to note here is that the Layout process can be quite expensive, so the browser uses extensive caching to avoid re-computing Layout unnecessarily.  
这里要注意的一件事是，布局过程可能非常昂贵，因此浏览器使用大量的缓存来避免不必要地重新计算布局。

Layout will typically appear in the Profiler during the Rendering phase, like this:  
在渲染阶段，布局通常会出现在探测器中，因此：

![[_posts/browser/渲染/media/c203791cd4b6a5288896d5491ca6091f_MD5.png|"A screenshot of the Chromium Profiler highlighting Layout."]]

In some cases, if you [force a synchronous reflow](https://webperf.tips/tip/layout-thrashing), it's possible that you will see Layout appear within a JavaScript task:  
在某些情况下，如果强制同步回流，则可能会在JavaScript任务中看到布局出现：

![[_posts/browser/渲染/media/d3f2de08c258881c090b841380ae3f85_MD5.png|"A screenshot of the Chromium Profiler highlighting a forced reflow."]]

## [](https://webperf.tips/tip/browser-rendering-pipeline/#paint)Paint  画

Let's take a look at our overall process flowchart (we're almost there!):  
让我们看一下我们的整体流程流程图（我们几乎在那里！）：

![[_posts/browser/渲染/media/8952652faa265fe22eeb026ae5dfa48b_MD5.png|"The overall browser rendering process, with Paint highlighted."]]

Once we have a styled, positioned set of Render Tree nodes, the browser utilizes a computational graphics library to draw the Render Tree nodes programmatically as pixels.  
一旦我们拥有了样式的，定位的渲染树节点集，浏览器就会利用计算图形库将渲染树节点编程为像素。

This process is quite nuanced, but, from a high level, the browser traverses the newly positioned Render Tree recursively, and executes instructions to draw each Render Tree node.  
这个过程非常细微，但是从高级别来看，浏览器会递归递归渲染树，并执行指令绘制每个渲染树节点。

This phases is responsible for making sure each visual element is painted in the correct order (i.e., resolving `z-index`, scroll containers, etc.).  
该相位负责确保每个视觉元素按正确的顺序（即解决z索引，滚动容器等）绘制。

Chromium utilizes the [Skia library](https://skia.org/) to facilitate drawing, and Skia will interface with the GPU for lower-level OpenGL / DirectX graphics instructions.  
Chromium利用Skia库来促进绘画，Skia将与GPU进行低级OpenGL / DirectX图形指令进行连接。

Once textures are produced from the GPU, the browser aggregates them into a Frame, and the Frame is submitted to the user's display!  
一旦从GPU产生纹理后，浏览器将它们汇总到框架中，并将框架提交给用户的显示器！

Paint is unique in that the work spans multiple threads and processes to complete, but in general it'll manifest in the Chromium Profiler like this:  
油漆是独一无二的，因为工作涵盖了多个线程和过程要完成，但总的来说，它会在这样的铬剖面中表现出来：

![[_posts/browser/渲染/media/48ff9590df8b6f0ed0ef7e14c72d54b4_MD5.png|"A screenshot of the Chromium Profiler highlighting Paint."]]

> **Note:** I am intentionally leaving off the advanced details of Layers and Compositing. Consider reading more about those in my [Layers and Compositing](https://webperf.tips/tip/layers-and-compositing) tip.  
> 注意：我有意删除层和合成的高级细节。考虑阅读更多有关我的层中的内容和组合提示。

## [](https://webperf.tips/tip/browser-rendering-pipeline/#when-does-rendering-run)When does Rendering Run?  渲染何时运行？

We've described _how_ the browser renders, but _when_ does it run?  
我们已经描述了浏览器的渲染_方式_，但是它_何时_运行？

For this answer, read my tip on [the browser event loop](https://webperf.tips/tip/event-loop).  
为此，请阅读我在[浏览器事件循环](https://webperf.tips/tip/event-loop)中的提示。

## [](https://webperf.tips/tip/browser-rendering-pipeline/#conclusion)Conclusion  结论

As web developers, it's important to understand that our favorite CSS, HTML, React components, etc. cannot present themselves visually to the user until the browser completes these phases.  
作为Web开发人员，重要的是要了解我们最喜欢的CSS，HTML，React组件等，直到浏览器完成这些阶段之前，才能直观地向用户展示自己。

Consider [how to measure frame paint time](https://webperf.tips/tip/measuring-paint-time) next, or go deeper into Browser Rendering with [Layers and Compositing](https://webperf.tips/tip/layers-and-compositing).  
考虑接下来[如何测量框架涂料时间](https://webperf.tips/tip/measuring-paint-time)，或更深入地进入浏览[器和合成的](https://webperf.tips/tip/layers-and-compositing)浏览器渲染。

That's all for this tip! Thanks for reading! _Discover more similar tips matching [Browser Internals](https://webperf.tips/topic/browser-internals)._  
这就是这个技巧！感谢您的阅读！_发现更多类似的技巧匹配[浏览器内部](https://webperf.tips/topic/browser-internals)。_

## 相关资料
- [An Introduction to the Browser Rendering Pipeline](https://webperf.tips/tip/browser-rendering-pipeline/)
- [【前端性能】浏览器渲染管道](https://www.bilibili.com/video/BV1zQ2zYeEjh/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)

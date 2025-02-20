# A Basic Overview of the Chromium F12 Performance Profiler
Chromium F12 性能分析器的基本概述

The Chromium Performance Profiler's UI can appear overwhelming at first, but it is actually quite intuitive once you understand how it's setup.  
铬性能分析器的 UI 一开始可能看起来令人眼花缭乱，但实际上一旦你了解了它的设置，它就相当直观。

In this tip, I'll walk you through the basic components of the Chromium F12 Profiler's UI, so you can start identifying performance bottlenecks on your web app!  
在这个技巧中，我将向您介绍 Chromium F12 分析器 UI 的基本组件，以便您开始识别您的 Web 应用程序中的性能瓶颈！

## [](https://webperf.tips/tip/profiler-basic-ui/#prerequisite)Prerequisite  先决条件

You should [collect a trace](https://webperf.tips/tip/collect-a-trace) and ensure it's loaded up in the Chromium Profiler.  
您应该收集一个跟踪并确保它在 Chromium 分析器中已加载。

The loaded profile I'm using in this article looks like this:  
我在这篇文章中使用的加载配置文件看起来是这样的：

![A presentation of the Chromium F12 Profiler UI](https://webperf.tips/static/01f58dd6dbab377cd74b999e34bd52e8/906b5/ProfilerBasicUI01.png "A presentation of the Chromium F12 Profiler UI")

## [](https://webperf.tips/tip/profiler-basic-ui/#layout)Layout  布局

There are three primary sections:  
有三个主要部分：

1. Trace Overview  跟踪概述
2. Selection Analysis Pane  选择分析面板
3. Details Bottom Pane  详情底部面板

![A presentation of the Chromium F12 Profiler UI with the 3 sections denoted.](https://webperf.tips/static/7b41de14417ca18f41c1627733acd62f/906b5/ProfilerBasicUI02.png "A presentation of the Chromium F12 Profiler UI with the 3 sections denoted.")

## [](https://webperf.tips/tip/profiler-basic-ui/#section-1-trace-overview)Section 1: Trace Overview  
第 1 节：跟踪概述

I think about this section as an "Overworld Map" or "minimap" of the entire captured profile.  
我认为这一节是整个捕获配置文件的“世界地图”或“迷你地图”。

Regardless of the duration of the collected profile, the entire profile will fit in this top section.  
无论收集到的配置文件持续时间如何，整个配置文件都将适合这个顶部部分。

It provides at a glance visualizations that you can use to _Select_ or _Zoom in_ to in the Selection Analysis Pane for deeper analysis.  
它提供了一目了然的可视化效果，您可以使用这些效果在“选择分析面板”中选择或放大以进行更深入的分析。

### [](https://webperf.tips/tip/profiler-basic-ui/#timestamps)Timestamps  时间戳

In the Profiler UI, time is represented from left to right, meaning the left-most portion of the trace represents the beginning of the collection and subsequent captured events are filled in to the right as time passes.  
在分析器 UI 中，时间从左到右表示，意味着最左侧的部分代表收集的开始，随着时间的推移，后续捕获的事件填充到右侧。

One thing that can be initially confusing is the timestamps both above and below this section.  
一件最初可能会让人困惑的事情是这一节上方和下方的时间戳。

![A presentation of the Chromium F12 Profiler Trace Overview section, with timestamps circled.](https://webperf.tips/static/a3f1ec0fa632506d549f9b323df6a504/906b5/ProfilerBasicUI03.png "A presentation of the Chromium F12 Profiler Trace Overview section, with timestamps circled.")

The timestamps that are used for the Trace Overview are the timestamps at the top (circled in Red above). These timestamps will span the entire duration of your trace collection.  
用于跟踪概览的时间戳是顶部的时间戳（如上图所示用红色圈出）。这些时间戳将覆盖您跟踪收集的整个持续时间。

The timestamps in Blue are related to the Selection Analysis Pane, and are driven by Selection.  
蓝色时间戳与选择分析面板相关，由选择驱动。

### [](https://webperf.tips/tip/profiler-basic-ui/#selection)Selection  选择

In order to get more details specific sections of the captured trace, we need to use Selection (or Zooming).  
为了获取捕获跟踪的更详细的部分，我们需要使用选择（或缩放）。

Click and drag in the Trace Overview section in an area of interest:  
点击并拖动在跟踪概览部分的感兴趣区域：

![Selecting a section of the Trace Overview in the Chromium F12 Profiler UI](https://webperf.tips/static/b1beaf12a3a60f9278d7d946dd81d385/906b5/ProfilerBasicUI04.png "Selecting a section of the Trace Overview in the Chromium F12 Profiler UI")

This will scope the Selection Analysis pane to **only** the area you've just selected. Notice the timestamps below (circled in Blue) change to reflect the section you've selected:  
这将仅将选择分析面板的范围缩小到您刚刚选择的区域。注意下面的（用蓝色圈出的）时间戳会改变以反映您选择的章节：

![Selecting a section of the Trace Overview in the Chromium F12 Profiler UI](https://webperf.tips/static/744d0722723e8b496f4ae5cc1907dfbc/906b5/ProfilerBasicUI05.png "Selecting a section of the Trace Overview in the Chromium F12 Profiler UI")

You can also use the Mouse Wheel, WSAD keys, or Click-and-Drag in the Selection Analysis Pane to adjust your Selection / Zoom area.  
您还可以使用鼠标滚轮、WSAD 键或在选择分析面板中点击并拖动来调整您的选择/缩放区域。

### [](https://webperf.tips/tip/profiler-basic-ui/#screenshot-preview)Screenshot Preview  截图预览

If you enabled screenshots during your trace collection (which I always recommend!) you should see a UI preview as you hover over the Trace Overview section.  
如果您在收集跟踪时启用了截图（我总是推荐这样做！），那么当您悬停在跟踪概览部分时，应该会看到一个 UI 预览。

This is particularly helpful if you want to scope a selection to a specific visual scenario.  
这对于您想要将选择范围限定到特定视觉场景时特别有帮助。

For example, you may want to select the portion of time between the user clicking a button and a modal appearing on the screen to see what the CPU and Network were doing in that block of time between those events happening.  
例如，您可能想选择用户点击按钮和模态出现在屏幕之间的时间段，以查看在这两个事件发生之间的那段时间内 CPU 和网络都在做什么。

![Selecting a section of the Trace Overview in the Chromium F12 Profiler UI](https://webperf.tips/static/a030bed263c1b07f3bc0678cbf8e254d/906b5/ProfilerBasicUI17.png "Selecting a section of the Trace Overview in the Chromium F12 Profiler UI")

## [](https://webperf.tips/tip/profiler-basic-ui/#section-2-selection-analysis-pane)Section 2: Selection Analysis Pane  
第二节：选择分析面板

This is the most complex section of the Profiler, and also where I spend most of my time analyzing profiles.  
这是 Profiler 中最复杂的部分，也是我花费大部分时间分析配置文件的地方。

This pane is **scoped** to whatever the selection you've set in the Trace Overview section. It will always fit the entirety of where you've selected.  
此面板的范围适用于您在跟踪概述部分设置的任何选择。它始终会适应您所选的全部内容。

### [](https://webperf.tips/tip/profiler-basic-ui/#sub-panes)Sub-panes  子窗格

This UI has multiple sub-panes that can be collapsed or expanded.  
此 UI 有多个可折叠或展开的子面板。

For beginners, I recommend expanding only the _Main_ and _Network_ tab, while collapsing the rest:  
对于初学者，我建议仅展开“主”和“网络”标签页，其余的折叠起来：

![Selection Analysis Pane with only Main and Network sub-panes expanded](https://webperf.tips/static/364cd01bb020bad5f4867ce0c031184f/906b5/ProfilerBasicUI06.png "Selection Analysis Pane with only Main and Network sub-panes expanded")

#### [](https://webperf.tips/tip/profiler-basic-ui/#network-sub-pane)Network Sub-pane  网络子面板

The **Network sub-pane** shows all networking activity within your selection.  
网络子窗格显示您选择项内的所有网络活动。

You can inspect the type, order, and durations of network requests made in the time slice of your selection.  
您可以检查您选择的时间段内发出的网络请求的类型、顺序和持续时间。

![Selection Analysis Pane with the Network sub-pane highlighted](https://webperf.tips/static/bcc5e7cc7a82f4c5b9db0a67c518f3b3/906b5/ProfilerBasicUI07.png "Selection Analysis Pane with the Network sub-pane highlighted")

For details on how to read this pane, see my detailed tip on [the Chromium Profiler Network Pane](https://webperf.tips/tip/network-profiler-pane).  
关于如何阅读此面板的详细信息，请参阅我在 Chromium Profiler 网络面板上的详细技巧。

#### [](https://webperf.tips/tip/profiler-basic-ui/#main-sub-pane)Main Sub-pane  主子面板

The **Main sub-pane** refers to the Main UI thread of your web application.  
主子面板指的是您的 Web 应用程序的主 UI 线程。

All CPU activity taking place on the Main UI thread is recorded here as Tasks.  
所有在主 UI 线程上进行的 CPU 活动都记录在这里，作为任务。

![Selection Analysis Pane with the Main sub-pane highlighted](https://webperf.tips/static/411d06b17a65640deab075b3cb259c10/906b5/ProfilerBasicUI08.png "Selection Analysis Pane with the Main sub-pane highlighted")

CPU activity can include, but is not limited to:  
CPU 活动可能包括但不限于：

- Parsing of HTML  HTML 解析
- Execution and compilation of JavaScript  
    执行和编译 JavaScript
- Handling User Input such as Clicks and Keypress events  
    处理用户输入，如点击和按键事件
- Style, Layout, and Paint, used to prepare for generation of pixels / frames  
    风格、布局和绘画，用于准备生成像素/帧

For details on how to read this pane, see my detailed tip on [the Chromium Profiler Main Thread Pane](https://webperf.tips/tip/main-profiler-pane).  
关于如何阅读此面板的详细信息，请参阅我在 Chromium Profiler 主线程面板上的详细技巧。

#### [](https://webperf.tips/tip/profiler-basic-ui/#other-panes)Other panes  其他窗格

Other helpful panes include:  
其他有用的面板包括：

- _Frames_ - a representation of frames generated to the screen over time  
    帧 - 随时间生成到屏幕上的帧表示
- _Timings_ - custom [markers](https://webperf.tips/tip/performance-timing-markers) web developers can place to help measure and annotate profiles  
    时间 - 网页开发者可以放置以帮助测量和注释配置文件的定制标记
- _WebWorker_ or _ServiceWorker_ threads - other JavaScript execution threads  
    WebWorker 或 ServiceWorker 线程 - 其他 JavaScript 执行线程
- _Compositor_ - activity for compositing graphical layers and preparing frame generation  
    合成器 - 用于合成图形层和准备帧生成的活动

![Selection Analysis Pane with all sub-panes collapsed](https://webperf.tips/static/e0c147e38443af72783b3a1d33fb2655/906b5/ProfilerBasicUI09.png "Selection Analysis Pane with all sub-panes collapsed")

These are covered in my detailed tip on [advanced profiler UI](https://webperf.tips/tip/profiler-advanced-ui).  
这些内容在我的详细高级分析器 UI 技巧中有所涉及。

## [](https://webperf.tips/tip/profiler-basic-ui/#section-3-details-bottom-pane)Section 3: Details Bottom Pane  
第 3 节：底部详细信息面板

At the bottom of the page, there's a Details Pane. Similar to the Selection Analysis pane, the statistics shared in this UI are scoped to the selection in the Trace Overview:  
页面底部有一个详细信息面板。类似于选择分析面板，此 UI 中共享的统计信息范围限定在跟踪概览中的选择：

![Details Bottom Pane highlighted](https://webperf.tips/static/01636480a736d5ea94c00a633cba68b6/906b5/ProfilerBasicUI10.png "Details Bottom Pane highlighted")

### [](https://webperf.tips/tip/profiler-basic-ui/#summary-tab)Summary Tab  摘要标签

The **Summary Tab** provides stats on what type of CPU activity is consuming your main thread:  
摘要标签提供有关消耗主线程的 CPU 活动类型的统计数据：

In this example, within the **790ms** selection, **592ms** is being spent on JavaScript activity:  
在这个例子中，在 790 毫秒的选择中，有 592 毫秒用于 JavaScript 活动：

![Details bottom pane summary tab](https://webperf.tips/static/d25f36f59c9815b18130fcde6a340aeb/83b75/ProfilerBasicUI11.png "Details bottom pane summary tab")

It should be noted that the Summary tab adapts to what entity is selected.  
需要注意的是，摘要标签会根据所选实体进行适配。

For example, if you select a network request, it will provide a summary of the network resource:  
例如，如果您选择一个网络请求，它将提供网络资源的摘要：

![Details bottom pane summary tab](https://webperf.tips/static/904d1989a48d3deb458f1e0e2bde70a8/906b5/ProfilerBasicUI15.png "Details bottom pane summary tab")

### [](https://webperf.tips/tip/profiler-basic-ui/#bottom-up-tab)Bottom Up Tab  自下而上标签

The **Bottom Up** tab provides information on time spent for various CPU / JavaScript activities within a selection from a reverse callstack perspective.  
底层数据选项卡提供了从反向调用栈的角度查看各种 CPU/JavaScript 活动所花费时间的详细信息。

For example, if `function a()` calls `function b()`, `b`'s time to execute is shown on top:  
例如，如果 `function a()` 调用 `function b()` ， `b` 的执行时间将显示在顶部：

![Details bottom pane bottom-up tab](https://webperf.tips/static/dbbe668d6b79a79301be2abff48f5084/5205c/ProfilerBasicUI12.png "Details bottom pane bottom-up tab")

This is covered in my detailed tip on [flamegraphs in-depth](https://webperf.tips/tip/flamegraphs-in-depth).  
这是在我的详细提示中关于火焰图的深入探讨。

### [](https://webperf.tips/tip/profiler-basic-ui/#call-tree-tab)Call Tree Tab  调用树标签

The **Call Tree** tab provides information on time spent for various CPU / JavaScript activities from a top-down perspective (in order of invocation):  
调用树选项卡提供了从上到下（按调用顺序）的视角，关于各种 CPU/JavaScript 活动所花费时间的详细信息

For example, if `function a()` calls `function b()`, `a`'s cumulative time to execute is shown on top:  
例如，如果 `function a()` 调用 `function b()` ， `a` 的累计执行时间将显示在顶部：

![Details bottom pane call tree tab](https://webperf.tips/static/5bb0ccc403f013d9112efa8f060dfe5a/7fee5/ProfilerBasicUI13.png "Details bottom pane call tree tab")

This is also covered in my detailed tip on [flamegraphs in-depth](https://webperf.tips/tip/flamegraphs-in-depth).  
这是在我的详细提示中关于火焰图深入探讨的部分。

### [](https://webperf.tips/tip/profiler-basic-ui/#event-log-tab)Event Log Tab  事件日志标签

The **Event Log** tab shows all Events in your selection in order from top to bottom.  
事件日志选项卡按从上到下的顺序显示您选择的所有事件。

It shows the same task information as the Main thread sub-pane from left to right, but ordered in a list view representation:  
它显示了从左到右与主线程子面板相同的任务信息，但以列表视图的形式排列：

![Details bottom pane event log tab with arrows denoting time](https://webperf.tips/static/b18887f068823d30443089077fdbd8b1/906b5/ProfilerBasicUI16.png "Details bottom pane event log tab with arrows denoting time")

## [](https://webperf.tips/tip/profiler-basic-ui/#conclusion)Conclusion  结论

The UI of the profiler is extremely powerful and it can be confusing to know where to start.  
分析器的 UI 功能非常强大，但知道从哪里开始可能会让人感到困惑。

By completing this tip, you can now start to see what your web application is doing as it's running in terms of CPU and Network activity.  
通过完成这个技巧，你现在可以开始看到你的 Web 应用程序在运行时的 CPU 和网络活动情况。

I recommend checking out these tips to help you understand the profiler even further:  
我建议查看这些技巧以帮助您更深入地了解分析器：

- [How to Read Flamegraphs  
    如何阅读火焰图](https://webperf.tips/tip/understanding-flamegraphs)
- [Advanced Profiler UI  高级配置文件 UI](https://webperf.tips/tip/profiler-advanced-ui)
- [Learn how to use the Profiler Network Pane  
    学习如何使用分析器网络面板](https://webperf.tips/tip/network-profiler-pane)
- [Advanced usage on the Profiler Main Pane  
    高级使用在分析器主面板上](https://webperf.tips/tip/main-profiler-pane)

That's all for this tip! Thanks for reading! _Discover more similar tips matching [Beginner](https://webperf.tips/topic/beginner), [CPU](https://webperf.tips/topic/cpu), and [Profiler](https://webperf.tips/topic/profiler)._  
这是本技巧的全部内容！感谢阅读！发现更多匹配初学者、CPU 和剖析器的相似技巧。

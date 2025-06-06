**Note:** See [Optimize Website Speed](https://developer.chrome.com/docs/devtools/lighthouse) to learn how make your pages load faster.  
注意：请参阅《优化网站速度》以了解如何使您的页面加载更快。

Runtime performance is how your page performs when it is running, as opposed to loading. This tutorial teaches you how to use the Chrome DevTools Performance panel to analyze runtime performance. In terms of the [RAIL](https://web.dev/rail/) model, the skills you learn in this tutorial are useful for analyzing the Response, Animation, and Idle phases of your page.  
运行时性能是指您的页面在运行时的表现，而不是加载时。本教程将教您如何使用 Chrome DevTools 性能面板来分析运行时性能。在 RAIL 模型方面，本教程中您学到的技能可用于分析页面的响应、动画和空闲阶段。

**Caution:** This tutorial is based on Chrome 129. If you use another version of Chrome, the UI and features of DevTools may be different. Check `chrome://help` to see what version of Chrome you're running.  
注意：本教程基于 Chrome 129 版本。如果您使用的是 Chrome 的另一个版本，DevTools 的界面和功能可能会有所不同。请查看 `chrome://help` 以了解您正在运行的 Chrome 版本。

## Get started  开始使用

In this tutorial, we will use the **Performance** panel to find a performance bottleneck on a live page. To begin:  
在这个教程中，我们将使用性能面板来查找一个实时页面的性能瓶颈。开始：

1. Open Google Chrome in [Incognito Mode](https://support.google.com/chrome/answer/95464). Incognito Mode ensures that Chrome runs in a clean state. For example, if you have a lot of extensions installed, those extensions might create noise in your performance measurements.  
    以隐身模式打开 Google Chrome。隐身模式确保 Chrome 以干净的状态运行。例如，如果你安装了很多扩展，这些扩展可能会在性能测量中产生干扰。
2. Load the following page in your Incognito window. This is the demo that you're going to profile. The page shows a bunch of little blue squares moving up and down.  
    在隐身窗口中加载以下页面。这是你将要分析的性能演示。页面显示了一堆上下移动的小蓝色方块。
    
    `https://googlechrome.github.io/devtools-samples/jank/`
    
3. Press Command+Option+I (Mac) or Control+Shift+I (Windows, Linux) to open DevTools.  
    按下 Command + Option + I （Mac）或 Control + Shift + I （Windows、Linux）以打开开发者工具。
    
    ![The demo on the left, and DevTools on the right.](https://developer.chrome.com/static/docs/devtools/performance/image/demo-and-devtools.png)
    
    **Note**: For the rest of the screenshots, DevTools is [undocked to a separate window](https://developer.mozilla.org/docs/Web/API/window/requestAnimationFrame) so that you can see its contents better.  
    注意：对于其余的截图，DevTools 已分离到一个单独的窗口中，以便更好地查看其内容。
    

### Simulate a mobile CPU  模拟移动 CPU

Mobile devices have much less CPU power than desktops and laptops. Whenever you profile a page, use CPU Throttling to simulate how your page performs on mobile devices.  
移动设备的 CPU 性能远低于台式机和笔记本电脑。每次分析页面时，都使用 CPU 节流来模拟您的页面在移动设备上的性能。

1. In DevTools, click the **Performance** tab.  
    在 DevTools 中，点击“性能”选项卡。
2. Make sure that the **Screenshots** check_box checkbox is enabled.  
    确保 Screenshots 复选框已启用。
3. Click **Capture Settings** settings. DevTools reveals settings related to how it captures performance metrics.  
    点击 Capture Settings 。DevTools 显示与它捕获性能指标相关的设置。
4. For **CPU**, select **4x slowdown**. DevTools throttles your CPU so that it's 4 times slower than usual.  
    对于 CPU，选择 4x 减速。DevTools 会限制你的 CPU，使其比平时慢 4 倍。
    
    ![CPU throttling set to 4x slowdown.](https://developer.chrome.com/static/docs/devtools/performance/image/cpu-throttling.png)
    
    **Note**: When testing other pages, if you want to ensure that they work well on low-end mobile devices, set CPU Throttling to **20x slowdown**. This demo doesn't work well with 20x slowdown, so it just uses 4x slowdown for instructional purposes.  
    注意：在测试其他页面时，如果你想确保它们在低端移动设备上运行良好，请将 CPU Throttling 设置为 20x 减速。这个演示与 20x 减速效果不佳，因此仅使用 4x 减速作为教学示例。
    

### Set up the demo  设置演示

It's hard to create a runtime performance demo that works consistently for all readers of this website. This section lets you customize the demo to ensure that your experience is relatively consistent with the screenshots and descriptions you see in this tutorial, regardless of your particular setup.  
很难创建一个对所有这个网站读者都运行一致的运行时性能演示。本节允许你自定义演示，以确保你的体验与这个教程中看到的截图和描述相对一致，无论你的特定设置如何。

1. Keep clicking **Add 10** until the blue squares move noticeably slower than before. On a high-end machine, it may take about 20 clicks.  
    继续点击添加 10，直到蓝色方块明显比之前移动得更慢。在一台高端机器上，这可能需要大约 20 次点击。
2. Click **Optimize**. The blue squares should move faster and more smoothly.  
    点击优化。蓝色方块应该移动得更快、更流畅。
    
    **Note:** If you don't see a noticeable difference between the optimized and un-optimized versions, try clicking **Subtract 10** a few times and trying again. If you add too many blue squares, you're just going to max out the CPU and you're not going to see a major difference in the results for the two versions.  
    注意：如果你在优化版本和未优化版本之间没有看到明显差异，请尝试几次点击“减去 10”然后再次尝试。如果你添加太多蓝色方块，你只会使 CPU 达到最大值，而且你不会看到两个版本的结果有显著差异。
    
3. Click **Un-Optimize**. The blue squares move slower and with more jank again.  
    点击“未优化”。蓝色方块再次移动得更慢，并且出现卡顿。
    

### Record runtime performance  
录制运行时性能

When you ran the optimized version of the page, the blue squares move faster. Why is that? Both versions are supposed to move each square the same amount of space in the same amount of time. Take a recording in the **Performance** panel to learn how to detect the performance bottleneck in the un-optimized version.  
当你运行页面优化版本时，蓝色方块移动得更快。为什么会出现这种情况？两个版本都应该在同一时间内移动每个方块相同的距离。在性能面板中录制一次来学习如何检测未优化版本中的性能瓶颈。

1. In DevTools, click **Record** radio_button_checked. DevTools captures performance metrics as the page runs.  
    在 DevTools 中，点击录制。DevTools 在页面运行时捕获性能指标。
    
    ![Profiling the demo page.](https://developer.chrome.com/static/docs/devtools/performance/image/profiling-page.png)
    
2. Wait a few seconds.  等几秒钟。
    
3. Click **Stop**. DevTools stops recording, processes the data, then displays the results in the **Performance** panel.  
    点击停止。DevTools 停止录制，处理数据，然后在性能面板中显示结果。
    
    ![The profiling report page.](https://developer.chrome.com/static/docs/devtools/performance/image/profiling-results.png)
    

Wow, that's an overwhelming amount of data. Don't worry, it'll make more sense shortly.  
哇，这些数据量太庞大了。别担心，很快就会变得清晰易懂。

## Analyze the results  分析结果

Once you have a performance recording, you can analyze just how poor the page's performance is, and find the cause(s).  
一旦你有了性能录制，你就可以分析页面的性能有多差，并找到原因。

### Analyze frames per second  
分析每秒帧数

The main metric for measuring the performance of any animation is frames per second (FPS). Users are happy when animations run at 60 FPS.  
衡量任何动画性能的主要指标是每秒帧数（FPS）。当动画以 60 FPS 运行时，用户会感到满意。

1. Look at the **FPS** chart. Whenever you see a red bar above **FPS**, it means that the framerate dropped so low that it's probably harming the user experience.  
    查看 FPS 图表。每当看到 FPS 上方的红色条，就意味着帧率已经降低到可能损害用户体验的程度。
    
    ![The FPS chart highlighted.](https://developer.chrome.com/static/docs/devtools/performance/image/the-fps-chart.png)
    
2. Below the **FPS** chart you see the **CPU** chart. The colors in the **CPU** chart correspond to the colors in the **Summary** tab, at the bottom of the **Performance** panel. The fact that the **CPU** chart is full of color means that the CPU was maxed out during the recording. Whenever you see the CPU maxed out for long periods, it's a cue to find ways to do less work.  
    FPS 图表下方是 CPU 图表。CPU 图表中的颜色与性能面板底部摘要选项卡中的颜色相对应。CPU 图表色彩丰富意味着在录制期间 CPU 已达到峰值。当 CPU 长时间处于峰值状态时，就意味着需要寻找减少工作量的方法。
    
    ![The CPU chart and Summary tab.](https://developer.chrome.com/static/docs/devtools/performance/image/the-cpu-chart-summary.png)
    
3. Hover your mouse over the **FPS**, **CPU**, or **NET** charts. DevTools shows a screenshot of the page at that point in time. Move your mouse left and right to replay the recording. This is called scrubbing, and it's useful for manually analyzing the progression of animations.  
    将鼠标悬停在 FPS、CPU 或 NET 图表上。DevTools 会显示该时间点的页面截图。左右移动鼠标以重播录制内容。这称为快进，适用于手动分析动画的进展。
    
    ![Viewing a screenshot in a performance recording.](https://developer.chrome.com/static/docs/devtools/performance/image/viewing-screenshot.png)
    
4. In the **Frames** section, hover your mouse over one of the green squares. DevTools shows you the FPS for that particular frame. Each frame is probably well below the target of 60 FPS.  
    在"帧"部分，将鼠标悬停在其中一个绿色方块上。DevTools 会显示该特定帧的 FPS。每一帧很可能都远低于 60 FPS 的目标。
    
    ![Hovering over a frame.](https://developer.chrome.com/static/docs/devtools/performance/image/hovering-a-frame.png)
    

Of course, with this demo, it's pretty obvious that the page is not performing well. But in real scenarios, it may not be so clear, so having all of these tools to make measurements comes in handy.  
当然，通过这个演示，很明显页面的性能并不好。但在实际场景中，情况可能并不那么明显，因此拥有这些工具来测量是非常有用的。

#### Bonus: Open the FPS meter  
小贴士：打开 FPS 计数器

Another handy tool is the **FPS meter**, which provides real-time estimates for FPS as the page runs.  
另一个实用的工具是 FPS 计数器，它可以在页面运行时提供实时的 FPS 估计。

1. Press Command+Shift+P (Mac) or Control+Shift+P (Windows, Linux) to open the Command Menu.  
    按 Command + Shift + P (Mac) 或 Control + Shift + P (Windows, Linux) 打开命令菜单。
2. Start typing `Rendering` in the Command Menu and select **Show Rendering**.  
    在命令菜单中开始输入 `Rendering` 并选择显示渲染。
3. In the **Rendering** panel, enable **Show Rendering stats**. A new overlay appears in the top-right of your viewport.  
    在渲染面板中，启用显示渲染统计。视图右上角会出现一个新的覆盖层。
    
    ![The FPS meter.](https://developer.chrome.com/static/docs/devtools/performance/image/the-fps-meter.png)
    
4. Disable the **FPS Meter** and press Escape to close the **Rendering** panel. You won't be using it in this tutorial.  
    关闭帧率计，然后按 Escape 关闭渲染面板。在本教程中，你将不会使用它。
    

### Find the bottleneck  找到瓶颈

Now that you've measured and verified that the animation is not performing well, the next question to answer is: why?  
既然你已经测量并验证动画性能不佳，接下来要回答的问题是：为什么？

1. Note the **Summary** tab. When no events are selected, this tab shows you a breakdown of activity. The page spent most of its time rendering. Since performance is the art of doing less work, your goal is to reduce the amount of time spent doing rendering work.  
    注意摘要标签页。当没有选择事件时，这个标签页会显示活动分解情况。页面大部分时间都在进行渲染。由于性能的艺术在于减少工作量，你的目标就是减少渲染工作所花费的时间。
    
    ![The Summary tab, outlined in blue.](https://developer.chrome.com/static/docs/devtools/performance/image/the-summary-tab.png)
    
2. Expand the **Main** section. DevTools shows you a flame chart of activity on the main thread, over time. The x-axis represents the recording, over time. Each bar represents an event. A wider bar means that event took longer. The y-axis represents the call stack. When you see events stacked on top of each other, it means the upper events caused the lower events.  
    展开主区域。DevTools 会显示主线程活动随时间变化的火焰图。x 轴代表随时间推移的录制过程。每个条形代表一个事件。条形越宽，表示该事件持续时间越长。y 轴代表调用栈。当看到事件堆叠在一起时，表示上层事件导致了下层事件。
    
    ![The Main section.](https://developer.chrome.com/static/docs/devtools/performance/image/the-main-section.png)
    
3. There's a lot of data in the recording. Zoom in on a single **Animation Frame Fired** event by clicking, holding, and dragging your mouse over the **Overview**, which is the section that includes the **FPS**, **CPU**, and **NET** charts. The **Main** section and **Summary** tab only display information for the selected portion of the recording.  
    录制中包含大量数据。通过在概览区域（包含 FPS、CPU 和 NET 图表的部分）点击、按住并拖动鼠标，可以放大单个“动画帧触发”事件。主区域和摘要标签页仅显示选定录制部分的信息。
    
    ![Zoomed in on a single Animation Frame Fired event.](https://developer.chrome.com/static/docs/devtools/performance/image/zoomed-frame.png)
    
    **Tip:** You can also use WASD to navigate the recording. Focus the **Main** section by clicking its background or selecting an event, then press W to zoom in, S to zoom out, A to scroll left, or D to scroll right.  
    提示：您也可以使用 WASD 来导航录制。通过点击其背景或选择一个事件来聚焦主区域，然后按 W 放大， S 缩小， A 向左滚动，或 D 向右滚动。
    
4. Note the red triangle in the top-right of the **Task** and layout events. Whenever you see a red triangle, it's a warning that there may be an issue related to this event. A red triangle on a **Task** means that it was a [long task](https://web.dev/articles/optimize-long-tasks?utm_source=devtools).  
    注意任务和布局事件右上角的红色三角形。每当看到红色三角形时，它就是一个警告，表示可能与此事件相关的问题。任务上的红色三角形表示它是一个耗时任务。
    
5. Click the **Animation Frame Fired** event. The **Summary** tab now shows you information about that event. Clicking the link next to **Initiated by** causes DevTools to highlight the event that initiated the **Animation Frame Fired** event. Also note the **app.update @** link. Clicking that jumps you to the relevant line in the source code.  
    点击动画帧触发事件。摘要选项卡现在显示该事件的信息。点击 Initiated by 旁边的链接，会使 DevTools 突出显示触发动画帧触发事件的那个事件。同时注意 app.update @ 链接。点击它将带你跳转到源代码的相关行。
    
    ![More information about the Animation Frame Fired event.](https://developer.chrome.com/static/docs/devtools/performance/image/more-information.png)
    
    **Note**: After selecting an event, use the arrow keys to select the events next to it.  
    注意：选择一个事件后，使用箭头键选择它旁边的事件。
    
6. Under the **app.update** event, there's a bunch of purple events. If they were wider, it looks as though each one might have a red triangle on it. Click one of the purple **Layout** events now. DevTools provides more information about the event in the **Summary** tab. Indeed, there's a warning about forced reflows (another word for layout).  
    在 app.update 事件下，有一系列紫色事件。如果它们更宽的话，看起来每个事件上可能都有一个红色三角形。现在点击其中一个紫色 Layout 事件。DevTools 在 Summary 标签中提供了更多关于该事件的信息。确实，有一个关于强制重排（布局的另一种说法）的警告。
    
7. In the **Summary** tab, click the link next to **app.update @** under **Animation Frame Requested**. DevTools takes you to the line of code that forced the layout.  
    在摘要标签页中，点击 Animation Frame Requested 下 app.update 旁边的链接。DevTools 会带你到导致布局的代码行。
    
    ![The line of code that caused the forced layout.](https://developer.chrome.com/static/docs/devtools/performance/image/source-code-caused.png)
    
    **Note**: The problem with this code is that, in each animation frame, it changes the style for each square, and then queries the position of each square on the page. Because the styles changed, the browser doesn't know if each square's position changed, so it has to re-layout the square in order to compute its position. See [Avoid forced synchronous layouts](https://web.dev/articles/avoid-large-complex-layouts-and-layout-thrashing#avoid-forced-synchronous-layouts) to learn more.  
    注意：这段代码的问题在于，在每一帧动画中，它会为每个方块更改样式，然后查询每个方块在页面上的位置。由于样式的改变，浏览器不知道每个方块的位置是否发生变化，因此必须重新布局方块以计算其位置。请参阅避免强制同步布局以了解更多信息。
    

Phew! That was a lot to take in, but you now have a solid foundation in the basic workflow for analyzing runtime performance. Good job.  
呼！这信息量很大，但你现在已经掌握了分析运行时性能的基本工作流程的基础。干得好。

### Bonus: Analyze the optimized version  
进阶：分析优化后的版本

Using the workflows and tools that you just learned, click **Optimize** on the demo to enable the optimized code, take another performance recording, and then analyze the results. From the improved framerate to the reduction in events in the **Main** section's flame chart, you can see that the optimized version of the app does much less work, resulting in better performance.  
使用刚刚学到的流程和工具，点击演示中的“优化”按钮以启用优化代码，进行另一次性能录制，然后分析结果。从提高的帧率到主区域火焰图中事件减少，你可以看到优化后的应用工作量大大减少，从而带来更好的性能。

**Note:** Even this optimized version isn't that great, because it still manipulates the `top` property of each square. A better approach is to stick to properties that only affect compositing. See [Use transform and opacity changes for animations](https://web.dev/articles/stick-to-compositor-only-properties-and-manage-layer-count#use-transform-and-opacity-changes-for-animations) for more information.  
注意：即使这个优化版本也不是那么好，因为它仍然操作每个方块的 `top` 属性。更好的方法是坚持只影响合成属性的属性。有关更多信息，请参阅 Use transform and opacity changes for animations。

## Next steps  下一步

The foundation for understanding performance is the RAIL model. This model teaches you the performance metrics that are most important to your users. See [Measure Performance With The RAIL Model](https://web.dev/rail/) to learn more.  
理解性能的基础是 RAIL 模型。该模型教会你对你用户最重要的性能指标。查看《使用 RAIL 模型测量性能》了解更多。

To get more comfortable with the Performance panel, practice makes perfect. Try profiling your own pages and analyzing the results. If you have any questions about your results, [open a Stack Overflow question tagged with `google-chrome-devtools`](http://stackoverflow.com/questions/ask?tags=google-chrome-devtools). Include screenshots or links to reproducible pages, if possible.  
要更熟悉性能面板，实践出真知。尝试分析自己的页面并查看结果。如果你对自己的结果有任何疑问，可以在 Stack Overflow 上提出一个带有 `google-chrome-devtools` 标签的问题。如果可能的话，附上截图或可复现页面的链接。

To become an expert in runtime performance, you've got to learn how the browser translates HTML, CSS, and JS into pixels on a screen. The best place to start is the [Rendering Performance Overview](https://web.dev/rendering-performance/). [The Anatomy Of A Frame](https://aerotwist.com/blog/the-anatomy-of-a-frame/) dives into even more detail.  
要成为运行时性能的专家，你需要学习浏览器如何将 HTML、CSS 和 JS 转换为屏幕上的像素。最好的起点是渲染性能概览。《帧的解剖》深入探讨了更多细节。

Last, there are many ways to improve runtime performance. This tutorial focused on one particular animation bottleneck to give you a focused tour through the Performance panel, but it's only one of many bottlenecks you may encounter. The rest of the Rendering Performance series has a lot of good tips for improving various aspects of runtime performance, such as:  
最后，有许多方法可以改善运行时性能。本教程专注于一个特定的动画瓶颈，带你专注于性能面板的游览，但这只是你可能遇到的一个瓶颈。渲染性能系列的其余部分有很多关于改善运行时性能各个方面的好建议，例如：

- [Optimizing JS Execution  优化 JS 执行](https://web.dev/optimize-javascript-execution/)
- [Reduce The Scope And Complexity Of Style Calculations  
    减少样式计算的范围和复杂性](https://web.dev/reduce-the-scope-and-complexity-of-style-calculations/)
- [Avoid Large, Complex Layouts And Layout Thrashing  
    避免大型复杂布局和布局抖动](https://web.dev/avoid-large-complex-layouts-and-layout-thrashing/)
- [Simplify Paint Complexity And Reduce Paint Areas  
    简化绘制复杂性并减少绘制区域](https://web.dev/simplify-paint-complexity-and-reduce-paint-areas/)
- [Stick To Compositor-Only Properties And Manage Layer Count  
    坚持仅使用合成器属性并管理图层数量](https://web.dev/stick-to-compositor-only-properties-and-manage-layer-count/)
- [Debounce Your Input Handlers  
    节流输入处理器](https://web.dev/debounce-your-input-handlers/)
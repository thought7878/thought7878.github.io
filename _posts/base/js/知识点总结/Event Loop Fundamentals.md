The Browser Event Loop is probably **the most important** frontend performance concept to grasp. Its behavior dictates how web applications behave at runtime.  
浏览器事件循环可能是*需要掌握的最重要的*前端性能概念。*它的行为决定了Web应用程序在运行时的行为*。

## [](https://webperf.tips/tip/event-loop/#the-main-thread)The Main Thread  主线程

Before we can talk about the Event Loop, we need to introduce some key concepts. Let's start with the **Main Thread**.  
在我们讨论事件循环之前，我们需要介绍一些关键概念。让我们从**主线**开始。

Web applications are required to execute certain key operations on this single Main Thread. When you navigate to your web application, the browser will create and grant your application this thread for your code to execute on.  
Web应用程序需要在这个主线程上执行某些关键操作。当您导航到您的Web应用程序时，浏览器将创建并授予您的应用程序此线程，以便在其上执行代码。

Most of your web application's runtime activity takes place on this single thread, and each activity on the thread is invoked within a Task.  
大多数Web应用程序的运行时活动都发生在这个线程上，线程上的每个活动都在一个Task中调用。

![[_posts/base/js/知识点总结/media/7273ae73b880a4906150e046976fcd22_MD5.png|"A diagram showing an Arrow, representing the Main Thread"]]

> **Note**: This does not mean the Browser (i.e. Chrome) is single threaded. Modern browsers are both [multi-process and multi-threaded](https://webperf.tips/tip/browser-process-model).  
> 注意：这并不意味着浏览器（即Chrome）是单线程的。现代浏览器都是多进程和多线程的。

## [](https://webperf.tips/tip/event-loop/#tasks)Tasks

**Tasks** are units of runtime execution that are executed on the Main Thread.  
**任务**是在主线程上执行的运行时执行单元。

Some common examples of Tasks include:  
*一些常见的任务示例包括：*

- Parsing of HTML or CSS  
    解析HTML或CSS
- User Input Handling, such as clicking or typing  
    用户输入处理，如单击或键入
- Compiling and Executing JavaScript  
    编译和执行JavaScript
- Receiving Network Data  接收网络数据
- The Render Steps (Style, Layout, and Paint; discussed later)  
    渲染步骤（样式、布局和绘制;稍后讨论）

![[_posts/base/js/知识点总结/media/a0dbdcfdb66e7cf41bcb3b5ccd372c8d_MD5.png|"A set of Boxes, visually representing each example Task"]]

Certain Tasks **must** run on the Main Thread. For example, anything that directly requires access to the DOM (i.e. `document`) must be run on the Main Thread (because the DOM is not thread-safe). This would include most UI related code.  
某些任务必须在主线程上运行。例如，任何直接需要访问DOM（即文档）的东西都必须在主线程上运行（因为DOM不是线程安全的）。这将包括大多数UI相关的代码。

**Only one** Task can run on the Main Thread at a time.  
*一次只能在主线程上运行一个任务*。

Furthermore, a Task **must run to completion** on the Main Thread before another Task can be run. There is no mechanism for the browser to "partially" execute a Task -- each Task is run in its entirety to completion.  
此外，*一个任务必须在主线程上运行完成，然后才能运行另一个任务。浏览器没有“部分”执行任务的机制--每个任务都是完整地运行完成的*。

In the example below, two tasks are run sequentially and each to completion on the Main Thread:  
在下面的示例中，两个任务按顺序运行，每个任务都在主线程上完成：

![[_posts/base/js/知识点总结/media/62c9cddbec3546bc53e713c907103cae_MD5.png|"Two tasks executing sequentially on the Main thread"]]

## [](https://webperf.tips/tip/event-loop/#task-queue)Task Queue  

It is impossible to run multiple Tasks simultaneously on the Main Thread, so any Task that is not actively running resides in a data structure called the **Task Queue**.  
*在主线程上同时运行多个任务是不可能的*，**因此任何没有运行的任务都驻留在一个称为*任务队列的数据结构中***。

![[_posts/base/js/知识点总结/media/6382b2d70156fef45c1fe91dad8d8eec_MD5.png|"The Task queue buffering three Tasks while one executes"]]

As your web application executes on the Main Thread, the browser queues Tasks into this data structure.  
当您的Web应用程序在主线程上执行时，浏览器会将任务排队到此数据结构中。

It also reads from this queue when the Main Thread is ready to execute the next Task.  
当主线程准备好执行下一个任务时，它也从这个队列中读取。

> **Note**: The Task Queue is not a FIFO queue. Most browsers implement heuristic-based scheduling logic, [ranking some Tasks](https://webperf.tips/tip/browser-scheduling-internals) above others.  
> 注意：任务队列不是FIFO队列。大多数浏览器都实现了基于时间的调度逻辑，将一些任务排在其他任务之上。

## [](https://webperf.tips/tip/event-loop/#the-render-steps-task-and-frames)The Render Steps Task and Frames  
渲染步骤任务和帧

The **Render Steps Task** is responsible for the translation of the active DOM and CSS Styles into visual pixels on the user's screen.  
**渲染步骤任务**负责将活动DOM和CSS样式转换为用户屏幕上的可视像素。

What's produced after the Render Steps Task completes is known as a **Frame**.  
*渲染步骤任务完成后生成的内容*称为**帧**。

![[_posts/base/js/知识点总结/media/01bb4390dffcb5589708078982986591_MD5.png|"Render Step Task visualized."]]

Edits to the DOM or CSS Styles for a web page will notify the browser that it needs to generate a Frame. When the browser detects it needs to generate a Frame, it'll run the Render Steps Task instead of running a Task from the Task Queue:  
*编辑网页的DOM或CSS样式将通知浏览器它需要生成一个Frame。当浏览器检测到需要生成帧时，它将运行渲染步骤任务，而不是运行任务队列中的任务：*

![[_posts/base/js/知识点总结/media/ccfc402e822b2937c9f9c37733989a79_MD5.png|"Render Step Task interleaved on the thread."]]

There are generally 3 key steps for the Render Steps to complete:  
渲染步骤通常有3个关键步骤要完成：

1. _Style_ - Assign CSS colors, fonts, and other styling properties to visual elements  
    _样式_-为可视元素分配CSS颜色、字体和其他样式属性
2. _Layout_ - Position visual elements with precise coordinates  
    _布局_-使用精确坐标定位视觉元素
3. _Paint_ - Instruct graphics libraries on how to draw positioned, styled visual elements  
    _绘画_-指导图形库如何绘制定位，样式化的视觉元素

For more details on each step, check out my detailed tip on [the browser rendering pipeline](https://webperf.tips/tip/browser-rendering-pipeline).  
有关每个步骤的更多详细信息，请查看我关于[浏览器渲染管道的](https://webperf.tips/tip/browser-rendering-pipeline)详细提示。

## [](https://webperf.tips/tip/event-loop/#the-event-loop)The Event Loop

Finally, with all of these foundational topics covered, we can finally discuss the Event Loop!  
最后，在涵盖了所有这些基础主题之后，我们终于可以讨论事件循环了！

The **Event Loop** is the orchestrator between these foundational pieces.  
**事件循环**是这些基础部分之间的协调器。

It is essentially an infinite loop, responsible for:  
它本质上是*一个无限循环，负责：*

- Selecting an available Task from the Task Queue, and placing it on the Main Thread to run to completion  
    从任务队列中选择一个可用的任务，并将其放置在主线程上以运行完成
- Occasionally selecting the Render Steps Task instead of reading from the Task Queue to generate a Frame  
    偶尔选择渲染步骤任务而不是从任务队列中阅读以生成帧

This pseudo-code snippet summarizes the Event Loop:  
这个伪代码片段总结了事件循环：

```js
while (true) {
    var nextTask;

    // The browser notifies the Event Loop
    // when it's time to render a frame
    if (shouldRenderFrame()) {
        nextTask = getRenderStepsTask();
    } else {
        // If it's not ready for a frame,
        // the Event Loop should run the
        // next Task.
        nextTask = taskQueue.next();
    }

    // Run Task to completion
    runOnMainThread(nextTask);
}
```

On each turn of the Event Loop, it decides either to select a Task or to render a Frame to the screen.  
*在事件循环的每一轮，它决定选择一个任务或渲染一个帧到屏幕上。*

What is key to understand is that the browser can **either** run a Task or generate a Frame, but it cannot do both at the same time. (there are limited exceptions to this, but I will not discuss them in this tip)  
关键是要理解的是，浏览器可以运行任务或生成帧，但*它不能同时做这两件事*。（有一些例外情况，但我不会在这篇技巧文章中讨论它们）

Whichever ends up running, it's run until completion.  
*无论运行什么，它都会一直运行到完成*。

In general, Task durations should be short so the browser can invoke the Render Steps more quickly to produce a Frame to your users. This is covered in more detail on my [avoiding Long Tasks](https://webperf.tips/tip/long-tasks) tip.  
*通常，任务持续时间应该很短，以便浏览器可以更快地调用渲染步骤来为用户生成帧*。这在我的避免长时间任务技巧中有更详细的介绍。

> **Note**: The Event Loop is a C++ construct and is not written in JavaScript, I just happened to use that for this pseudo-code.  
> 注意：事件循环是一个C++结构，不是用JavaScript编写的，我只是碰巧在这个伪代码中使用了它。

## [](https://webperf.tips/tip/event-loop/#non-blocking)Non-Blocking  
非阻塞

The Event Loop, by design, never _pauses_ or _blocks_ the Main Thread; it will continuously loop and run Tasks and produce Frames.  
事件循环，根据设计，永远不会*暂停*或*阻塞*主线程；它将连续循环和运行任务并产生帧。

But what happens when we need to do an **asynchronous task**? For example, if our web application requests network data or utilizes a timer, how does this manifest in the Event Loop?  
但是当我们需要*执行异步任务时会发生什么呢？* 例如，如果我们的Web应用程序请求网络数据或使用计时器，这在事件循环中如何表现？

Each asynchronous primitive provided by the browser is represented as a Task that is compatible with the Event Loop execution model.  
浏览器提供的每个异步原语都表示为与事件循环执行模型兼容的任务。

Example asynchronous Browser APIs include:  
示例异步浏览器API包括：

- Loading network data via `XMLHttpRequest` or `fetch`  
    通过`XMLHttpRequest`或`fetch`加载网络数据
- Responding to events via `addEventListener(...)`  
    通过`addEventEvent(...)`响应事件
- Timers like `setTimeout` or `setInterval`  
    定时器，如`setInterval`或`setInterval`

For example, utilizing `setTimeout(callback, 500)` does not block the Main Thread, but tells the browser to queue a Task to run `callback` on the Task Queue after 500ms.  
例如，使用`setTimeout(callback, 500)` 不会阻塞主线程，而是告诉浏览器将一个任务排队，以便在500 ms后在任务队列上运行回调。

## [](https://webperf.tips/tip/event-loop/#example-timer)Example: Timer  
示例：计时器

Let's put together everything we've covered in a simple example.  
让我们把我们在一个简单的例子中介绍的所有内容放在一起。

Our example will be a simple script that edits the DOM in a Timer callback every 500ms.  
我们的示例是一个简单的脚本，它每隔500毫秒在Timer回调中编辑DOM。

```js
let count = 0;

// Queue a Task on the Task Queue every 500ms
setInterval(() => {
    // This code runs in a Task
    count++;
    const node = document.getElementById('output');
    node.innerText = `${count} Timers Fired!`
}, 500);
```

Since this edits the DOM in a Task, the browser will need to generate a Frame to represent the changes as pixels.  
由于这会编辑任务中的DOM，因此浏览器需要生成一个Frame来将更改表示为像素。

Initially, after this script is evaluated, our Main Thread will be free, and the Task Queue will be empty. The browser has been directed to queue a Task after 500ms.  
最初，在这个脚本被评估之后，我们的主线程将是空闲的，任务队列将是空的。*浏览器已被指示在500毫秒后对任务进行排队*。

Though the thread has no activity happening during this 500ms waiting time, it is free and able to respond to clicking, typing, or other Tasks if there were any queued.  
虽然线程在这500毫秒的等待时间内没有任何活动发生，但它是自由的，并且*能够响应单击，键入或其他任务*（如果有任何排队的话）。

![[_posts/base/js/知识点总结/media/1728e8d4c1f84c186dab3b67884932ab_MD5.png|"An empty Task Queue and thread"]]

After 500ms, the browser will queue a Task for running our callback for editing the DOM:  
*500ms后，浏览器将排队一个Task，以运行我们的回调来编辑DOM：*

![[_posts/base/js/知识点总结/media/7ee6393e667c81ec51288e2ec458c1e6_MD5.png|"A task queued on the Task Queue"]]

The Event Loop detects a Task queued to be run, and moves it from the Queue to the Main Thread to run (since the Main Thread is not busy doing anything else).  
*事件循环检测到一个排队等待运行的任务，并将其从队列移动到主线程运行*（因为主线程不忙于做任何其他事情）。

![[_posts/base/js/知识点总结/media/d9d8c2eebf3d27dd4cd9aa797cd042b8_MD5.png|"A task dequeued from the Task Queue"]]

This task is run and edits the DOM, which notifies the Event Loop to run the Render Steps:  
*此任务运行并编辑DOM，通知事件循环运行渲染步骤：*

![[_posts/base/js/知识点总结/media/07e48c90eb3e5b158332776c5a3987e3_MD5.png|"A frame produced by the Event Loop"]]

Once this Frame is produced, our updated text appears on the screen!  
*一旦这个Frame产生，我们更新的文本出现在屏幕上*！

Every 500ms, these exact phases will continually take place, with a Timer Tasks being queued. The Event Loop will remove each from the queue, and place it on the Main Thread to run. This will cause the Event Loop to run the Render Steps Task and produce a Frame.  
*每500 ms，这些确切的阶段将不断发生，并对计时器任务进行排队。事件循环将从队列中删除每一个，并将其放在主线程上运行。这将导致事件循环运行渲染步骤任务并生成帧。*

You can profile [this example](https://webperf.tips/examples/event-loop/example1/index.html) and see the Tasks and Frame Generation in the profiler!  
您可以分析[此示例](https://webperf.tips/examples/event-loop/example1/index.html)，并在分析器中查看任务和帧生成！

Here's a screenshot of it happening in the Profiler:  
下面是Profiler中发生的截图：

![[_posts/base/js/知识点总结/media/132dd112dd35a89d9b165496b7f8be44_MD5.png|"A frame produced by the Event Loop"]]

While this simple example is just a Timer, you can generalize this to any action in your web application, whether it's a button click or receiving network data.  
虽然这个简单的示例只是一个Timer，但是您可以将其推广到Web应用程序中的任何操作，无论是单击按钮还是接收网络数据。

## [](https://webperf.tips/tip/event-loop/#conclusion)Conclusion  
结论

Almost all operations that a web application does can be boiled down into Tasks queued into the Task Queue, subsequently run on the Main Thread via the Event Loop.  
Web应用程序执行的几乎所有操作都可以归结为任务队列中的任务，随后通过事件循环在主线程上运行。

Tasks that require a visual update rely on the Event Loop to interleave Render Steps Tasks to produce Frames between other Tasks.  
需要视觉更新的任务依赖于事件循环来交错渲染步骤任务，以在其他任务之间生成帧。

You can leverage this knowledge to improve your user experience, by optimizing how and when your Tasks execute.  
您可以利用这些知识来优化任务的执行方式和执行时间，从而改善用户体验。

Consider these tips next:  
接下来考虑这些提示：

- [Browser Process Model  浏览器进程模型](https://webperf.tips/tip/browser-process-model)
- [Browser Scheduling Logic  浏览器调度逻辑](https://webperf.tips/tip/browser-scheduling-internals)
- [Browser Rendering Pipeline  
    浏览器Rendering Pipeline](https://webperf.tips/tip/browser-rendering-pipeline)
- [Long Tasks and Performance  
    长时间任务和性能](https://webperf.tips/tip/long-tasks)

That's all for this tip! Thanks for reading! _Discover more similar tips matching [Browser Internals](https://webperf.tips/topic/browser-internals)._  
这是所有的提示！感谢您的阅读！_发现更多与[浏览器内部组件](https://webperf.tips/topic/browser-internals)匹配的类似提示。_

## 相关资料
- [Event Loop Fundamentals](https://webperf.tips/tip/event-loop/)
- [【前端性能】事件循环基础知识](https://www.bilibili.com/video/BV1xR2BYrEv1/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)
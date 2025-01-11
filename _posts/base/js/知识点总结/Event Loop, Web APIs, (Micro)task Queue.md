[YouTube视频](https://www.youtube.com/watch?v=eiC58R16hb8&ab_channel=LydiaHallie)
[b站视频](https://www.bilibili.com/video/BV1iJ4m157Ya/?spm_id_from=333.999.0.0&vd_source=22af953ea4c09540ad1966711a2d53f0)
[blog](https://www.lydiahallie.com/blog/event-loop)

- [00:00](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=0.347#t=0.35) 视频简介
- [00:30](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=30.200167#t=30.20) Call Stack
- [01:15](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=75.47154#t=01:15.47) Single-threaded Problem
- [01:59](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=119.491738#t=01:59.49) Web APIs、浏览器环境
- [02:42](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=162.391326#t=02:42.39) Callback-based APIs
- [04:02](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=242.587872#t=04:02.59) Task Queue
- [04:35](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=275.374264#t=04:35.37) setTimeout
- [06:11](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=371.467821#t=06:11.47) Microtask Queue
- [08:37](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=517.350741#t=08:37.35) Promisifying Callbacks
- [08:53](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=533.216063#t=08:53.22) 测试题
- [10:49](https://www.bilibili.com/video/BV1iJ4m157Ya/?t=649.430338#t=10:49.43) 回顾

![[_posts/base/js/知识点总结/media/b8636af185b43b323ded5e088659bb02_MD5.jpeg]]
				上图与下图的区别，下图简略了JS Engine，只显示Call Stack
![[_posts/base/js/知识点总结/media/f95bce1abddc3ba4a993ddf3915e8836_MD5.jpeg]]

## Call Stack

- **定义**：浏览器的 Call Stack（调用栈）是一种*数据结构*，用于**跟踪在程序执行过程中函数的调用顺序**。它采用后进先出（LIFO）的原则，就像一叠盘子，最后放上去的盘子最先被拿下来。
- **栈帧（Stack Frame）**：当一个函数被调用时，会在调用栈中创建一个栈帧。栈帧包含了函数的*局部变量、参数以及函数执行完后返回的地址*等信息。例如，当调用一个函数`function add(a, b) { return a + b; }`时，在调用栈中创建的栈帧就会存储`a`和`b`的值以及函数返回的位置等。

`Call Stack` 管理我们程序的执行。当我们调用函数时，会创建一个新的*执行上下文*，并将其推送到 `Call Stack` 上。调用堆栈中最顶层的函数被评估，该函数又可以调用另一个函数，依此类推。当函数完成执行时，执行上下文将从 `Call Stack` 中弹出。

### JavaScript 是单线程的原因

**因为只使用单个`Call Stack`，所以 JavaScript 是单线程的。**

JavaScript 一次只能处理一项任务；如果一项任务需要很长时间才能完成，则无法处理其他任务。这意味着运行时间较长的任务会阻止任何其他任务的执行，从本质上冻结我们的程序！

## Web APIs
### 问题
单线程带来了问题。

### Callback-based APIs

## Task Queue


## Event Loop
Event Loop 只是 `JavaScript 运行时`中的一个很小的组件！参考上图。
`Event Loop`在处理异步任务中发挥着重要作用！因为，异步任务是基于 Event Loop 实现的。这很重要，因为 JavaScript 是单线程的 - 我们只使用单个`Call Stack`。

## Microtask Queue



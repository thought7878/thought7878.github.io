[YouTube 视频](https://www.youtube.com/watch?v=eiC58R16hb8&ab_channel=LydiaHallie)
[b 站视频](https://www.bilibili.com/video/BV1iJ4m157Ya/?spm_id_from=333.999.0.0&vd_source=22af953ea4c09540ad1966711a2d53f0)
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
上图与下图的区别，下图简略了 JS Engine，只显示 Call Stack
![[_posts/base/js/知识点总结/media/f95bce1abddc3ba4a993ddf3915e8836_MD5.jpeg]]

## Call Stack

- **定义**：浏览器的 Call Stack（调用栈）是一种*数据结构*，用于**跟踪在程序执行过程中函数的调用顺序**。它采用后进先出（LIFO）的原则，就像一叠盘子，最后放上去的盘子最先被拿下来。
- **栈帧（Stack Frame）**：当一个函数被调用时，会在调用栈中创建一个栈帧。栈帧包含了函数的*局部变量、参数以及函数执行完后返回的地址*等信息。例如，当调用一个函数`function add(a, b) { return a + b; }`时，在调用栈中创建的栈帧就会存储`a`和`b`的值以及函数返回的位置等。

`Call Stack` 管理我们程序的执行。当我们调用函数时，会执行函数的`call属性`，创建一个新的*执行上下文*，并将其推送到 `Call Stack` 上。调用堆栈中最顶层的函数被评估，该函数又可以调用另一个函数，依此类推。当函数完成执行时，执行上下文将从 `Call Stack` 中弹出。
![[Screen_Recording_2024-04-01_at_8.52.24_AM_icxgdw.mp4]]

### JavaScript 是单线程的原因

**因为只使用单个`Call Stack`，所以 JavaScript 是单线程的。**

JavaScript 一次只能处理一项任务；如果一项任务需要很长时间才能完成，则无法处理其他任务。这意味着运行时间较长的任务会阻止任何其他任务的执行，从本质上冻结我们的程序！
![[Screen_Recording_2024-04-01_at_8.53.49_AM_jjfaqa.mp4]]

## Web APIs

### 问题

单线程带来了问题。在一个实际的应用程序中，我们经常需要一些运行时间较长的任务。诸如*网络请求、计时器或任何基于用户输入的内容*。这是否意味着，当我们使用此类长时间运行的任务时，我们的整个应用程序会冻结？

```js
fetch("https://website.com/api/posts");
// We don't know when the data gets returned from the server...
// Would this be on the call stack until the data returns?
```

幸运的是，没有！此类异步编程功能，实际上并不是 JavaScript 本身的一部分。它通过`Web APIs`提供给我们。

### 是什么

`Web APIs` 是**一组与浏览器功能进行交互的接口**。这包括我们在使用 JavaScript 构建时经常使用的功能，例如`文档对象模型 DOM`、 `fetch` 、 `setTimeout` 等等。
`Web APIs`本质上**充当 JavaScript 运行时和浏览器功能之间的桥梁，使我们能够访问信息并使用超出 JavaScript 自身能力的功能**。

浏览器是一个强大的平台，有大量的功能。其中一些是我们构建应 ​​ 用程序所必需的，例如用于显示内容的*渲染引擎*或用于网络请求的*网络进程*。我们甚至可以访问一些较低级别的功能，例如设备的*传感器*、_摄像头_、*地理定位*等。
![[Screen_Recording_2024-03-31_at_9.42.46_AM_epb9jc.mp4]]


### Web APIs 与非阻塞的关系

但 Web APIs 与非阻塞任务有什么关系呢？

一些`Web APIs`允许我们启动异步任务，并**将运行时间较长的任务交给浏览器**！_调用此类 API 实际上只是将运行时间较长的任务交给浏览器环境，并设置处理程序来处理该任务的最终完成_。
![[Screen_Recording_2024-04-02_at_10.16.00_PM_fszooa.mp4]]

启动异步任务后（无需等待结果），执行上下文可以快速从`Call Stack`中弹出；它是非阻塞的！

有异步功能的`Web APIs`使用基于 callback-based 回调或基于 Promise 的方法。
![[_posts/base/js/知识点总结/media/80916770b52509fd3eb6392d64d5c9d3_MD5.jpeg]]

### Callback-based APIs

我们以[**Geolocation API**](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API)为例。在我们的网站上，我们希望访问用户的位置。
为此，我们可以使用`getCurrentPosition`方法，该方法接收两个回调：成功接收用户位置时使用的`successCallback` ，以及出现任何问题时使用的可选`errorCallback` 。
![[Screen_Recording_2024-03-31_at_10.16.27_AM_hm4xf8.mp4]]

调用此函数会将其新创建的执行上下文推送到`Call Stack`上。这实际上只是将其回调“注册”到 Web API，然后将操作交给浏览器。
然后该函数从`Call Stack`中弹出；现在是浏览器的责任。
![[Screen_Recording_2024-03-31_at_11.26.32_AM_kh2e27.mp4]]

在幕后，浏览器会提示用户允许网站访问其位置。
我们实际上并不知道用户何时会与我们的提示进行交互，也许他们分心了或者他们根本没有看到弹出窗口的出现。但这没问题！由于这一切都在后台发生， `Call Stack`仍然可以承担和执行其他任务。我们的网站保持响应性和互动性。

![[Screen_Recording_2024-03-31_at_11.38.48_AM_zah8j0.mp4]]

最后，用户允许我们的网站访问他们的位置。 API 接收来自浏览器的数据，并使用`successCallback`来处理结果。

![[Screen_Recording_2024-03-31_at_11.45.37_AM_zvseeb.mp4]]

*但是， `successCallback`不能简单地推送到`Call Stack`上，因为这样做可能会破坏已经运行的任务，从而导致不可预测的行为和潜在的冲突。* JavaScript 引擎一次处理一个任务，这确保了可预测且有组织的执行环境。

## Task Queue

上面的`successCallback`被添加到`Task Queue` （因此​​也称为`Callback Queue` ）。 **`Task Queue`保存等待在将来某个时刻执行的 Web API 回调和事件处理程序**。

**如下这些任务会被放入Task Queue ：**
- setTimeout与setInterval的callback function
- UI渲染任务（UI rendering tasks）
- 用户输入事件（User input events）
- 网络事件（Network Events）
![[Screen_Recording_2024-03-31_at_1.53.30_PM_s0nwe6.mp4]]

*现在`successCallback`位于任务队列中......但是它什么时候执行？*

## Event Loop

**`Event Loop`的责任：**
- *不断检查`Call Stack`是否为空*。
- 每当`Call Stack`为空（意味着当前没有正在运行的任务）时，它就会*从`Task Queue`中获取第一个可用任务，并将其移至`Call Stack` ，并在其中执行回调*。
![[Screen_Recording_2024-04-01_at_9.05.24_AM_gzb12b.mp4]]

Event Loop 只是 `JavaScript 运行时`中的一个很小的组件！参考上图。
*`Event Loop`在处理异步任务中发挥着重要作用！因为，异步任务是基于 Event Loop 实现的。这很重要，因为 JavaScript 是单线程的 - 我们只使用单个`Call Stack`。*

### 例子：setTimeout

另一个流行的基于回调的 Web API 是`setTimeout` 。每当我们调用`setTimeout`时，函数调用都会被推送到`Call Stack`上，该调用堆栈仅*负责启动具有指定延迟的计时器。在后台，浏览器会跟踪计时器*。
![[Screen_Recording_2024-03-31_at_8.24.17_PM_cwetjv.mp4]]
一旦计时器到期，计时器的`callback`就会在`Task Queue`中排队！重要的是要记住，延迟指定了回调被推送到`Task Queue` 而不是`Call Stack`之后的时间。这意味着*实际的执行延迟可能比传递给`setTimeout`指定延迟长*！如果`Call Stack`仍然忙于处理其他任务，则回调必须在`Task Queue`中等待。

到目前为止，我们已经了解了如何处理基于回调的 API。然而，大多数现代 Web API 使用**基于 Promise** 的方法，这些方法的处理方式有所不同。

## Microtask Queue

大多数现代 `Web APIs` 返回一个promise，通过链式 promise 的处理程序（或使用`await` ），而不是使用回调，来处理返回的数据。
```js
fetch("...")
  .then(res => ...)
  .catch(err => (...)
```

由于使用 Promise 处理数据，因此这些任务会使用 `Microtask Queue` ！
`Microtask Queue`是运行时中**比`Task Queue`优先级更高的**队列。

**如下这些任务会被放入 Microtask Queue：**
- `then(callback)` 、 `catch(callback)`和`finally(callback)`
- 在`await`之后执行的`async`函数
- `MutationObserver`回调函数（参考 [[MutationObserver]]）
- `queueMicrotask`回调函数（参考 [[queueMicrotask()]]）

### 微任务与宏任务的优先级、执行顺序

当`Call Stack`为空时， `Event Loop`首先处理`Microtask Queue`中的所有微任务，然后才是`Task Queue` 中的一个宏任务。
完成`Task Queue`中的单个任务后，并且`Call Stack`为空， `Event Loop`会再次处理`Microtask Queue`中的所有微任务，然后再继续执行`Task Queue`中的下一个宏任务。

**这可确保立即处理与刚刚完成的宏任务相关的微任务，从而保持程序的响应能力和一致性**。

### 例子：fetch
一个流行的基于 Promise 的 API 是`fetch` 。当我们调用`fetch`时，它的执行上下文被添加到`Call Stack`中。
调用`fetch`在内存中创建一个Promise 对象，默认情况下该对象处于`"pending"` 。发起网络请求后， `fetch`函数调用将从`Call Stack`中弹出。
![[Screen_Recording_2024-04-01_at_2.34.43_PM_hn7zy3.mp4]]
引擎遇到`then`处理程序，该处理程序创建存储在`PromiseFulfillReactions`中的**PromiseReaction**记录。
![[Screen_Recording_2024-04-01_at_2.35.23_PM_fhrvwh.mp4]]
接下来， `console.log`被推送到`Call Stack` ，并将`End of script`记录到控制台。在这种情况下，网络请求仍然处于待处理状态。
![[Screen_Recording_2024-04-01_at_2.38.37_PM_belndv.mp4]]
当服务器最终返回数据时， `[[PromiseStatus]]`设置为`"fulfilled"` ， `[[PromiseResult]]`设置为`Response`对象。当 Promise 解析时， **PromiseReaction**被推送到`Microtask Queue`上。
当`Call Stack`为空时， `Event Loop`将回调从`Microtask Queue`移动到`Call Stack` ，执行它，输出`Response`对象。
![[Screen_Recording_2024-04-01_at_2.39.20_PM_lhccgg.mp4]]

## Promise化基于回调的Web API
为了增强可读性并管理基于回调的 Web API 中的异步操作流程，我们可以将它们包装在 Promise 中。
例如，我们可以将 Geolocation API 的基于回调的`getCurrentPosition`方法包装在`Promise`构造函数中。
```js
function getCurrentPosition() {
  return new Promise((resolve, reject) => {
    navigator.geolocation.getCurrentPosition(resolve, reject);
  });
}
```
这充分利用了 Promise 的全部功能，例如更好的可读性和`async` / `await`语法的使用。
```js
async function fetchAndLogCurrentPosition() {
  try {
    const position = await getCurrentPosition();
    console.log(position);
  } catch (error) {
    console.log(error);
  }
}

// function fetchAndLogCurrentPosition() {
//   getCurrentPosition()
//     .then((position) => console.log(position))
//     .catch((error) => console.error(error));
// }
```

我们甚至可以使用`setTimeout`创建一个基于 Promise 的计时器来推迟代码块的执行，直到计时器到期：
```js
function delay(ms) {
  return new Promise(resolve => setTimeout(resolve, ms))
}

async function doStuff() {
  // Perform tasks...
  await delay(5000);
  // Perform the rest after 5 seconds
}
```


## 总结
了解`Event Loop` 、 `Task Queue`和`Microtask Queue`如何协同工作对于掌握异步、非阻塞 JavaScript 非常重要。使 JavaScript 能够在单线程环境中处理复杂的异步行为。

*所有 Web API 都是异步处理的吗？*
不是，只是启动异步操作的那些API。其他方法（例如`document.getElementById()`或`localStorage.setItem()`是同步处理的。

*总结上面的内容：*
- JavaScript 是单线程的，这意味着它一次只能处理一项任务。
- `Web APIs`用于与浏览器的功能进行交互。其中一些 API 允许我们在后台启动异步任务。
- 启动异步任务的函数调用被添加到`Call Stack`中，但这只是将其交给浏览器。实际的异步任务在后台处理，并且启动的函数不会保留在`Call Stack`上。
- 基于回调的`Web APIs`使用`Task Queue`，在异步任务完成后，将回调排入Task Queue。
- `Microtask Queue`由 Promise 处理程序、 `await`之后的`async`函数、 `MutationObserver`回调和`queueMicrotask`回调使用。该队列的优先级高于`Task Queue` 。
- 当`Call Stack`为空时， `Event Loop`首先从`Microtask Queue`中获取任务，直到该队列完全为空。然后，它获取`Task Queue` ，将第一个可用任务移至`Call Stack` 。处理完第一个可用任务后，它会通过再次检查`Microtask Queue`来“重新开始”。

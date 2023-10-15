## [前端监控训练营Day2-前端性能指标详解](https://www.bilibili.com/video/BV18S4y1H7Vs/?spm_id_from=333.337.top_right_bar_window_history.content.click&vd_source=22af953ea4c09540ad1966711a2d53f0)

### Web性能指标的诞生（00:42）

### 传统的性能指标（01:50）

#### navigation-timing 指标（02:33）

[Navigation Timing](https://www.w3.org/TR/navigation-timing/#process)

[Navigation Timing Level 2](https://www.w3.org/TR/navigation-timing-2/#processing-model)

![Timing attributes](https://www.w3.org/TR/navigation-timing/timing-overview.png)

<img title="" src="https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_6f529d3a0675dbeec1aa1fc6d16b25de" alt="" width="726">

![image]()

##### 初始化阶段（没有真正发请求）

- navigationStart：用户完成卸载前一个文档（document，前一个页面）的时间点。

- redirectStart：页面重定向时的开始时间（如果存在重定向的话）或者是0。

- redirectEnd：如果存在重定向的话，redirectEnd表示最后一次重定向后服务器端response的数据被接收完毕的时间。否则的话就是0。

##### 请求阶段

- fetchStart：浏览器发起资源请求时，如果有缓存，则返回读取缓存的开始时间。

- domainLookupStart：查询DNS的开始时间。如果请求没有发起DNS请求，如keep-alive，缓存等，则返回fetchStart的时间点。

- domainLookupEnd：查询DNS的结束时间。如果没有发起DNS请求，如keep-alive，缓存等，则返回fetchStart的时间点。

- connectStart：当浏览器开始与服务器连接时的时间。如果没有建立连接，如请求是keep-alive、缓存等，那么它的值等同于domainLookupEnd。

- secureConnectionStart：如果页面使用HTTPS，它的值是安全连接握手之前的时刻。如果该属性不可用，则返回undefined。如果该属性可用，但没有使用HTTPS，则返回0。

- connectEnd：当浏览器端完成与服务器端建立连接的时刻。如果没有建立连接，如请求是keep-alive、缓存等，那么它的值等同于domainLookupEnd。

- responseStart：指客户端收到从服务器端（或缓存、本地资源）响应回的第一个字节的数据的时刻。

- responseEnd：指客户端收到从服务器端（或缓存、本地资源）响应回的最后一个字节的数据的时刻。

##### 解析渲染阶段

- domLoading：浏览器即将开始解析第一批收到的 HTML 文档字节。

- domInteractive：浏览器<u>完成对所有 HTML 的解析</u>并且 DOM 构建完成的时间点， 是DOM 准备就绪的时间点。

- domContentLoadedEventStart：将用户代理在文档中触发 DOMContentLoaded 事件之前的时间记录为 [domContentLoadedEventStart]([Navigation Timing](https://www.w3.org/TR/navigation-timing/#process))

- domContentLoaded：DOM 准备就绪并且没有样式表阻止 JavaScript 执行的时间点，可以开始构建渲染树，一般表示 [DOM 和 CSSOM 均准备就绪](http://calendar.perfplanet.com/2012/deciphering-the-critical-rendering-path/)的时间点。

- domContentLoadedEventEnd：DOMContentLoaded 事件完成后立即将时间记录为 [domContentLoadedEventEnd]([Navigation Timing](https://www.w3.org/TR/navigation-timing/#process))

- domComplete：顾名思义，所有处理完成，<u>并且网页上的所有资源（图像等）都已下载完毕</u>，也就是说，加载转环已停止旋转, <u>表示网页及其所有子资源都准备就绪的时间点</u>。

- loadEventStart：作为每个网页加载的最后一步，浏览器会触发 `onload` 事件，以便触发额外的应用逻辑。

- loadEventEnd：`onload` 事件 执行完成。  
  许多 JavaScript 框架都会等待`onload`事件发生后，才开始执行它们自己的逻辑。因此，浏览器会捕获 `loadEventStart` 和 `loadEventEnd` 时间戳来追踪执行所花费的时间。

#### 各阶段指标的计算方式和含义

##### 实际运用中的常见指标

- LOAD

LOAD，<u>反映页面及其依赖资源全部加载完成的时间</u>。在服务端路由场景下，LOAD 指标对应页面 load 事件发生的时间。根据 Navigation Timing 2.0 定义的页面加载阶段模型，同步跳转场景下，LOAD 的计算方式为：LOAD = loadEventStart - fetchStart（疑问：loadEventEnd）。

- Dom Ready：domContentLoadedEventEnd - fetchStart

执行完所有同步脚本的耗时

- Dom解析：domInteractive - responseEnd

从请求响应结束至Dom可交互的耗时

- TTFB：responseStart-navigationStart

TTFB（Time To First Byte），即首字节网络请求耗时，发出页面请求到接收到应答数据第一个字节的时间。

- ResourceLoad：loadEventStart - domContentLoadedEventEnd

Dom解析完后，剩余资源加载的耗时

##### 其他指标

- Redirect：redirectEnd - redirectStart

重定向没有发生，或者其中一个重定向非同源，则该值为 0

- HTTP/App cache：domainLookupStart - fetchStart

缓存

- DNS：domainLookupEnd - domainLookupStart

域名解析耗时（命中解析缓存时为0）

- TCP：connectEnd - connectStart

TCP连接耗时

- SSL：connectEnd - secureConnectionStart

SSL安全连接耗时

- Request：responseStart - requestStart

在SSL链接建立后，从客户端发送至服务端首次响应的耗时

- Response：responseEnd - responseStart

从服务端首次响应至数据完全响应完的耗时

- Processing：domComplete - domLoading

Dom解析开始至结束的耗时

#### 如何获取指标数据

navigation timing：window.performance.timing

navigation timing 2：window.performance.getEntriesByType('navigation')

### 以用户为中心的性能指标（05:15）

#### Why/传统性能指标的问题

传统的性能指标专注于容易衡量的技术细节，但是他们**很难反映出用户真正关心的是什么**。**如果仅仅是把加载速度优化的更快，很快就会发现网站的用户体验依然很差**。

**这就是创建用户为中心的性能指标的原因**，它们专注于用户视角下的浏览体验。

#### 指标分类

| 用户体验   | 指标                                           |
| ------ | -------------------------------------------- |
| 发生了吗？  | FP(First Paint), FCP(First Contentful Paint) |
| 内容有用吗？ | FMP(First Meaningful Paint), SI(Speed Index) |
| 内容可用吗？ | TTI(Time To Interactive)                     |
| 令人愉悦吗？ | FID(First Input Delay)                       |

**以用户为中心的性能指标，衡量页面显示有用内容的速度，用户是否可以与之交互，这些交互是否流畅且无延迟。**

#### FP & FCP

[First Contentful Paint (FCP)-google](https://web.dev/articles/fcp?hl=zh-cn)

https://www.volcengine.com/docs/6431/107441

### 如何根据性能指标衡量站点满意度（:）

### 如何获取站点的性能指标 （:）

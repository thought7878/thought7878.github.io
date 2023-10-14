

## [前端监控训练营Day2-前端性能指标详解](https://www.bilibili.com/video/BV18S4y1H7Vs/?spm_id_from=333.337.top_right_bar_window_history.content.click&vd_source=22af953ea4c09540ad1966711a2d53f0)

### Web性能指标的诞生（00:42）



### 传统的性能指标（01:50）

#### navigation-timing 指标（02:33）

[Navigation Timing](https://www.w3.org/TR/navigation-timing/#process)

[Navigation Timing Level 2](https://www.w3.org/TR/navigation-timing-2/#processing-model)

![Timing attributes](https://www.w3.org/TR/navigation-timing/timing-overview.png)

![](https://lf6-volc-editor.volccdn.com/obj/volcfe/sop-public/upload_6f529d3a0675dbeec1aa1fc6d16b25de)



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

- domInteractive：浏览器完成对所有 HTML 的解析并且 DOM 构建完成的时间点， 是DOM 准备就绪的时间点。

- domContentLoaded：DOM 准备就绪并且没有样式表阻止 JavaScript 执行的时间点，可以开始构建渲染树，一般表示 [DOM 和 CSSOM 均准备就绪](http://calendar.perfplanet.com/2012/deciphering-the-critical-rendering-path/)的时间点。

- domComplete：顾名思义，所有处理完成，并且网页上的所有资源（图像等）都已下载完毕，也就是说，加载转环已停止旋转, 表示网页及其所有子资源都准备就绪的时间点。

- loadEventStart：作为每个网页加载的最后一步，浏览器会触发 `onload` 事件，以便触发额外的应用逻辑。

- loadEventEnd：`onload` 事件 执行完成。  
  许多 JavaScript 框架都会等待`onload`事件发生后，才开始执行它们自己的逻辑。因此，浏览器会捕获 `loadEventStart` 和 `loadEventEnd` 时间戳来追踪执行所花费的时间。

### 以用户为中心的性能指标（:）

### 如何根据性能指标衡量站点满意度（:）

### 如何获取站点的性能指标 （:）



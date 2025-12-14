【【前端杂谈10】渲染页面的 N 种姿势 | 从模板引擎到新式服务端渲染】 https://www.bilibili.com/video/BV1NW5NzVE5F/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862


# 如何渲染页面？

![[_posts/architect/渲染架构/media/15cab83d093572d5a5021cf0a4b53b49_MD5.jpeg]]


## MVC架构

![[_posts/architect/渲染架构/media/a71db297e17f8e527efcacbb77783522_MD5.jpeg]]


## 模版渲染
早期的服务端渲染

![[_posts/architect/渲染架构/media/653cef2be8aaa4a2c96bfe5d4366c316_MD5.jpeg]]


## 客户端渲染
将服务端MVC中的V，独立到前端。前端使用JS直接在浏览器中渲染更复杂的页面。
![[_posts/architect/渲染架构/media/b0a7944c30a2ee959c216c92639f9220_MD5.jpeg]]

### 单页应用架构
**单页应用架构（客户端渲染的常见做法）：**
- 服务端只提供带有JS引用的基本HTML骨架
- 浏览器只在App初始时加载HTML，再加载JS，再执行JS，获取数据、渲染页面
- 前后端通信，使用异步HTTP接口（REST API、GraphQL）
- 后续所有路由切换、交互处理，均由前端负责
![[_posts/architect/渲染架构/media/9ea840a73fc05bfaee971ab840972b7e_MD5.jpeg]]

### 框架、库

![[_posts/architect/渲染架构/media/667e857d3494312dd91d2631f3b45888_MD5.jpeg]]


### 客户端渲染的好处
![[_posts/architect/渲染架构/media/f237af7a4c65bcd5327c210e33492c6e_MD5.jpeg]]


### 客户端渲染的坏处

![[_posts/architect/渲染架构/media/10c1c1241615d2495064daaa493f9ca1_MD5.jpeg]]

## 新一代的服务端渲染
**因为客户端渲染的缺点，是否可以结合客户端渲染、服务端渲染的优点？**
**可以只让服务端做首屏渲染，客户端接管后续的渲染**

### 服务端渲染的核心做法
**服务端渲染的核心做法：**
- 服务端将数据和页面结构一起渲染到HTML主文档中，随即发送到客户端。
- 浏览器将立即渲染HTML页面，但此时页面尚不能进行交互。
- 直到通过JS为页面元素绑定交互事件（`hydrate 水合/补水`）

![[_posts/architect/渲染架构/media/e734c250d7975a7e5ec1734d36f9502b_MD5.jpeg]]

![[_posts/architect/渲染架构/media/ea69edcf6a5e25b01421b72a6778365e_MD5.jpeg]]

![[_posts/architect/渲染架构/media/0113db656c60040d4d4e8a993b67296c_MD5.jpeg]]

### 同构设计
`同构设计 （Isomorphic Architecture）`，前端框架，既要能运行在客户端，还要能运行在服务端

![[_posts/architect/渲染架构/media/c829b4bc3107a0406fd3ebca7d51df16_MD5.jpeg]]

以React+Express为例：

![[_posts/architect/渲染架构/media/1041deffba3f45871dc9f914e6be7870_MD5.jpeg]]

## 服务端渲染的变体
为了进一步提升服务端渲染的效率和终端用户体验，服务端渲染技术又演化出了许多变体

### SSG
`静态站点生成（Static Site Generation）`是一种服务端***在构建时*预先生成多个静态页面的方案**。
适用于内容固定的博客、资讯、CMS系统。
它减少了服务端运行时的资源消耗。
典型框架有Gatesby、Hugo、Hexo。

![[_posts/architect/渲染架构/media/69919abf3ca9009c0064f02ad8358b4f_MD5.jpeg]]

### ISR
`增量静态再生（ISR，Incremental Static Regeneration）`是一种**对SSG的改进**，相比于SSG在构建阶段生成页面，ISR可以在内容发生变化时重新渲染，而不需要跑一次构建发布流程。
支持ISR的典型框架有Next.js、Nuxt.js等。

![[_posts/architect/渲染架构/media/1c34ba37c3d67417efcb1e86d5c120e1_MD5.jpeg]]


### 流式渲染
`流式渲染（SSR with Streaming）`是*先将渲染好的部分发送给客户端，再将后渲染的发送给客户端*。一种**对传统SSR的改进**，传统SSR会渲染整个HTML，而使用Suspense组件可以转变成流式渲染

例子，服务端可以*先将渲染好的文章组件*发送给客户端，*评论组件*则进行**异步渲染、流式传输**
![[_posts/architect/渲染架构/media/63a1a524bd81f6fd7a93c1e307277451_MD5.jpeg]]

![[_posts/architect/渲染架构/media/b47880adccbcbb534ad4092ff4eb6435_MD5.jpeg]]

### RSC
`React服务器组件（React Server Component）`是React框架特有的一种组件，可以**在服务端和客户端之间传输特定格式的数据（非HTML代码）**，看起来如下图。
相比直接传输HTML，*RSC可以实现更高效的服务端渲染和网络传输*。

![[_posts/architect/渲染架构/media/2ce7d563a99c76ee0d70b421e7325afe_MD5.jpeg]]

和它对应的是`客户端组件（CSC Client Side Component）`。


### PPR
还有一种**更先进的服务端渲染方案**。
`部分预渲染（PPR，Partial PreRendering）`**把动静结合发挥到了极致，整合了ISR、RSC、流式渲染的特性，最大程度保证了用户体验**。
当前只有Next.js。
图中*紫色部分*即为ISR渲染好的静态内容，*蓝色部分*则为用户请求到来时才渲染的动态内容。

![[_posts/architect/渲染架构/media/22066f3ea862cc571eb8f841e9982d8c_MD5.jpeg]]
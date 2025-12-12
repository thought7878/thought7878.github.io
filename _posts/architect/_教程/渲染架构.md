---
title: "Rendering on the Web  |  Articles"
source: "https://web.dev/articles/rendering-on-the-web#client-side"
author:
  - "[[web.dev]]"
published:
created: 2025-12-10
description: "Recommendations for implementing logic and rendering in apps."
tags:
  - "clippings"
---
参考资料：[【前端杂谈10】渲染页面的 N 种姿势 | 从模板引擎到新式服务端渲染](https://www.bilibili.com/video/BV1NW5NzVE5F/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)

One of the core decisions web developers must make is where to implement logic and rendering in their application. This can be difficult because there are so many ways to build a website.  
网页开发者必须做出的**核心决策之一**是确定在应用程序中**实施逻辑和渲染的位置**。这可能会很困难，因为构建网站的方法有很多。

Our understanding of this space is informed by our work in Chrome talking to large sites over the past few years. Broadly speaking, we encourage developers to consider server-side rendering or static rendering over a full rehydration approach.  
我们对这个领域的理解来自于过去几年我们在 Chrome 中与大型网站合作的工作。总的来说，*我们鼓励开发者考虑使用服务器端渲染或静态渲染，而不是采用完整的重新激活方法*。

To better understand the architectures we're choosing from when we make this decision, we need [consistent terminology](https://web.dev/articles/#terminology) and a shared framework for each approach. Then, you can better evaluate the tradeoffs of each rendering approach from the perspective of page performance.  
*为了更好地理解我们在做出这个决定时选择的基础架构，我们需要为每种方法提供一致的术语和共享的框架。这样，你就能从页面性能的角度更好地评估每种渲染方法的权衡*。

# 术语
Terminology

First, we define some terminology we will use.  
首先，我们定义一些我们将使用的术语。

## 渲染
Rendering 

### Server-side rendering (SSR)  
服务器端渲染（SSR）

Rendering an app on the server to send HTML, rather than JavaScript, to the client.  
在服务器上渲染应用程序以发送 HTML 而不是 JavaScript 到客户端。

### Client-side rendering (CSR)  
客户端渲染（CSR）

Rendering an app in a browser, using JavaScript to modify the DOM.  
在浏览器中渲染应用，使用 JavaScript 修改 DOM。

### Prerendering 
预渲染

Running a client-side application at build time to capture its initial state as static HTML.  
在构建时运行客户端应用程序，将其初始状态捕获为静态 HTML。

### Hydration 
水合

Running client-side scripts to add application state and interactivity to server-rendered HTML. Hydration assumes the DOM doesn't change.  
在客户端运行脚本以向服务器渲染的 HTML 添加应用状态和交互性。水合假设 DOM 不会改变。

### Rehydration 
重新水合

While often used to mean the same thing as hydration, rehydration implies regularly updating the DOM with the latest state, including after the initial hydration.  
虽然通常用来表示与水合相同的意思，但重新水合意味着定期用最新状态更新 DOM，包括初始水合之后。

## Performance 性能

[Time to First Byte (TTFB)  首次字节时间（TTFB）](https://web.dev/articles/ttfb)

The time between clicking a link and the first byte of content loading on the new page.  
点击链接到新页面上内容开始加载的第一个字节之间的时间。

[First Contentful Paint (FCP)  首次内容绘制（FCP）](https://web.dev/articles/fcp)

The time when requested content (article body, etc) becomes visible.  
请求的内容（如文章正文等）变得可见的时间。

[Interaction to Next Paint (INP)  交互到下一次绘制（INP）](https://web.dev/articles/inp)

A representative metric that assesses whether a page consistently responds quickly to user inputs.  
一个代表性指标，用于评估页面是否始终能快速响应用户输入。

[Total Blocking Time (TBT)  总阻塞时间（TBT）](https://web.dev/articles/tbt)

A [proxy metric for INP](https://almanac.httparchive.org/en/2022/performance#inp-and-tbt) that calculates how long the main thread was blocked during page load.  
一个用于 INP 的代理指标，计算页面加载期间主线程被阻塞的时间。

# Server-side rendering 
服务器端渲染

Server-side rendering generates the full HTML for a page on the server in response to navigation. This avoids additional round trips for data fetching and templating on the client, because the renderer handles them before the browser gets a response.  
`服务器端渲染`**在服务器上生成页面的完整 HTML**。**这避免了**客户端*在数据获取和模板处理上的额外往返*（先获取模版，再获取数据），因为渲染器在浏览器收到响应之前就处理了这些任务。

Server-side rendering generally produces a fast FCP. Running page logic and rendering on the server lets you avoid sending lots of JavaScript to the client. This helps to reduce a page's TTBT, which can also lead to a lower INP, because the main thread isn't blocked as often during page load. When the main thread is blocked less often, user interactions have more opportunities to run sooner.  
服务器端渲染*通常能产生快速的 FCP*。**在服务器上运行页面逻辑和渲染可以避免向客户端发送大量 JavaScript**。*这有助于减少页面的 TTBT，也可能导致较低的 INP*，**因为主线程在页面加载期间不会被频繁阻塞（渲染工作在服务器完成了，不占用主线程了）。当主线程阻塞次数减少时，用户交互有更多机会更快地运行**。

This makes sense, because with server-side rendering, you're really just sending text and links to the user's browser. This approach can work well for a variety of device and network conditions and opens up interesting browser optimizations, such as streaming document parsing.  
这是有道理的，**因为使用服务器端渲染，你实际上只是向用户的浏览器发送文本和链接**。这种方法*在各种设备和网络条件下都能很好地工作，并开启了有趣的浏览器优化，例如流式文档解析*。

![[_posts/architect/_教程/media/fed96421230f1bf815b8d1c00717ddde_MD5.png]]

		FCP and TTI with server-side rendering. FCP 和 TTI 与服务器端渲染。

With server-side rendering, users are less likely to be left waiting for CPU-bound JavaScript to run before they can use your site. Even when you can't avoid [third-party JavaScript](https://web.dev/articles/optimizing-content-efficiency-loading-third-party-javascript), using server-side rendering to reduce your own first-party [JavaScript costs](https://medium.com/@addyosmani/the-cost-of-javascript-in-2018-7d8950fbb5d4) can give you more [budget](https://medium.com/@addyosmani/start-performance-budgeting-dabde04cf6a3) for the rest. However, there is one potential trade-off with this approach: generating pages on the server takes time, which can increase your page's TTFB.  
使用服务器端渲染，用户在访问你的网站时不太可能需要等待 CPU 密集型 JavaScript 执行才能使用。即使你无法避免第三方 JavaScript，使用服务器端渲染来减少你自己的第一方 JavaScript 成本，也能为你留下更多预算用于其他方面。然而，*这种方法有一个潜在的权衡：在服务器上生成页面需要时间，这可能会增加你页面的 TTFB*（Time to First Byte）。

Whether server-side rendering is enough for your application largely depends on what type of experience you're building. There's a long-standing debate over the correct applications of server-side rendering versus client-side rendering, but you can always choose to use server-side rendering for some pages and not others. Some sites have adopted hybrid rendering techniques with success. For example, [Netflix](https://medium.com/dev-channel/a-netflix-web-performance-case-study-c0bcde26a9d9) server-renders its relatively static landing pages, while [prefetching](https://dev.to/addyosmani/speed-up-next-page-navigations-with-prefetching-4285) the JavaScript for interaction-heavy pages, giving these heavier client-rendered pages a better chance of loading quickly.  
服务器端渲染是否足够满足你的应用需求，很大程度上取决于你正在构建什么样的体验。*关于服务器端渲染与客户端渲染的正确应用方式一直存在长期争论*，但你**可以选择仅对某些页面使用服务器端渲染，而其他页面则不使用。一些网站已经成功采用了混合渲染技术。例如**，Netflix 对其**相对静态的首页**采用服务器端渲染，同时**预取交互密集型页面的 JavaScript**，让这些较重的客户端渲染页面*有更好的机会快速加载*。

With many modern frameworks, libraries, and architectures, you can render the same application on both the client and the server. You can use these techniques for server-side rendering. However, architectures where rendering happens both on the server *and* on the client are their own class of solution with very different performance characteristics and tradeoffs. React users can use [server DOM APIs](https://react.dev/reference/react-dom/server) or solutions built on them like [Next.js](https://nextjs.org/) for server-side rendering. Vue users can use Vue's [server-side rendering guide](https://vuejs.org/guide/scaling-up/ssr.html) or [Nuxt](https://nuxtjs.org/). Angular has [Universal](https://angular.io/guide/universal).  
在现代许多框架、库和架构中，你可以在客户端和服务器上渲染相同的应用程序。你可以使用这些技术进行服务器端渲染。然而，渲染既在服务器上又在前端进行的架构属于独立的解决方案类别，具有非常不同的性能特征和权衡。React 用户可以使用服务器 DOM API 或基于它们的解决方案（如 `Next.js`）进行服务器端渲染。Vue 用户可以使用 Vue 的服务器端渲染指南或 `Nuxt`。Angular 有 `Universal`。

Most popular solutions use some form of hydration, though, so be aware of the approaches your tool uses.  
然而，*大多数流行的解决方案都使用某种形式的 hydration*，所以要注意你的工具使用的方法。

# Static rendering 
静态渲染

[Static rendering](https://frontarm.com/james-k-nelson/static-vs-server-rendering/) happens at build time. This approach offers a fast FCP, and also a lower TBT and INP, as long as you limit the amount of client-side JavaScript on your pages. Unlike server-side rendering, it also achieves a consistently fast TTFB, because the HTML for a page doesn't have to be dynamically generated on the server. Generally, static rendering means producing a separate HTML file for each URL ahead of time. With HTML responses generated in advance, you can deploy static renders to multiple CDNs to take advantage of edge caching.  
`静态渲染`**发生在*构建时***。这种方法**提供快速 FCP，同时具有较低的 TBT 和 INP**，只要限制页面上的客户端 JavaScript 数量。*与服务器端渲染不同，它还实现了始终如一的快速 TTFB，因为页面的 HTML 不需要在服务器上动态生成*。通常，`静态渲染`意味着**提前为每个 URL 生成一个单独的 HTML 文件**。*通过预先生成 HTML 响应，您可以部署静态渲染到多个 CDN 以利用边缘缓存*。

![[_posts/architect/_教程/media/7366be32000cd021087f143f1b1ac72b_MD5.png]]

		FCP and TTI with static rendering. 静态渲染下的 FCP 和 TTI

Solutions for static rendering come in all shapes and sizes. Tools like [Gatsby](https://www.gatsbyjs.org/) are designed to make developers feel like their application is being rendered dynamically, not generated as a build step. Static site generation tools such as [11ty](https://www.11ty.dev/),[Jekyll](https://jekyllrb.com/), and [Metalsmith](https://metalsmith.iooperationalizing-node-js-for-server-side-rendering-c5ba718acfc9speedier-server-side-rendering-in-react-16-with-component-caching-e8aa677929b1/) embrace their static nature, providing a more template-driven approach.  
**静态渲染方案多种多样**。像 `Gatsby` 这样的工具旨在让开发者感觉应用是动态渲染的，而不是作为构建步骤生成的。**静态网站生成工具**如 `11ty`、`Jekyll` 和 `Metalsmith` 则拥抱其静态特性，提供更模板驱动的方案。

One of the downsides to static rendering is that it must generate individual HTML files for every possible URL. This can be challenging or even infeasible when you need to predict those URLs ahead of time and for sites with a large number of unique pages.  
**静态渲染的一个缺点**是*必须为每个可能的 URL 生成单独的 HTML 文件。当你需要提前预测这些 URL，或对于具有大量独特页面的网站来说，这可能具有挑战性甚至不可行*。

React users might be familiar with Gatsby,[Next.js static export](https://nextjs.org/learn/excel/static-html-export/), or [Navi](https://frontarm.com/navi/), all of which make it convenient to create pages from components. However, static rendering and prerendering behave differently: statically rendered pages are interactive without needing to execute much client-side JavaScript, whereas prerendering improves the FCP of a Single Page Application that must be booted on the client to make pages truly interactive.  
React 用户可能熟悉 Gatsby、Next.js 静态导出或 Navi，它们都使从组件创建页面变得方便。然而，**静态渲染和预渲染的行为不同**：*`静态渲染`的页面无需执行大量客户端 JavaScript 即可交互，而`预渲染`则通过在客户端启动单页应用程序来提高其 FCP，使页面真正交互*。

If you're unsure whether a given solution is static rendering or prerendering, try disabling JavaScript and load the page you want to test. For statically rendered pages, most interactive features still exist without JavaScript. Prerendered pages might still have some basic features like links with JavaScript disabled, but most of the page is inert.  
如果你**不确定某个解决方案是静态渲染还是预渲染**，可以*尝试禁用 JavaScript 并加载你想要测试的页面*。**对于静态渲染的页面**，即使没有 JavaScript，*大多数交互功能仍然存在*。**预渲染的页面**可能仍然有一些基本功能，比如禁用 JavaScript 后的链接，但页面的*大部分内容是无效的*。

Another useful test is to use [network throttling in Chrome DevTools](https://developer.chrome.com/docs/devtools/device-mode/#network) and see how much JavaScript downloads before a page becomes interactive. Prerendering generally needs more JavaScript to become interactive, and that JavaScript tends to be more complex than the [progressive enhancement](https://developer.mozilla.org/docs/Glossary/Progressive_Enhancement) approach used in static rendering.  
**另一个有用的测试方法**是使用 Chrome DevTools 中的网络限速功能，*查看页面变得可交互之前需要下载多少 JavaScript*。`预渲染`通常需要更多的 JavaScript 才能变得可交互，而且这些 JavaScript 往往比静态渲染中使用的渐进增强方法更复杂。

# Server-side rendering VS static rendering
服务器端渲染与静态渲染

Server-side rendering isn't the best solution for everything, because its dynamic nature can have significant compute overhead costs. Many server-side rendering solutions don't flush early, delay TTFB, or double the data being sent (for example, inlined states used by JavaScript on the client). In React,`renderToString()` can be slow because it's synchronous and single-threaded.[Newer React server DOM APIs](https://react.dev/reference/react-dom/server) support streaming, which can get the initial part of an HTML response to the browser sooner while the rest of it is still being generated on the server.  
**服务器端渲染并非适用于所有场景，因为*其动态特性会导致显著的计算开销成本***。许多服务器端渲染方案不会提前刷新，延迟 TTFB（首次内容呈现时间），或使传输数据量加倍（例如，客户端 JavaScript 使用的内联状态）。在 React 中， renderToString() 可能会很慢，因为它同步且单线程。较新的 React 服务器 DOM API 支持`流式传输`，*可以在其余部分仍在服务器上生成时，更快地将 HTML 响应的初始部分发送到浏览器*。

Getting server-side rendering "right" can involve finding or building a solution for [component caching](https://medium.com/@reactcomponentcaching/), managing memory consumption, using [memoization](https://speakerdeck.com/maxnajim/hastening-react-ssr-with-component-memoization-and-templatization) techniques, and other concerns. You're often processing or rebuilding the same app twice, once on the client and once on the server. Server-side rendering showing content sooner doesn't necessarily give you less work to do. If you have a lot of work on the client after a server-generated HTML response arrives on the client, this can still lead to higher TBT and INP for your website.  
**要正确实现服务器端渲染**，*可能需要找到或构建组件缓存解决方案、管理内存消耗、使用记忆化技术以及其他相关考虑*。你通常*需要处理或重建两次相同的 App，一次在客户端，一次在服务器端*。服务器端渲染提前显示内容并不一定意味着你需要做的工作更少。*如果在服务器生成的 HTML 响应到达客户端后，客户端仍有大量工作需要处理*，这仍然可能导致你的网站 TBT（首次文本绘制时间）和 INP（交互式内容性能）更高。

Server-side rendering produces HTML on demand for each URL, but it can be slower than just serving static rendered content. If you can put in the additional legwork, server-side rendering plus [HTML caching](https://freecontent.manning.com/caching-in-react/) can significantly reduce server render time. The upside to server-side rendering is the ability to pull more "live" data and respond to a more complete set of requests than is possible with static rendering. Pages that need personalization are a concrete example of the type of request that doesn't work well with static rendering.  
*服务器端渲染会根据每个 URL 按需生成 HTML，但可能比直接提供静态渲染内容要慢*。如果你愿意付出额外的努力，*服务器端渲染结合 HTML 缓存可以显著减少服务器渲染时间*。**服务器端渲染的优势**在于能够获取更多"实时"数据，并响应比静态渲染更完整的请求集。需要*个性化处理的页面就是静态渲染无法良好处理*的请求类型的一个具体例子。

Server-side rendering can also present interesting decisions when building a [PWA](https://web.dev/explore/progressive-web-apps). Is it better to use full-page [service worker](https://developer.chrome.com/docs/workbox/service-worker-overview) caching, or server-render individual pieces of content?  
在构建 PWA 时，服务器端渲染也会带来有趣的决策。是使用全页service worker缓存，还是服务器渲染单个内容片段更好？

# Client-side rendering 
客户端渲染

Client-side rendering means rendering pages directly in the browser with JavaScript. All logic, data fetching, templating, and routing are handled on the client instead of on the server. The effective outcome is that more data is passed to the user's device from the server, and that comes with its own set of tradeoffs.  
`客户端渲染`是指**使用 JavaScript 直接在浏览器中渲染页面**。**所有逻辑、数据获取、模板处理、路由，都在客户端处理，而不是在服务器端**。其有效结果是服务器向用户设备传递了更多数据，这也带来了一套自己的权衡。

Client-side rendering can be difficult to make and keep fast for mobile devices. With a little work to keep a [tight JavaScript budget](https://mobile.twitter.com/HenrikJoreteg/status/1039744716210950144) and deliver value in as few [round-trips](https://en.wikipedia.org/wiki/Round-trip_delay_time) as possible, you can get client-side rendering to almost replicate the performance of pure server-side rendering. You can get the parser to work for you faster by delivering critical scripts and data using `<link rel=preload>` We also recommend considering using patterns like [PRPL](https://web.dev/articles/apply-instant-loading-with-prpl) to ensure that initial and subsequent navigations feel instant.  
客户端渲染可能难以实现并保持对移动设备的快速响应。通过稍加努力，严格控制 JavaScript 预算，并在尽可能少的往返请求中传递价值，你可以使客户端渲染几乎复制纯服务器端渲染的性能。通过使用 `<link rel=preload>` 更快地让解析器为你工作，我们可以传递关键脚本和数据。我们还建议考虑使用 PRPL 等模式，以确保初始和后续导航感觉即时。

![[_posts/architect/_教程/media/84b0f1f34660cb263d693c80e2cfc884_MD5.png]]

		FCP and TTI with client-side rendering. FCP 和 TTI 在客户端渲染中。

The primary downside to client-side rendering is that the amount of JavaScript required tends to grow as an application grows, which can impact a page's INP. This becomes especially difficult with the addition of new JavaScript libraries, polyfills, and third-party code, which compete for processing power and must often be processed before a page's content can render.  
**客户端渲染的主要缺点**是随着应用的增长，所需的 JavaScript 量往往会增加，这可能会影响页面的 INP。当添加新的 JavaScript 库、polyfills 和第三方代码时，这个问题会变得更加困难，因为这些代码会争夺处理能力，并且通常需要在页面内容渲染之前进行处理。

Experiences that use client-side rendering and rely on large JavaScript bundles should consider [aggressive code-splitting](https://web.dev/articles/reduce-javascript-payloads-with-code-splitting) to lower TBT and INP during page load, as well as lazy-loading JavaScript to serve only what the user needs, when it's needed. For experiences with little or no interactivity, server-side rendering can represent a more scalable solution to these issues.  
使用客户端渲染且依赖大型 JavaScript 包的体验**应该考虑采用激进的代码拆分来降低页面加载时的 TBT 和 INP，以及使用 JavaScript 懒加载来在需要时只提供用户需要的代码**。对于交互性很少或没有的体验，服务器端渲染可以代表一种更具可扩展性的解决方案。

For folks building single page applications, identifying core parts of the user interface shared by most pages lets you apply the [application shell caching](https://developer.chrome.com/blog/app-shell) technique. Combined with service workers, this can dramatically improve perceived performance on repeat visits, because the page can load its application shell HTML and dependencies from `CacheStorage` very quickly.  
对于构建单页应用程序的开发者来说，**识别大多数页面共享的用户界面核心部分，可以使用`应用程序壳缓存技术`。结合使用 Service Workers，这可以显著提高重复访问时的感知性能，因为页面可以非常快速地从 `CacheStorage` 加载其应用程序壳 HTML 和依赖项**。

# Rehydration
Rehydration combines server-side and client-side rendering
Rehydration 结合了服务器端和客户端渲染

[Hydration](https://react.dev/reference/react-dom/client/hydrateRoot#hydrating-server-rendered-html) is an approach that mitigate the tradeoffs between client-side and server-side rendering by doing both. Navigation requests, like full page loads or reloads, are handled by a server that renders the application to HTML. Then the JavaScript and data used for rendering is embedded into the resulting document. When done carefully, this achieves a fast FCP like server-side rendering, then "picks up" by rendering again on the client.  
`hydration` 是**一种通过同时进行客户端和服务器端渲染，来缓解客户端与服务器端渲染之间权衡的方法**。导航请求，如**全页加载或重新加载，由服务器处理，服务器将应用程序渲染为 HTML**。然后***将用于渲染的 JavaScript 和数据嵌入到生成的文档中***。当操作谨慎时，***这实现了类似服务器端渲染的快速 FCP，然后在客户端再次"接手"渲染***。

This is an effective solution, but it can have considerable performance drawbacks.  
这是一种有效的解决方案，但它可能会有相当大的性能缺点。

The primary downside of server-side rendering with rehydration is that it can have a significant negative impact on TBT and INP, even if it improves FCP. Server-side rendered pages can appear to be loaded and interactive, but can't actually respond to input until the client-side scripts for components are executed and event handlers have been attached. On mobile, this can take minutes, confusing and frustrating the user.  
使用服务器端渲染和重新 hydration 的**主要缺点**是，*即使它提高了 FCP，也可能对 TBT 和 INP 产生显著的负面影响*。服务器端渲染的页面*可能看起来已加载且可交互，但实际上直到组件的客户端脚本执行并附加了事件处理程序后才能响应输入*。在移动设备上，这*可能需要几分钟*，让用户感到困惑和沮丧。

## Rehydration 问题：一个应用却要花两倍的价格
A rehydration problem: one app for the price of two

For the client-side JavaScript to accurately take over where the server left off, without re-requesting all the data the server rendered its HTML with, most server-side rendering solutions serialize the response from a UI's data dependencies as script tags in the document. Because this duplicates a lot of HTML, rehydration can cause more problems than just delayed interactivity.  
**为了让客户端 JavaScript 能够准确接续服务器的工作，而无需重新请求服务器渲染 HTML 所需的所有数据**，大多数服务器端渲染解决方案都会**将 UI 的数据依赖关系序列化成文档中的 script 标签**。**由于这会导致大量 HTML 的重复**，因此重新激活（rehydration）可能会**引发比交互延迟更严重的问题**。

![[_posts/architect/_教程/media/a45d9f0d73e7ab6a82a9b933c8dc2907_MD5.png]]

		HTML document containing serialized UI, inlined data and a bundle.js script. 包含序列化 UI、内联数据和 bundle.js 脚本的 HTML 文档。

The server is returning a description of the application's UI in response to a navigation request, but it's also returning the source data used to compose that UI, and a complete copy of the UI's implementation which then boots up on the client. The UI doesn't become interactive until after `bundle.js` has finished loading and executing.  
服务器在响应请求时**返回了应用程序 UI 的描述，但也返回了用于构建该 UI 的源数据，以及 UI 实现的完整副本，该副本随后在客户端启动。直到 `bundle.js` 完成加载和执行后，UI 才会变得可交互**。

Performance metrics collected from real websites using server-side rendering and rehydration indicate that it's rarely the best option. The most important reason is its effect on the user experience, when a page looks ready but none of its interactive features work.  
从使用服务器端渲染和再水化的**真实网站收集的性能指标表明，这很少是最佳选项**。**最重要的原因**是它对用户体验的影响，*当页面看起来准备好了，但没有任何交互功能可用*。

![[_posts/architect/_教程/media/a158278f2bf1d9b2311d4da6e3e8b797_MD5.png]]

		The negative effects of client-side rendering on TTI. 客户端渲染对 TTI 的负面影响。

There's hope for server-side rendering with rehydration. In the short term, only using server-side rendering for highly cacheable content can reduce TTFB, producing similar results to prerendering. Rehydrating [incrementally](https://www.emberjs.com/blog/2017/10/10/glimmer-progress-report.html), progressively, or partially might be the key to making this technique more viable in the future.  
使用 rehydration 技术有望实现服务器端渲染。在短期内，仅对高度可缓存的內容使用服务器端渲染可以减少 TTFB，效果与预渲染类似。**逐步、渐进式或部分地Rehydrating可能是使这项技术在未来更具可行性的关键**。

# 流式服务器端渲染并逐步 Rehydrate
Stream server-side rendering and rehydrate progressively

Server-side rendering has had a number of developments over the last few years.  
服务器端渲染在过去几年中取得了一些发展。

[Streaming server-side rendering](https://mxstbr.com/thoughts/streaming-ssr) lets you send HTML in chunks that the browser can progressively render as it's received. This can get markup to your users faster, speeding up your FCP. In React, streams being asynchronous in `renderToPipeableStream()`, compared to synchronous `renderToString()`, means backpressure is handled well.  
`流式服务器端渲染`**允许你将 HTML 分块发送，浏览器可以在接收的同时逐步渲染**。这可以更快地将标记发送给用户，从而加速你的 FCP。*在 React 中，`流`是异步的 renderToPipeableStream() ，与同步的 renderToString() 相比，这意味着背压得到了良好处理*。

[Progressive rehydration](https://www.patterns.dev/react/progressive-hydration/) is also worth considering ([React has implemented it](https://github.com/facebook/react/pull/14717)). With this approach, individual pieces of a server-rendered application are "booted up" over time, instead of the current common approach of initializing the entire application at once. This can help reduce the amount of JavaScript needed to make pages interactive, because it lets you defer client-side upgrading of low-priority parts of the page to prevent it from blocking the main thread, letting user interactions happen sooner after the user initiates them.  
渐进式水合也值得考虑（React 已经实现了它）。在这种方法中，服务器渲染应用程序的各个部分会随着时间的推移被"启动"，而不是当前常见的方法——一次性初始化整个应用程序。这有助于减少使页面交互所需的 JavaScript 量，因为它允许你将页面低优先级部分的客户端升级推迟到防止其阻塞主线程，从而使用户交互在用户发起后能更快地发生。

Progressive rehydration can also help you avoid one of the most common server-side rendering rehydration pitfalls: a server-rendered DOM tree gets destroyed and then immediately rebuilt, most often because the initial synchronous client-side render required data that wasn't quite ready, often a `Promise` that hasn't resolved yet.  
渐进式重新激活也能帮助你避免最常见的服务器端渲染重新激活陷阱之一：服务器渲染的 DOM 树被销毁，然后立即重新构建，这通常是因为初始同步客户端渲染需要的数据尚未完全准备好，往往是一个尚未解决的 `Promise` 。

## Partial rehydration 
部分重新水化

Partial rehydration has proven difficult to implement. This approach is an extension of progressive rehydration that analyzes individual pieces of the page (components, views, or trees) and identifies the pieces with little interactivity or no reactivity. For each of these mostly-static parts, the corresponding JavaScript code is then transformed into inert references and decorative features, reducing their client-side footprint to nearly zero.  
部分重新水化已被证明难以实现。这种方法是渐进式重新水化的扩展，它分析页面的各个部分（组件、视图或树），并识别出交互性较弱或无响应的部分。对于这些大部分是静态的部分，相应的 JavaScript 代码会被转换为惰性引用和装饰性特征，将其客户端占用的空间减少到几乎为零。

The partial rehydration approach comes with its own issues and compromises. It poses some interesting challenges for caching, and client-side navigation means we can't assume that server-rendered HTML for inert parts of the application are available without a full page load.  
部分重新水化方法也带来了它自身的问题和妥协。它对缓存提出了一些有趣的挑战，客户端导航意味着我们不能假设应用程序惰性部分的服务器渲染 HTML 在未进行完整页面加载的情况下是可用的。

## 同形渲染
Trisomorphic rendering 

If [service workers](https://developer.chrome.com/docs/workbox/service-worker-overview) are an option for you, consider *trisomorphic* rendering. This technique lets you use streaming server-side rendering for initial or non-JavaScript navigations, and then have your service worker take on rendering of HTML for navigations after it has been installed. This can keep cached components and templates up to date and enable SPA-style navigations for rendering new views in the same session. This approach works best when you can share the same templating and routing code between the server, client page, and service worker.  
如果服务工作者是你的选择，可以考虑使用三态渲染。这种技术允许你使用流式服务器端渲染来处理初始或非 JavaScript 导航，然后让服务工作者负责安装后 HTML 的渲染。这可以保持缓存的组件和模板的最新状态，并支持在相同会话中渲染新视图的 SPA 风格导航。当服务器、客户端页面和服务工作者之间可以共享相同的模板和路由代码时，这种方法效果最佳。

![[_posts/architect/_教程/media/030e2af007b5c41b086e34c45953dc42_MD5.png]]

Trisomorphic rendering, demonstrating a browser and service worker communicating with the server. 三态渲染，展示了浏览器和服务工作者与服务器通信的过程。

# SEO 考虑因素
SEO considerations 

When choosing a web rendering strategy, teams often consider the impact of SEO. Server-side rendering is a popular choice for delivering a "complete looking" experience that crawlers can interpret. Crawlers [can understand JavaScript](https://web.dev/articles/how-search-works), but there are often [limitations](https://developers.google.com/search/docs/guides/rendering) to how they render. Client-side rendering can work, but often needs additional testing and overhead. More recently,[dynamic rendering](https://developers.google.com/search/docs/advanced/javascript/dynamic-rendering) has also become an option worth considering if your architecture depends heavily on client-side JavaScript.  
在选择 Web 渲染策略时，团队通常会考虑 SEO 的影响。服务器端渲染是一种流行的选择，可以提供"完整外观"的体验，供爬虫解释。爬虫可以理解 JavaScript，但它们在渲染方面往往存在限制。客户端渲染可以工作，但通常需要额外的测试和开销。最近，动态渲染也成为了一个值得考虑的选项，如果你的架构严重依赖客户端 JavaScript，这一点尤其重要。

When in doubt, the [mobile friendly test tool](https://search.google.com/test/mobile-friendly) is a great way to test that your chosen approach does what you're hoping for. It shows a visual preview of how any page appears to Google's crawler, the serialized HTML content it finds after JavaScript is executed, and any errors encountered during rendering.  
不确定时，移动友好测试工具是测试所选方法是否达到预期效果的好方法。它展示了任何页面在 Google 爬虫看来呈现的视觉预览、执行 JavaScript 后找到的序列化 HTML 内容，以及渲染过程中遇到的任何错误。

![[_posts/architect/_教程/media/b9c6fce49922b551461fdfcfc012fd82_MD5.png]]

The Mobile-Friendly Test UI. 移动友好测试界面。

# 结论
Conclusion 

When deciding on an approach to rendering, measure and understand what your bottlenecks are. Consider whether static rendering or server-side rendering can get you most of the way there. It's fine to mostly ship HTML with minimal JavaScript to get an experience interactive. Here's a handy infographic showing the server-client spectrum:  
在决定渲染方案时，测量并理解你的瓶颈所在。考虑静态渲染或服务器端渲染是否能够满足大部分需求。用少量 JavaScript 主要发送 HTML 来获得交互体验也是可行的。这里有一张展示服务器-客户端光谱的实用信息图：

![[_posts/architect/_教程/media/e8031509f5b0a9ddd91fe39f45636e3c_MD5.png]]

Rendering options and their tradeoffs. 渲染选项及其权衡。



Last updated 2019-02-06 UTC.  
最后更新时间：2019-02-06 UTC。
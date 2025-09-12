---
title: "Autocomplete | Front End System Design"
source: "https://www.greatfrontend.com/questions/system-design/autocomplete"
author:
  - "[[GreatFrontEnd]]"
published: 2022-11-01
created: 2025-09-12
description: "Learn how to solve the Autocomplete question in front end system design interviews using the RADIO framework. Read our deep-dives on many other top front end system questions."
tags:
  - "clippings"
---
## Asked at these companies 问这些公司


Autocomplete is a common question asked by many companies and encompasses many useful front end concepts and techniques which can be generalized to other front end system design questions. It is highly recommended to study this question well and thoroughly!  
自动完成是许多公司提出的一个常见问题，它包含许多有用的前端概念和技术，可以推广到其他前端系统设计问题。强烈建议好好研究这个问题！

## Question 问题

Design an autocomplete UI component that allows users to enter a search term into a text box, a list of search results appear in a popup and the user can select a result.  
设计一个自动完成 UI 组件，允许用户在文本框中输入搜索词，搜索结果列表出现在弹出窗口中，用户可以选择结果。

Some real-life examples where you might have seen this component in action:  
以下是一些您可能已经看到此组件实际运行的真实示例：

- Google's search bar on google.com where you see a list of primarily text-based suggestions.  
	谷歌在 google.com 上的搜索栏，你可以看到一个主要基于文本的建议列表。
- Facebook's search input where you see a list of rich results. The results can be friends, celebrities, groups, pages, etc.  
	Facebook 的搜索输入，你可以看到一个丰富的结果列表。结果可以是朋友、名人、群组、页面等。

![[_posts/architect/System Design/greatfrontend.com/questions/media/c4e86df5ee3bcebb60f3c09d331a63ac_MD5.jpeg]]


A back end API is provided which will return a list of results based on the search query.  
提供了一个后端 API，它将返回基于搜索查询的结果列表。

### Requirements 要求

- The component is generic enough to be usable by different websites.  
	该组件是通用的，足以被不同的网站使用。
- The input field UI and search results UI should be customizable.  
	输入字段 UI 和搜索结果 UI 应可自定义。

## Requirements exploration 需求探索

These are questions you should be asking your interviewer to dive deeper into the problem and refine the requirements.  
这些是你应该问面试官的问题，以便更深入地了解问题并细化要求。

### What kind of results should be supported? 应该支持什么样的结果？

Text, image, media (image accompanied with text) are the most common type of results but we cannot anticipate all the different kinds of results that users of the component will want to render.  
文本、图像、媒体（图像伴随文本）是最常见的结果类型，但我们无法预测组件用户想要呈现的所有不同类型的结果。

### What devices will this component be used on? 该组件将用于哪些设备？

All possible devices: laptop, tablets, mobile, etc.  
所有可能的设备：笔记本电脑、平板电脑、移动的等。

Not for the initial version. We can explore this if we have time.  
不是最初的版本。如果有时间，我们可以研究一下。

---

## Architecture 架构

![[_posts/architect/System Design/greatfrontend.com/questions/media/3d979ef23634fe47c228ae589e10f28f_MD5.png]]

- **Input field UI 输入字段 UI**
	- Handles user input and passes the user input to the controller.  
		处理用户输入并将用户输入传递给控制器。
- **Results UI (Popup) 结果 UI（弹出窗口）**
	- Receives results from the controller and presents it to the user.  
		从控制器接收结果并将其呈现给用户。
	- Handles user selection and informs the controller which input was selected.  
		处理用户选择并通知控制器选择了哪个输入。
- **Cache 缓存**
	- Stores the results for previous queries so that the controller can check if sending a request to the server.  
		存储以前查询的结果，以便控制器可以检查是否向服务器发送请求。
- **Controller 控制器**
	- The "brain" of the whole component, similar to the Controller in the Model View Controller (MVC) pattern. All the components in the system interact with this component.  
		整个组件的“大脑”，类似于模型视图控制器（MVC）模式中的控制器。系统中的所有组件都与此组件交互。
	- Passes user input and results between components.  
		在组件之间传递用户输入和结果。
	- Fetches results from the server if the cache is empty for a particular query.  
		如果特定查询的该高速缓存为空，则从服务器获取结果。

---

## Data model 数据模型

- Controller 控制器
	- Props/options exposed via the component API  
		通过组件 API 公开的道具/选项
	- Current search string 当前搜索字符串
- Cache 缓存
	- Initial results 初步成效
	- Cached results 缓存结果
	- Refer to section below for cache data model design  
		有关缓存数据模型设计，请参阅以下部分

*These are only the core fields that are needed for the basic functionality. More fields will be added as we dive deeper into specific topics below.  
这些只是基本功能所需的核心字段。随着我们深入到下面的特定主题，将添加更多字段。*

---

## Interface definition (API) 接口定义（API）

Since this is a front end system design question, we will focus on the API of the component and only briefly touch on the search API that the server should provide.  
由于这是一个前端系统设计问题，因此我们将重点讨论组件的 API，而只简单地涉及服务器应该提供的搜索 API。

### Client 客户端

Since we want to make a component that is flexible and easy for other developers to use, we cannot make too many assumptions about how the component will be used and have to supply a pretty large amount of options.  
由于我们希望开发一个灵活且易于其他开发人员使用的组件，因此我们不能对组件的使用方式做出太多假设，必须提供相当多的选项。

#### Basic API 基本 API

These are the core APIs which affect the functionality of the component.  
这些是影响组件功能的核心 API。

- **Number of results**: The number of results to show in the list of results.  
	**结果数** ：要在结果列表中显示的结果数。
- **API URL**: The URL to hit when a query is made. For an autocomplete use case, queries are made as the user types.  
	**API URL** ：进行查询时要命中的 URL。对于自动完成用例，查询是在用户输入时进行的。
- **Event listeners**: `'input'`, `'focus'`, `'blur'`, `'change'`, `'select'` are some of the common events which developers might want to respond to (possibly log user interactions), so adding hooks for these events would be helpful.  
	**事件监听器** ： `'input'` ， `'focus'` ， `'blur'` ， `'change'` ， `'select'` 是开发人员可能想要响应的一些常见事件（可能记录用户交互），因此为这些事件添加钩子会很有帮助。
- **Customized rendering**: There are a few ways to allow developers to customize the rendering of the various parts of their UI for their use cases:  
	**自定义渲染** ：有几种方法可以让开发人员为他们的用例自定义 UI 的各个部分的渲染：
	- **Theming options object**: This approach is the easiest to use but the least flexible/customizable. The component can accept an object of key/values (e.g. `{ textSize: 12px, textColor: 'red' }`) and use it when rendering.  
		**主题化选项对象** ：这种方法最容易使用，但最不灵活/可定制。组件可以接受一个键/值对象（例如 `{ textSize: 12px, textColor: 'red' }` ），并在渲染时使用它。
	- **Classnames**: Allow developers to specify their own CSS class names which the component will add to the various UI sub-components.  
		**Classnames：允许开发人员指定他们自己的 CSS 类名，组件将添加到各种 UI 子组件中。**
	- **Render function/callback**: This is an inversion of control technique commonly used in React where the rendering is completely left to the developer. The component invokes a developer-provided function with some data and the developer can customize the logic/code to render the UI based on that data. This is the most flexible approach but requires the most effort from the developer.  
		**渲染函数/回调：这是 React 中常用的控制反转技术，渲染完全留给开发人员。** 该组件使用一些数据调用开发人员提供的函数，并且开发人员可以定制逻辑/代码以基于该数据呈现 UI。这是最灵活的方法，但需要开发人员付出最多的努力。

#### Advanced API 高级 API

These APIs affect the user experience and performance of the component and should be covered if there's enough time.  
这些 API 会影响用户体验和组件的性能，如果有足够的时间，应该涵盖这些 API。

- **Minimum query length**: There will likely be too many irrelevant results if the user query is too short as it is not specific enough. We might only want to trigger the search when there's a minimum number of characters typed in, possibly 3 or more.  
	**最小查询长度** ：如果用户查询太短，则可能会有太多不相关的结果，因为它不够具体。我们可能只想在输入的字符数达到最小值（可能是 3 个或更多）时触发搜索。
- **Debounce duration**: Triggering a back end search API for every keystroke can be quite wasteful, especially when the queries for the first few characters are likely to not be meaningful. Debouncing is a technique that limits the number of time a function gets called. We could debounce hitting of the API so that the server does not get hit too often. With a debounce duration of 300ms, the back end search API will only be called after there has been no user input for 300ms.  
	**Debounce 持续时间** ：为每个字符串触发后端搜索 API 可能非常浪费，特别是当前几个字符的查询可能没有意义时。去弹跳是一种限制函数被调用次数的技术。我们可以对 API 的命中进行反跳，这样服务器就不会经常被命中。如果去抖动持续时间为 300 ms，则仅在 300 ms 内没有用户输入后才会调用后端搜索 API。
- **API timeout duration**: How long we should be waiting for a response to determine that the search has timed out and we can display an error.  
	**API 超时持续时间** ：我们应该等待多长时间的响应，以确定搜索已经超时，我们可以显示错误。
- **Cache-related**: More details on these options will be covered in the cache section below.  
	**缓存相关** ：关于这些选项的更多细节将在下面的该高速缓存一节中介绍。
	- Initial results 初步成效
	- Results source: network only/network and cache/cache only  
		结果来源：仅网络/网络和缓存/仅缓存
	- Function to merge results from server and cache  
		函数合并来自服务器和缓存的结果
	- Cache duration 缓存持续时间

### Server API 服务器 API

The server should provide a HTTP API that supports the following parameters:  
服务器应提供支持以下参数的 HTTP API：

- `query`: The actual search query  
	`query` ：实际的搜索查询
- `limit`: Number of results in one page  
	`limit` ：一页中的结果数
- `pagination`: The page number  
	`pagination` ：页码

`pagination` and `limit` are useful if there is a need to allow users to scroll down beyond the initial list of results to get the next "page" of results.  
如果需要允许用户向下滚动超出初始结果列表以获得下一“页”结果 `，则分页` 和 `限制` 是有用的。

---

## Optimizations and deep dive 优化和深度挖掘

With the basics out of the way, we can dive deeper into specific topics which are relevant to the question.  
有了基础知识，我们可以更深入地探讨与问题相关的特定主题。

### Network 网络

#### Handling concurrent requests/race conditions 处理并发请求/竞争条件

What happens if the user makes changes to the query while there's a pending network request? If there are multiple pending network requests, we will need to be mindful not to display results for a previous search query. We cannot rely on the return order of network responses from the server because an earlier request might only be completed later than a request fired later on.  
如果用户在有挂起的网络请求时更改查询，会发生什么？如果有多个待处理的网络请求，我们需要注意不要显示先前搜索查询的结果。我们不能依赖于服务器网络响应的返回顺序，因为较早的请求可能只会比稍后触发的请求晚完成。

To know which request's response we should use display, we could:  
要知道我们应该使用 display 来显示哪个请求的响应，我们可以：

1. Attach a timestamp to each request to determine the latest request and only display the results of the latest request (not the latest response!). Discard the response of irrelevant queries.  
	为每个请求附加一个时间戳，以确定最新的请求，并仅显示最新请求的结果（而不是最新的响应！）。丢弃不相关查询的响应。
2. Save the results in an object/map, keyed by the search query string and only present the results corresponding to the input value in the search input.  
	将结果保存在对象/映射中，由搜索查询字符串键入，并仅显示与搜索输入中的输入值相对应的结果。

Which option is better? Given we have a cache that remembers the responses of each search query, option 2 is clearly better. Refer to the cache section details for more details.  
哪种选择更好？考虑到我们有一个缓存可以记住每个搜索查询的响应，选项2显然更好。有关详细信息，请参阅该高速缓存部分的详细信息。

It's not advisable to abort requests (via `AbortController`) or discard responses since the server would have received and processed the request and returned the data back to the client.  
不建议中止请求（通过 `AbortController` ）或丢弃响应，因为服务器将接收和处理请求并将数据返回给客户端。

Saving the responses for historical keystrokes is useful for cases where users accidentally typed an extra character. "f' -> "fo" -> "foo" -> meant to type "t" but mistyped an extra "r" due to fat fingers -> "foot" -> "footr" -> deletes extra "r" -> "foot" (results for "foot" can be displayed immediately it is already in the cache). If there's debounce, then the requests for "foot" might not have fired immediately and there's no response for "foot" to cache, so this mainly benefits autocomplete components without debounce or people who type slower than the debounce duration.  
如果用户不小心键入了额外的字符，则保存对历史错误的响应非常有用。“f' ->“fo”->“foo”->本打算键入“t”，但由于胖手指而键入了额外的“r”->“foot”->“footr”->删除了额外的“r”->“foot”（“foot”的结果可以立即显示，因为它已经在该高速缓存中）。如果存在去抖动，那么对“foot”的请求可能不会立即触发，并且对“foot”的缓存没有响应，因此这主要有利于没有去抖动的自动完成组件或输入速度比去抖动持续时间慢的人。

#### Failed requests and retries 失败的请求和重试

Server requests can sometimes fail, possibly due to the user's flaky internet connection. The component can automatically retry firing the query. In case the server is indeed offline and we are concerned about overloading the server, we could use an exponential backoff strategy.  
服务器请求有时会失败，可能是由于用户的 Internet 连接不稳定。组件可以自动重试触发查询。如果服务器确实离线，并且我们担心服务器过载，我们可以使用指数退避策略。

#### Offline usage 离线使用

If we detect that the device has entirely lost network connection, there's not a whole lot that we can do since our component relies on the server for data. But we could do the following to improve the UX:  
如果我们检测到设备完全失去了网络连接，我们就无能为力了，因为我们的组件依赖于服务器获取数据。但我们可以做以下事情来改善 UX：

- Read purely from cache. Obviously this is not very useful if the cache is empty.  
	纯粹从缓存读取。显然，如果该高速缓存为空，则这不是很有用。
- Not fire any requests so as not to waste CPU cycles.  
	不触发任何请求，以免浪费 CPU 周期。
- Indicate that there's no network connection somewhere in the component.  
	在组件中的某个地方没有网络连接。

### Cache 缓存

What is the cache for? Caches are typically used to improve performance of queries and reducing processing costs by saving the results of previous queries in memory. If/when the user searches for the same term again, instead of hitting the server for the results, we can retrieve the results from memory and instantly show the results, effectively removing the need for any network request and latency.  
该高速缓存是做什么用的？缓存通常用于提高查询的性能，并通过将先前查询的结果保存在内存中来降低处理成本。如果/当用户再次搜索同一个词时，我们可以从内存中检索结果并立即显示结果，而不是点击服务器获取结果，从而有效地消除了对任何网络请求和延迟的需求。

To provide the best experience, Google and Facebook search inputs cache user queries.  
为了提供最佳体验，Google 和 Facebook 搜索输入缓存用户查询。

#### Cache structure 高速缓存结构

The cache within the component is the most interesting aspect of the component as there are many ways to design the cache, each with its own pros and cons. Explaining the tradeoffs of each is essential to acing front end system design interviews.  
组件内的该高速缓存是该组件最有趣的方面，因为有很多种方法可以设计该高速缓存，每种方法都有自己的优点和缺点。解释每种方法的权衡对于获得前端系统设计面试至关重要。

**1\. Hash map with search query as key and results as value.** This is the most obvious structure for a cache, mapping the string query to the results. Retrieving the results is super simple and can be obtained in O(1) time just by looking up whether the cache contains the search term as a key.  
**1.以搜索查询为键，结果为值的哈希映射。** 这是缓存最明显的结构，它将字符串查询映射到结果。检索结果非常简单，只需查找该高速缓存是否包含搜索项作为键，就可以在 O（1）时间内获得。

```js
const cache = {
  fa: [
    { type: 'organization', text: 'Facebook' },
    {
      type: 'organization',
      text: 'FasTrak',
      subtitle: 'Government office, San Francisco, CA',
    },
    { type: 'text', text: 'face' },
  ],
  fac: [
    { type: 'organization', text: 'Facebook' },
    { type: 'text', text: 'face' },
    { type: 'text', text: 'facebook messenger' },
  ],
  face: [
    { type: 'organization', text: 'Facebook' },
    { type: 'text', text: 'face' },
    { type: 'text', text: 'facebook stock' },
  ],
  faces: [
    { type: 'television', text: 'Faces of COVID', subtitle: 'TV program' },
    { type: 'musician', text: 'Faces', subtitle: 'Rock band' },
    { type: 'television', text: 'Faces of Death', subtitle: 'Film series' },
  ],
  // ...
};
```

However, see that there are lots of duplicate results especially if we don't do any debouncing as the user is typing and we fire one request per keystroke. This results in the page consuming lots of memory for the cache.  
然而，看到有很多重复的结果，特别是如果我们在用户输入时不做任何去抖动，并且我们每个去抖动触发一个请求。这会导致页占用大量内存用于该高速缓存。

**2\. List of results.** Alternatively, we could save the results as a flat list and do our own filtering on the front end. There will not be much (if any) duplication of results.  
**2.结果列表。** 或者，我们可以将结果保存为一个平面列表，并在前端进行我们自己的过滤。结果不会有太多重复（如果有的话）。

```js
const results = [
  { type: 'company', text: 'Facebook' },
  {
    type: 'organization',
    text: 'FasTrak',
    subtitle: 'Government office, San Francisco, CA',
  },
  { type: 'text', text: 'face' },
  { type: 'text', text: 'facebook messenger' },
  { type: 'text', text: 'facebook stock' },
  { type: 'television', text: 'Faces of COVID', subtitle: 'TV program' },
  { type: 'musician', text: 'Faces', subtitle: 'Rock band' },
  { type: 'television', text: 'Faces of Death', subtitle: 'Film series' },
];
```

However, this is not ideal in practice because we have to do filtering on the client side. This is bad for performance and might end up blocking the UI thread and is especially evident on large data sets and slow devices. The ranking order per result might also be lost, which is not ideal.  
然而，这在实践中并不理想，因为我们必须在客户端进行过滤。这对性能不利，可能最终会阻塞 UI 线程，在大型数据集和慢速设备上尤其明显。每个结果的排名顺序也可能丢失，这是不理想的。

**3\. Normalized map of results.** We take inspiration from [normalizr](https://github.com/paularmstrong/normalizr/tree/master/docs) and structure the cache like a database, combining the best traits of the earlier approaches - fast lookup and non-duplicated data. Each result entry is one row in the "database" and is identified by a unique ID. The cache simply refers to each item's ID.  
**3.结果的归一化图。** 我们从 normalizr 中获得灵感，并将该高速缓存结构化为数据库，结合了早期方法的最佳特性-快速查找和非重复数据。 每个结果条目都是"数据库"中的一行，并由唯一的 ID 标识。该高速缓存只是引用每个项目的 ID。

```js
// Store results by ID to easily retrieve the data for a specific ID.
const results = {
  1: { id: 1, type: 'organization', text: 'Facebook' },
  2: {
    id: 2,
    type: 'organization',
    text: 'FasTrak',
    subtitle: 'Government office, San Francisco, CA',
  },
  3: { id: 3, type: 'text', text: 'face' },
  4: { id: 4, type: 'text', text: 'facebook messenger' },
  5: { id: 5, type: 'text', text: 'facebook stock' },
  6: {
    id: 6,
    type: 'television',
    text: 'Faces of COVID',
    subtitle: 'TV program',
  },
  7: { id: 7, type: 'musician', text: 'Faces', subtitle: 'Rock band' },
  8: {
    id: 8,
    type: 'television',
    text: 'Faces of Death',
    subtitle: 'Film series',
  },
};

const cache = {
  fa: [1, 2, 3],
  fac: [1, 3, 4],
  face: [1, 3, 5],
  faces: [6, 7, 8],
  // ...
};
```

There will be pre-processing that needs to be done before showing the results to the user, to map a list's result IDs to the actual result items but the processing cost is negligible if there are only a few items to be shown.  
在向用户显示结果之前，需要进行预处理，将列表的结果 ID 映射到实际的结果项，但是如果只有几个项要显示，则处理成本可以忽略不计。

**Which structure to use?使用哪种结构？**

Which to use depends on the type of application this component is being used in.  
使用哪一个取决于使用此组件的应用程序的类型。

- **Short-lived websites**: If the component is being used on a page which is short-lived (e.g. Google search), option 1 would be the best. Even though there is duplicated data, the user is unlikely to use search so often that memory usage becomes an issue. The cache is being cleared/reset when the user clicks on a search result anyway.  
	**短期网站** ：如果组件用于短期页面（例如 Google 搜索），则选项 1 是最佳选择。即使有重复的数据，用户也不太可能经常使用搜索，以至于内存使用成为一个问题。当用户单击搜索结果时，该高速缓存将被清除/重置。
- **Long-lived websites**: If this autocomplete component is used on page which is a long-lived single page application (e.g. Facebook website), then option 3 might be viable. However, do also note that caching results for too long might be a bad idea as stale results take up memory without being useful.  
	**长期网站** ：如果这个自动完成组件用于长期单页应用程序（例如 Facebook 网站），那么选项 3 可能是可行的。但是，还要注意，缓存结果太长时间可能是一个坏主意，因为过时的结果会占用内存而没有用处。

#### Initial results 初步成效

Noticed how on Google search, when you first focus on the input, a list of results is displayed even though you haven't typed anything yet? Showing an initial relevant list of results could be useful in saving users from typing and reducing server costs.  
注意到在 Google 搜索中，当你第一次关注输入时，即使你还没有输入任何东西，也会显示一个结果列表？显示一个初始的相关结果列表可以帮助用户节省输入和降低服务器成本。

- **Google**: Popular search queries today (current affairs, trending celebrities, latest happenings) and historical searches  
	**Google** ：今日热门搜索查询（时事、热门名人、最新事件）和历史搜索
- **Facebook**: Historical searches.  
	**Facebook** ：历史搜索。
- **Stock/crypto/currency exchanges**: Historical searches or trending stocks  
	**股票/加密/货币交易所** ：历史搜索或趋势股票

The initial results could be an option on the component and added to the cache where the key is an empty string.  
初始结果可以是组件上的一个选项，并添加到键为空字符串的该高速缓存中。

In the past, Facebook loaded a user's friends, pages, groups into the browser cache so that results can be shown instantly via client-side filtering without sending another HTTP request.  
在过去，Facebook 会将用户的好友、页面、群组加载到浏览器缓存中，这样就可以通过客户端过滤立即显示结果，而无需发送另一个 HTTP 请求。

*Source: [The Life of a Typeahead Query](https://engineering.fb.com/2010/05/17/web/the-life-of-a-typeahead-query/)  
数据来源： [The Life of a Typeahead Query](https://engineering.fb.com/2010/05/17/web/the-life-of-a-typeahead-query/)*

#### Caching strategy 缓存策略

Caching is a space/time tradeoff where we trade memory space to save on processing time. Having cached results stay around for too long is a bad idea because it consumes memory and if too much time has passed since the cache entry was written, the results are likely irrelevant/outdated. There's little value in using memory to store irrelevant/outdated results.  
缓存是一种空间/时间的权衡，我们用内存空间来节省处理时间。让缓存结果停留太长时间是一个坏主意，因为它会消耗内存，并且如果自写入该高速缓存条目以来已经过了太长时间，则结果可能是不相关的/过时的。使用内存来存储不相关/过时的结果没有什么价值。

When to evict the cache depends on the type of application:  
何时收回该高速缓存取决于应用程序的类型：

- **Google**: Google search results don't update that often, so the cache is useful and can live for long (hours?).  
	**Google** ：Google 搜索结果不会经常更新，因此该高速缓存很有用，并且可以保存很长时间（几个小时？）。
- **Facebook**: Facebook search results are updated moderately often, so a cache is useful but entries should be evicted every now and then (half an hour?).  
	**Facebook** ：Facebook 的搜索结果更新频率适中，所以缓存是有用的，但条目应该时不时地被驱逐（半小时？）。
- **Stock/currency exchanges**: Exchanges with an autocomplete for stock ticker symbols/currency showing the current price in the results might not want to cache at all because the prices change every minute when the markets are open.  
	**股票/货币交易所** ：在结果中显示当前价格的自动完成股票代码/货币的交易所可能根本不想缓存，因为当市场开放时，价格每分钟都在变化。

We can add the data source/caching strategy and cache duration as configuration options on the component.  
我们可以在组件上添加数据源/缓存策略和缓存持续时间作为配置选项。

- **Data source**: Whether to read the results from the "network only", "network and cache", "cache only".  
	**数据源** ：是否从“仅网络”、“网络和缓存”、“仅缓存”读取结果。
- **Cache duration/Time-to-live**: How long to retain each cache entry for. This will involve adding timestamps to each entry and evicting stale cache entries every now and then.  
	**缓存持续时间/生存时间** ：每个缓存条目保留多长时间。这将涉及到为每个条目添加时间戳，并不时地驱逐过时的缓存条目。

### Performance 性能

Performance here refers to client-side performance since server-side performance (how fast the query returns) is out of scope.  
这里的性能指的是客户端性能，因为服务器端性能（查询返回的速度）超出了范围。

We can't improve how fast the server returns the response, but with client-side caching, we can show results for previous queries near instantly. We can even go one step further and use cached results for future results if there's a match.  
我们无法提高服务器返回响应的速度，但通过客户端缓存，我们可以立即显示先前查询的结果。我们甚至可以更进一步，如果有匹配的结果，就将缓存的结果用于将来的结果。

#### Debouncing/throttling 去抖动/节流

By limiting the number of network requests that can be fired, we reduce server payload and CPU processing.  
通过限制可以触发的网络请求的数量，我们减少了服务器负载和 CPU 处理。

#### Memory usage 内存使用

Long-lived pages might have autocomplete components which accumulate too many results in the cache and hog memory. Purging the cache and freeing up memory is essential for such pages. The purging can be done when the browser is idle or total memory/number of cache entries exceed a certain threshold.  
长期存在的页可能具有自动完成组件，该组件会在该高速缓存中累积太多结果并占用内存。清除该高速缓存并释放内存对于这些页是必不可少的。清除可以在浏览器空闲或总内存/缓存条目数超过某个阈值时完成。

#### Virtualized lists 虚拟化列表

If the results contains many items (order of hundreds and thousands), rendering that many DOM nodes in the browser would cost lots of memory and slow down the browser. List virtualization is a technique we can use here to help the component retain its performance at scale.  
如果结果中包含很多项（成百上千的数量级），那么在浏览器中呈现这么多 DOM 节点将消耗大量内存，并降低浏览器的速度。列表虚拟化是我们可以在这里使用的一种技术，它可以帮助组件保持其大规模性能。

From [https://web.dev](https://web.dev/virtualize-long-lists-react-window):  
从 [https://web.dev](https://web.dev/virtualize-long-lists-react-window) 下载：

> List virtualization, or "windowing", is the concept of only rendering what is visible to the user. The number of elements that are rendered at first is a very small subset of the entire list and the "window" of visible content moves when the user continues to scroll. This improves both the rendering and scrolling performance of the list.  
> 列表虚拟化，或“窗口化”，是只呈现用户可见内容的概念。最初呈现的元素数量是整个列表的一个非常小的子集，当用户继续滚动时，可见内容的“窗口”会移动。这提高了列表的呈现和滚动性能。

The trick here is to only render the nodes which are visible and recycle DOM nodes instead of creating new ones. We can make the results window give the illusion that it contains that many results with fake off-screen elements that add up to the height of the non-visible result elements.  
这里的技巧是只呈现可见的节点，并回收 DOM 节点，而不是创建新的节点。我们可以让结果窗口给人一种错觉，即它包含了许多假的屏幕外元素，这些元素加起来相当于不可见的结果元素的高度。

### User experience 用户体验

The following are some good UX practices to apply to the autocomplete component:  
以下是一些适用于自动完成组件的良好 UX 实践：

#### Autofocus 自动对焦

Add the `autofocus` attribute to your `input` if it's a search page (like Google) and you're very certain that the user has a high intention to use the autocomplete when it is present on the screen.  
如果是一个搜索页面（如 Google），并且您非常确定用户在屏幕上显示自动完成时有很高的意图，请将 `autofocus` 属性添加到您 `的输入` 中。

#### Handle different states 处理不同的状态

- **Loading**: Show spinner when there's a background request.  
	**加载** ：当有后台请求时显示微调器。
- **Error**: Show an error message with a retry request button.  
	**错误** ：显示带有重试请求按钮的错误消息。
- **No network**: Show an error message that there's no network available.  
	**无网络** ：显示无可用网络的错误消息。

#### Handle long strings 处理长字符串

Long text in the result items should be handled appropriately, usually via truncating with an ellipsis or wrapping nicely. The text should not overflow and appear outside the component.  
结果项中的长文本应该得到适当的处理，通常是通过省略号截断或很好地换行。文本不应溢出并出现在组件之外。

#### Mobile-friendliness 移动友好

- Each result item should be large enough for user to tap on if used on mobile.  
	如果在移动的上使用，每个结果项应足够大，以便用户点击。
- Dynamic number of results depending on viewport window size, but this is better implemented in userland instead.  
	根据 viewport 窗口大小的动态结果数量，但这更好地实现在 userland 中。
- Set helpful attributes for mobile: `autocapitalize="off"`, `autocomplete="off"`, `autocorrect="off"`, `spellcheck="false"` so that the browser suggestions do not interfere with the user's search.  
	为移动的设置有用的属性： `autocapitalize=“off”` ， `autocomplete=“off”` ， `autocorrect=“off”` ， `spellcheck=“false”` ，这样浏览器建议就不会干扰用户的搜索。
- Users should be able to use the component and focus on the autocomplete suggestions just via their keyboard. Read more under the **Accessibility** section.  
	用户应该能够使用该组件，并通过键盘专注于自动完成建议。在 **无障碍** 部分阅读更多内容。
- Add a global shortcut key to let the user easily focus on the autocomplete input. A common keyboard shortcut is the / (forward slash) key, which is used by Facebook, X and YouTube.  
	添加一个全局快捷键，让用户轻松地专注于自动完成输入。一个常见的键盘快捷键是 / （正斜杠）键，Facebook，X 和 YouTube 都使用它。

It is easy to make typographical errors, especially on mobile devices. A fuzzy search is a searching technique where results that matches the search query closely instead of exactly are also considered. Fuzzy searches help you find relevant results even when the search terms are misspelled.  
很容易出现印刷错误，尤其是在移动的设备上。模糊搜索是一种搜索技术，其中也考虑与搜索查询紧密匹配而不是完全匹配的结果。模糊搜索可以帮助您找到相关的结果，即使搜索词拼写错误。

Fuzzy searches can be used if the filtering is purely done on the client side by computing edit distance (e.g. [Levenshtein distance](https://en.wikipedia.org/wiki/Levenshtein_distance)) between the search query and the results and selecting the ones with the smallest edit distance. For searches done on the server-side, we can send the query as-is and have the fuzzy matching be done on the server.  
如果过滤纯粹在客户端通过计算搜索查询和结果之间的编辑距离（例如 [Levenshtein 距离](https://en.wikipedia.org/wiki/Levenshtein_distance) ）并选择具有最小编辑距离的那些来完成，则可以使用模糊搜索。对于在服务器端完成的搜索，我们可以按原样发送查询，并在服务器上完成模糊匹配。

#### Query results positioning 查询结果定位

The list of autocomplete suggestions typically appear below the input. However, if the autocomplete component is at the bottom of the window, then there's insufficient space to fully display the results. The suggestions can be made aware of its positioning on the page and render above the input if there's no space to show it below the input.  
自动完成建议列表通常出现在输入的下方。但是，如果自动完成组件位于窗口底部，则没有足够的空间来完全显示结果。可以让建议知道它在页面上的位置，如果没有空间在输入下方显示它，则在输入上方呈现。

### Accessibility 无障碍

#### Screen readers 屏幕阅读器

- Use semantic HTML or use the right `aria` roles if using non-semantic HTML. Use `<ul>`, `<li>` for building list items or `role="listbox"` and `role="option"`.  
	使用语义 HTML，如果使用非语义 HTML，则使用正确的 `aria` 角色。使用 `<ul>` 、 `<li>` 构建列表项或使用 `role=“listbox”` 和 `role=“option”` 。
- `aria-label` for the `<input>` because there usually isn't a visible label.  
	为 `<input>` ` 添加 aria-label` ，因为通常没有可见的标签。
- `role="combobox"` for the `<input>`.  
	`role` = `<input>` 的“combobox”。
- [`aria-haspopup`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-haspopup) to indicate that the element can trigger an interactive popup element.  
	[`aria-haspopup`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-haspopup) 指示元素可以触发交互式弹出元素。
- [`aria-expanded`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-expanded) to indicate whether the popup element is currently displayed.  
	[`aria-expanded`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-expanded) 以指示当前是否显示 popup 元素。
- Mark the results region with [`aria-live`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) so that when new results are shown, screen reader users are notified.  
	用 [`aria-live`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Live_Regions) 标记结果区域，以便在显示新结果时通知屏幕阅读器用户。
- [`aria-autocomplete`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-autocomplete) to describe the type of autocompletion interaction model the combobox will use when dynamically helping users complete text input, whether suggestions will be shown as a single value inline (`aria-autocomplete="inline"`) or in a collection of values (`aria-autocomplete="list"`)  
	[`aria-autocomplete`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-autocomplete) 描述组合框在动态帮助用户完成文本输入时将使用的自动完成交互模型的类型，建议将显示为单个值内联（ `aria-autocomplete=“inline”` ）还是值集合（ `aria-autocomplete=“list”` ）
	- Google uses `aria-autocomplete="both"` while Facebook and X use `aria-autocomplete="list"`.  
		Google 使用 `aria-autocomplete=“both”` ，而 Facebook 和 X 使用 `aria-autocomplete=“list”` 。
- Hit enter to perform a search. You can get this behavior for free by wrapping the `<input>` in a `<form>`.  
	按回车键进行搜索。您可以通过将 `<input>` 包装在 `<form> 中来` 免费获得此行为。
- Up/down arrows to navigate the options, wrapping around when the end of the list is reached.  
	用于导航选项的上/下箭头，到达列表末尾时环绕。
- Escape to dismiss the results popup if it is visible.  
	如果结果弹出窗口可见，则退出。
- ... and more. Follow the [WAI ARIA Combo Box](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/) practices.  
	...和更多.遵循 [阿威 ARIA Combo Box 的](https://www.w3.org/WAI/ARIA/apg/patterns/combobox/) 做法。

Here's a comparison of Google, Facebook, and X's search autocomplete component and the HTML attributes used.  
下面是 Google、Facebook 和 X 的搜索自动完成组件和使用的 HTML 属性的比较。

| HTML Attribute HTML 属性 | Google 谷歌 | Facebook | X |
| --- | --- | --- | --- |
| HTML Element HTML 元素 | `<textarea>` | `<input>` | `<input>` |
| Within `<form>`   在 `< 表单>` 内 | Yes 是的 | No 没有 | Yes 是的 |
| `type` | `"text"` | `"search"` | `"text"` |
| `autocapitalize` | `"off"` | Absent 缺席 | `"sentence"` |
| `autocomplete` | `"off"` | `"off"` | `"off"` |
| `autocorrect` | `"off"` | Absent 缺席 | `"off"` |
| `autofocus` | Present 本 | Absent 缺席 | Present 本 |
| `placeholder` | Absent 缺席 | `"Search Facebook"` | `"Search"` |
| `role` | `"combobox"` | Absent 缺席 | `"combobox"` |
| `spellcheck` | `"false"` | `"false"` | `"false"` |
| `aria-activedescendant` | Present 本 | Absent 缺席 | Present 本 |
| `aria-autocomplete` | `"both"` | `"list"` | `"list"` |
| `aria-expanded` | Present 本 | Present 本 | Present 本 |
| `aria-haspopup` | `"false"` | Absent 缺席 | Absent 缺席 |
| `aria-invalid` | Absent 缺席 | `"false"` | Absent 缺席 |
| `aria-label` | `"Search"` | `"Search Facebook"` | `"Search query"` |
| `aria-owns` | Present 本 | Absent 缺席 | Present 本 |
| `dir` | Absent 缺席 | `"ltr"` / `"rtl"`   `“ltr”` / `“rtl”` | `"auto"` |
| `enterkeyhint` | Absent 缺席 | Absent 缺席 | `"search"` |

Note: This table is only accurate at the time of writing since companies update their search components from time to time. The point is to demonstrate is that there's no standardized practice on the ARIA properties to use.  
注：此表仅在撰写时准确，因为公司会不时更新其搜索组件。关键是要证明，在 ARIA 属性上没有标准化的实践可供使用。

## References 引用

- [The Life of a Typeahead Query  
	Typeahead 查询的生命](https://engineering.fb.com/2010/05/17/web/the-life-of-a-typeahead-query/)
- [Building an accessible autocomplete control  
	生成可访问的自动完成控件](https://adamsilver.io/blog/building-an-accessible-autocomplete-control/)

## Changelog

- 2024/08/21
	- Updated Google, Facebook, X's HTML attributes table.  
		更新了 Google，Facebook，X 的 HTML 属性表。
	- Changed Twitter to X.  
		把 Twitter 改成了 X。
- 2023/01/14
	- Update normalized store format to be an object instead of array for quick lookup.  
		更新规范化存储格式为对象而不是数组，以便快速查找。

The fastest way to learn Front End – by building actual projects  
学习前端的最快方法-通过构建实际项目

Want to grow in your front end skills? Learn them the right way — build apps step-by-step while referencing best practices from Senior engineers.

- [80+ real world projects](https://www.greatfrontend.com/a/click?a=projects-in-content&u=%2Fprojects%2Fchallenges)
- Professional specs and designs by top-tier product managers and designers
- Guides and reference solutions from Senior Big Tech engineers
- Use a [front end skill roadmap](https://www.greatfrontend.com/a/click?a=projects-in-content&u=%2Fprojects%2Fskills) to build from beginner to pro

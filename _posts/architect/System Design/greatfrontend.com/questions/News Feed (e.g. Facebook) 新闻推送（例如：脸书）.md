---
title: "News Feed (e.g. Facebook) | Front End System Design"
source: "https://www.greatfrontend.com/questions/system-design/news-feed-facebook"
author:
  - "[[GreatFrontEnd]]"
published: 2022-11-01
created: 2025-09-12
description: "Learn how to solve the News Feed (e.g. Facebook) question in front end system design interviews using the RADIO framework. Read our deep-dives on many other top front end system questions."
tags:
  - "clippings"
---

Designing a news feed application is a classic system design question, but almost no existing resource discusses in detail about how to design the front end of a news feed.  
设计新闻提要应用程序是一个经典的系统设计问题，但几乎没有现有的资源详细讨论如何设计新闻提要的前端。

## Question 问题

Design a news feed application that contains a list of feed posts users can interact with.  
设计一个新闻提要应用程序，其中包含用户可以与之交互的提要帖子列表。

![[_posts/architect/System Design/greatfrontend.com/questions/media/d266eddcc2ee42bcbbdc5e14a407201d_MD5.png]]

### Real-life examples 现实生活中的例子

- [https://www.facebook.com](https://www.facebook.com/)
- [https://www.twitter.com](https://www.twitter.com/)
- [https://www.quora.com](https://www.quora.com/)
- [https://www.reddit.com](https://www.reddit.com/)

## Requirements exploration 需求探索

### What are the core features to be supported? 需要支持的核心功能是什么？

- Browse news feed containing posts by the user and their friends.  
	浏览包含用户及其好友帖子的新闻提要。
- Liking and reacting to feed posts.  
	喜欢和反应饲料职位。
- Creating and publishing new posts.  
	创建和发布新帖子。

Commenting and sharing will be discussed further down below but is not included in the core scope.  
评论和分享将在下面进一步讨论，但不包括在核心范围内。

### What kind of posts are supported? 支持哪些类型的帖子？

Primarily text and image-based posts. If time permits we can discuss more types of posts.  
基于文本和图像的帖子。如果时间允许，我们可以讨论更多类型的职位。

### What pagination UX should be used for the feed? 什么分页 UX 应该用于饲料？

Infinite scrolling, meaning more posts will be added when the user reaches the end of their feed.  
无限滚动，这意味着当用户到达其提要的末尾时，将添加更多的帖子。

### Will the application be used on mobile devices? 该应用程序将在移动的设备上使用吗？

Not a priority, but a good mobile experience would be nice.  
不是优先考虑的，但一个好的移动的体验会很好。

---

## Architecture / high-level design 架构/高级设计

![[_posts/architect/System Design/greatfrontend.com/questions/media/723a835cb5c3d712820cb2b36ef3c016_MD5.png]]

### Component responsibilities 组成部分职责

- **Server**: Provides HTTP APIs to fetch feed posts and to create new feed posts.  
	**服务器端** ：提供 HTTP API 来获取 feed post 和创建新的 feed post。
- **Controller**: Controls the flow of data within the application and makes network requests to the server.  
	**控制器** ：控制应用程序中的数据流，并向服务器发出网络请求。
- **Client store**: Stores data needed across the whole application. In the context of a news feed, most of the data in the store will be server-originated data needed by the feed UI.  
	**客户端存储** ：存储整个应用程序所需的数据。在新闻提要的上下文中，存储中的大部分数据都是提要 UI 所需的源自服务器的数据。
- **Feed UI**: Contains a list of feed posts and the UI for composing new posts.  
	**Feed UI** ：包含 Feed 帖子列表和用于撰写新帖子的 UI。
	- **Feed posts**: Presents the data for a feed post and contains buttons to interact with the post (like/react/share).  
		**Feed posts** ：显示 Feed post 的数据，并包含与 post 交互的按钮（如/反应/分享）。
	- **Post composer**: WYSIWYG (what you see is what you get) editor for users to create new feed posts.  
		**Post composer** ：WYSIWYG（所见即所得）编辑器，用于用户创建新的 Feed 帖子。

### Rendering approach 渲染方法

Traditional web applications have multiple choices on where to render the content, whether to render on the server or the client.  
传统的 Web 应用程序在何处呈现内容方面有多种选择，是在服务器上呈现还是在客户端上呈现。

- **Server-side rendering (SSR)**: Rendering the HTML on the server side, which is the most traditional way. Best for static content that require SEO and does not require heavy user interaction. Websites like blogs, documentation sites, e-commerce websites are built using SSR.  
	**服务器端渲染（SSR）** ：在服务器端渲染 HTML，这是最传统的方式。最适合需要 SEO 且不需要大量用户交互的静态内容。博客、文档网站、电子商务网站等网站都是使用 SSR 构建的。
- **Client-side rendering (CSR)**: Rendering in the browser, by dynamically adding DOM elements into the page using JavaScript. Best for interactive content. Applications like dashboards, chat apps are built using CSR.  
	**客户端呈现（CSR）** ：通过使用 JavaScript 将 DOM 元素动态添加到页面中，在浏览器中呈现。最适合互动内容。仪表板、聊天应用等应用程序都是使用 CSR 构建的。

Interestingly, news feed applications are somewhere in-between, there's a good amount of static content but they also require interaction. In reality, Facebook uses a hybrid approach which gives the best of both worlds: a fast initial load with SSR then hydrating the page to attach event listeners for user interactions. Subsequent content (e.g. more posts added once the user reaches the end of their feed) and page navigation will use CSR.  
有趣的是，新闻提要应用程序介于两者之间，有大量的静态内容，但它们也需要交互。实际上，Facebook 使用了一种混合方法，它提供了两个世界的最佳效果：快速初始加载 SSR，然后对页面进行水合，以附加事件侦听器进行用户交互。后续内容（例如，一旦用户到达其提要的末尾，就会添加更多帖子）和页面导航将使用 CSR。

Modern UI JavaScript frameworks like React and Vue, along with meta frameworks like [Next.js](https://nextjs.org/) and [Nuxt](https://nuxtjs.org/) support this rendering strategy.  
React 和 Vue 等现代 UI JavaScript 框架，沿着 [Next.js](https://nextjs.org/) 和 [Nuxt](https://nuxtjs.org/) 等 Meta 框架都支持这种渲染策略。

Read more on [Rendering on the web](https://web.dev/rendering-on-the-web/) and ["Rebuilding our tech stack for the new Facebook.com" blog post](https://engineering.fb.com/2020/05/08/web/facebook-redesign/).  
阅读更多关于 [在网络上渲染](https://web.dev/rendering-on-the-web/) 和 [“为新的 Facebook.com 重建我们的技术堆栈“博客文章](https://engineering.fb.com/2020/05/08/web/facebook-redesign/) 。

---

## Data model 数据模型

A news feed shows a list of posts fetched from the server, hence most of the data involved in this application will be server-originated data. The only client-side data needed is form state for input fields in the post composer.  
新闻提要显示从服务器获取的帖子列表，因此此应用程序中涉及的大部分数据都是源自服务器的数据。唯一需要的客户端数据是 postcomposer 中输入字段的表单状态。

| Entity 实体 | Source 源 | Belongs to 属于 | Fields 领域 |
| --- | --- | --- | --- |
| `Feed` | Server 服务器 | Feed UI | `posts` (list of `Post` s), `pagination` (pagination metadata)   `posts` （ `Post` s 的列表）， `pagination` （pagination metadata） |
| `Post` | Server 服务器 | Feed post 馈电柱 | `id`, `created_time`, `content`, `author` (a `User`), `reactions`, `image_url` (for posts containing images)   `id` ， `created_time` ， `content` ， `author` （a `User` ）， `reactions` ， `image_url` （对于包含图片的帖子） |
| `User` | Server 服务器 | Client store 客户端存储 | `id`, `name`, `profile_photo_url`   `ID` 、 `  姓名  ` 、 ` 个人资料_照片_网址` |
| `NewPost` | User input (Client) 用户输入（客户端） | Post composer UI 后合成器 UI | `message`, `image`   `信息 ` 、 ` 图像` |

Although the `Post` and `Feed` entities belong to the feed post and feed UI respectively, all server-originated data can be stored in the client store and queried by the components which need them.  
尽管 `Post` 和 `Feed` 实体分别属于 feed post 和 feed UI，但所有源自服务器的数据都可以存储在客户端存储中，并由需要它们的组件进行查询。

The shape of the client store is not particularly important here, as long as it is in a format that can be easily retrieved from the components. New posts fetched from the second page should be joined with the previous posts into a single list with the pagination parameters (`cursor`) updated.  
客户端存储的形式在这里并不特别重要，只要它采用可以从组件中轻松检索的格式即可。从第二页获取的新帖子应该与以前的帖子加入一个单独的列表，并更新分页参数（ `  光标  ` ）。

### Advanced: Normalized store 高级：标准化商店

Both Facebook and Twitter use a normalized client side store. If the term "normalized" is new to you, have a read of [Redux's documentation on normalizing state shape](https://redux.js.org/usage/structuring-reducers/normalizing-state-shape). In a nutshell, normalized data stores:  
Facebook 和 Twitter 都使用规范化的客户端存储。如果您对“normalized”这个术语不熟悉，请阅读 [Redux 关于规范化状态形状的文档](https://redux.js.org/usage/structuring-reducers/normalizing-state-shape) 。简而言之，规范化的数据存储：

- Resemble databases where each type of data is stored in its own table.  
	类似于数据库，其中每种类型的数据都存储在自己的表中。
- Each item has a unique ID.  
	每个项目都有一个唯一的 ID。
- References across data types use IDs (like a foreign key) instead of having nested objects.  
	跨数据类型的引用使用 ID（如外键）而不是嵌套对象。

Facebook uses [Relay](https://relay.dev/) (which can normalize the data by virtue of knowing the GraphQL schema) while Twitter uses [Redux](https://redux.js.org/) as seen from the ["Dissecting Twitter's Redux Store" blog post](https://medium.com/statuscode/dissecting-twitters-redux-store-d7280b62c6b1).  
Facebook 使用 [Relay](https://relay.dev/) （它可以通过了解 GraphQL 模式来规范化数据），而 Twitter 使用 [Redux](https://redux.js.org/) ，如 [“剖析 Twitter 的 Redux 商店”博客文章](https://medium.com/statuscode/dissecting-twitters-redux-store-d7280b62c6b1) 所示。

The benefits of having a normalized store are:  
拥有一个标准化的商店的好处是：

- **Reduced duplicated data**: Single source of truth for the same piece of data that could be presented in multiple instances on the UI. E.g. if many posts are by the same author, we're storing duplicated data for the `author` field in the client store.  
	**减少重复数据** ：同一数据的单一真实来源，可以在 UI 上的多个实例中呈现。例如，如果许多帖子都是由同一个作者撰写的，我们会在客户端存储中为 `作者` 字段存储重复的数据。
- **Easily update all data for the same entity**: In the scenario that the feed post contains many posts authored by the user and that user changes their name, it'd be good to be able to immediately reflect the updated author name in the UI. This will be easier to do with a normalized store than a store that just stores the server response verbatim.  
	**轻松更新同一实体的所有数据** ：在 feed post 包含许多由用户创作的 post 并且用户更改了他们的名称的情况下，能够在 UI 中立即反映更新的作者名称是很好的。使用规范化的存储比使用仅逐字存储服务器响应的存储更容易做到这一点。

In the context of an interview, we don't really need to use a normalized store for a news feed because:  
在采访的上下文中，我们并不需要为新闻提要使用规范化的存储，因为：

- With the exception of the user/author fields, there isn't much duplicated data.  
	除了用户/作者字段之外，没有太多重复的数据。
- News feed is mostly for consuming information, there aren't many use cases to update data. Feed user interactions such as liking only affect data within a feed post.  
	新闻提要主要用于消费信息，没有太多的用例来更新数据。提要用户交互（如点赞）只会影响提要帖子中的数据。

Hence the upsides of using a normalized store is limited. In reality, Facebook and Twitter websites contain many other features that will benefit from the features a normalized store provides.  
因此，使用规范化存储的好处是有限的。实际上，Facebook 和 Twitter 网站包含许多其他功能，这些功能将受益于规范化商店提供的功能。

*Further reading: [Making Instagram.com faster: Part 3 — cache first](https://instagram-engineering.com/making-instagram-com-faster-part-3-cache-first-6f3f130b9669)  
进一步阅读： [让 Instagram.com 更快：第 3 部分-缓存优先](https://instagram-engineering.com/making-instagram-com-faster-part-3-cache-first-6f3f130b9669)*

---

## Interface definition (API) 接口定义（API）

| Source 源 | Destination 目的地 | API type API 类型 | Functionality 功能 |
| --- | --- | --- | --- |
| Server 服务器 | Controller 控制器 | HTTP | Fetch feed posts 获取源帖子 |
| Controller 控制器 | Server 服务器 | HTTP | Create new post 创建新职位 |
| Controller 控制器 | Feed UI | JavaScript | Pass feed posts data, Reactions   传递 feed 帖子数据，反应 |
| Post composer 后作曲家 | Controller 控制器 | JavaScript | Pass new post data 传递新的发布数据 |

The most interesting API to talk about will be the HTTP API to fetch a list of feed posts because the pagination approach is worth discussing. The HTTP API for fetching feed posts from the server has the basic details:  
最有趣的 API 是获取提要帖子列表的 HTTP API，因为分页方法值得讨论。用于从服务器获取提要帖子的 HTTP API 具有基本细节：

| Field 领域 | Value 值 |
| --- | --- |
| HTTP Method HTTP 方法 | `GET` |
| Path 路径 | `/feed` |
| Description 描述 | Fetches the feed results for a user.   获取用户的提要结果。 |

There are two common ways to return paginated content, each with its own pros and cons.  
返回分页内容有两种常见的方法，每种方法都有自己的优点和缺点。

- Offset-based pagination 基于偏移的分页
- Cursor-based pagination 基于指针的分页

### Offset-based pagination 基于偏移的分页

Offset-based pagination involves using an offset to specify where to start retrieving data and a limit to specify the number of items to retrieve. It's like saying, "Start from the 10th record and give me the next 5 items". The offset can either be an explicit number or converted from the requested page number. Requesting page 3 with a page size of 5 will translate to an offset of 10 because there are 2 pages before the 3rd page and 2 x 5 = 10. It's more common for an offset-based pagination API to accept the `page` parameter and the server will convert it into the `offset` value when querying the database.  
基于偏移量的分页涉及使用偏移量来指定从何处开始检索数据，并使用限制来指定要检索的项数。这就像说，“从第 10 条记录开始，给我接下来的 5 条”。偏移量可以是显式数字，也可以是从请求的页码转换而来的。请求页面大小为 5 的第 3 页将转换为偏移量 10，因为第 3 页之前有 2 个页面，2 x 5 = 10。更常见的是，基于偏移的分页 API 接受 `page` 参数，服务器在查询数据库时将其转换为 `偏移` 值。

An offset-based pagination API accepts the following parameters:  
基于偏移的分页 API 接受以下参数：

| Parameter 参数 | Type 类型 | Description 描述 |
| --- | --- | --- |
| `size` | number | Number of items per page   每页项目数 |
| `page` | number | Page number to fetch 要获取的页码 |

Given 20 items in a feed, parameters of `{size: 5, page: 2}` will return items 6 - 10 along with pagination metadata:  
给定提要中的 20 个项目， `{size：5，page：2}` 的参数将返回项目 6 - 10 沿着：

```json
{
  "pagination": {
    "size": 5,
    "page": 2,
    "total_pages": 4,
    "total": 20
  },
  "results": [
    {
      "id": "123",
      "author": {
        "id": "456",
        "name": "John Doe"
      },
      "content": "Hello world",
      "image": "https://www.example.com/feed-images.jpg",
      "reactions": {
        "likes": 20,
        "haha": 15
      },
      "created_time": 1620639583
    }
    // ... More posts.
  ]
}
```

and the underlying SQL query resembles:  
底层 SQL 查询类似于：

```sql
SELECT * FROM posts LIMIT 5 OFFSET 0; -- First page
SELECT * FROM posts LIMIT 5 OFFSET 5; -- Second page
```

Offset-based pagination has the following advantages:  
基于偏移的分页具有以下优点：

- Users can directly jump to a specific page.  
	用户可以直接跳转到特定页面。
- Easy to see the total number of pages.  
	很容易看到总页数。
- Easy to implement on the back end. The `OFFSET` value for a SQL query is calculated using `(page - 1) * size`.  
	易于在后端实现。SQL 查询的 `OFFSET` 值使用 `（page - 1）* size` 计算。
- Easily used with various database systems and not tied to specific data storage mechanisms  
	易于与各种数据库系统一起使用，不受特定数据存储机制的约束

However, offset-based pagination comes with some issues:  
但是，基于偏移的分页也有一些问题：

**Inaccurate page results**: For data that updates frequently, the current page window might be inaccurate after some time. Imagine a user has fetched the first 5 posts in their feed. After sometime, 5 more posts were added. If the users scroll to the bottom of the feed and fetches page 2, the same posts in the original page 1 will be fetched, and the user will see duplicate posts.  
**不准确的页面结果** ：对于经常更新的数据，当前页面窗口可能在一段时间后不准确。想象一下，一个用户在他们的提要中获取了前 5 个帖子。过了一段时间，又增加了 5 个帖子。如果用户滚动到提要的底部并获取页面 2，则原始页面 1 中的相同帖子将被获取，并且用户将看到重复的帖子。

```
// Initial posts (newest on the left, oldest on the right)
Posts: A, B, C, D, E, F, G, H, I, J
       ^^^^^^^^^^^^^ Page 1 contains A - E

// New posts added over time
Posts: K, L, M, N, O, A, B, C, D, E, F, G, H, I, J
                      ^^^^^^^^^^^^^ Page 2 also contains A - E
```

Clients can try to be smart and de-duplicate posts by not showing posts that are already visible. However this requires custom logic and the client will have to make a new request to make up for the lack of new posts which costs an extra network roundtrip. For use cases where the number of items can reduce over time, pages can end up missing some items instead.  
客户端可以尝试通过不显示已经可见的帖子来聪明地删除重复的帖子。然而，这需要自定义逻辑，客户端将不得不发出一个新的请求，以弥补缺乏新的职位，这需要额外的网络往返。对于项目数量会随着时间的推移而减少的用例，页面最终可能会丢失一些项目。

**Page size cannot be easily changed**: Another downside of offset-based pagination is that the client cannot change the page size for subsequent queries since the offset is a product of the page size and the page being requested.  
**页面大小不容易更改** ：基于偏移的分页的另一个缺点是客户端无法更改后续查询的页面大小，因为偏移量是页面大小和所请求页面的乘积。

| Page 页面 | Page size 页面大小 | Results 结果 |
| --- | --- | --- |
| 1 | 5 | Items 1 - 5 项目1 - 5 |
| 2 | 5 | Items 6 - 10 项目6 - 10 |
| 2 | 7 | Items 8 - 14 项目8 - 14 |

In the above example, the client will miss out on items 6 and 7 if it goes from `{page: 1, size: 5}` to `{page: 2, size: 7}`.  
在上面的例子中，如果客户端从 `{page：1，size：5}` 到 `{page：2，size：7}，` 它将错过项目 6 和 7。

**Query performance degrades over time**: Lastly, query performance degrades as the table becomes larger. For huge offsets (e.g. `OFFSET 1000000`) the database still has to read up to those `count` + `offset` rows, discard the `offset` rows and only return the `count` rows, which results in very poor query performance for large offsets. This is considered back end knowledge but it's useful to know and you might get brownie points for mentioning it.  
**查询性能会随着时间的推移而降低** ：最后，查询性能会随着表的变大而降低。对于巨大的偏移量（例如 `OFFSET 1000000` ），数据库仍然必须读取那些 `count` + `offset` 行，丢弃 `偏移量` 行，只返回 `count` 行，这导致大偏移量的查询性能非常差。这被认为是后端知识，但它是有用的，你可能会得到布朗尼点提到它。

Offset-based pagination is common in web applications for displaying lists like search results, where jumping to a specific page is required and the results don't update too quickly. Hence blogs, travel booking websites, e-commerce websites will benefit from using offset-based pagination for their search results.  
基于偏移的分页在显示搜索结果等列表的 Web 应用程序中很常见，需要跳转到特定页面，并且结果不会更新得太快。因此，博客，旅游预订网站，电子商务网站将受益于使用基于偏移的分页为他们的搜索结果。

### Cursor-based pagination 基于指针的分页

Cursor-based pagination uses a pointer (the cursor) to a specific record in a dataset. Instead of saying "give me items 11 to 15", it says "give me 5 items starting after \[specific item\].".  
基于游标的分页使用指向数据集中特定记录的指针（游标）。而不是说“给我项目11至15”，它说“给我5个项目开始后\[特定项目\]。".

The cursor is usually a unique identifier, which can be the item id, timestamp, or something else. Subsequent requests use the identifier of the last item as the cursor to fetch the next set of items. In SQL, an example is:  
游标通常是一个唯一的标识符，它可以是项目 ID、时间戳或其他内容。随后的请求使用最后一项的标识符作为游标来获取下一组项。在 SQL 中，一个例子是：

```sql
SELECT * FROM table WHERE id > cursor LIMIT 5.
```

A cursor-based pagination API accepts the following parameters:  
基于光标的分页 API 接受以下参数：

| Parameter 参数 | Type 类型 | Description 描述 |
| --- | --- | --- |
| `size` | number | Number of results per page   每页显示的结果数 |
| `cursor` | string 字符串 | An identifier for the last item fetched. The database query will make use of this identifier.   获取的最后一项的标识符。数据库查询将使用此标识符。 |

```json
{
  "pagination": {
    "size": 10,
    "next_cursor": "=dXNlcjpVMEc5V0ZYTlo"
  },
  "results": [
    {
      "id": "123",
      "author": {
        "id": "456",
        "name": "John Doe"
      },
      "content": "Hello world",
      "image": "https://www.example.com/feed-images.jpg",
      "reactions": {
        "likes": 20,
        "haha": 15
      },
      "created_time": 1620639583
    }
    // ... More posts.
  ]
}
```

Advantages of cursor-based pagination:  
基于光标的分页的优点：

- More efficient and faster on large datasets.  
	在大型数据集上更高效、更快速。
- Avoids the inaccurate page window problem because new posts added over time do not affect the offset, which is determined by a fixed cursor. Great for real-time data.  
	避免了页面窗口不准确的问题，因为随着时间的推移添加的新文章不会影响偏移量，这是由固定光标决定的。非常适合实时数据。

[Facebook](https://developers.facebook.com/docs/graph-api/reference/page/feed/), [Slack](https://api.slack.com/docs/pagination#cursors), [Zendesk](https://developer.zendesk.com/documentation/developer-tools/pagination/paginating-through-lists-using-cursor-pagination/) use cursor-based pagination for their developer APIs.  
[Facebook](https://developers.facebook.com/docs/graph-api/reference/page/feed/) ， [Slack](https://api.slack.com/docs/pagination#cursors) ， [Zendesk](https://developer.zendesk.com/documentation/developer-tools/pagination/paginating-through-lists-using-cursor-pagination/) 在他们的开发者 API 中使用基于光标的分页。

Downsides of cursor-based pagination:  
基于光标的分页的缺点：

- Since the client doesn't know the cursor, it cannot jump to a specific page without going through the previous pages.  
	由于客户端不知道光标，它不能跳到一个特定的页面，而不通过以前的页面。
- Slightly more complex to implement compared to offset-based pagination.  
	与基于偏移的分页相比，实现起来稍微复杂一些。

In order for the back end to implement cursor-based pagination, the database needs to uniquely map the cursor to a row, which can be done using a database table's primary key or in some cases, the timestamp.  
为了让后端实现基于游标的分页，数据库需要将游标唯一地映射到一行，这可以使用数据库表的主键或在某些情况下使用时间戳来完成。

### Which pagination to use for news feed? 新闻源使用哪种分页？

In a nutshell, the choice between offset-based pagination and cursor-based pagination largely depends on the data and requirements. Offset-based is simpler and better for static or small datasets where direct access to pages is important. Cursor-based is more efficient and reliable for large, dynamic datasets where the data sequence is important and changes frequently.  
简而言之，基于偏移量的分页和基于光标的分页之间的选择在很大程度上取决于数据和需求。基于偏移的更简单，更适合于静态或小型数据集，其中直接访问页面很重要。对于数据序列很重要且经常变化的大型动态数据集，基于指针的方法更有效、更可靠。

For an infinite scrolling news feed where:  
对于无限滚动的新闻提要，其中：

- New posts can be added frequently to the top of the feed.  
	新的帖子可以经常添加到提要的顶部。
- Newly fetched posts are appended to the end of the feed.  
	新获取的帖子会被附加到提要的末尾。
- Table size can grow pretty quickly.  
	表的大小可以快速增长。

Cursor-based pagination is clearly superior and should be used for a news feed.  
基于光标的分页显然是上级的，应该用于新闻提要。

*Reference: [Evolving API Pagination at Slack](https://slack.engineering/evolving-api-pagination-at-slack)  
参考： [Slack 上不断发展的 API 分页](https://slack.engineering/evolving-api-pagination-at-slack)*

### Post creation 岗位创建

This HTTP method is for users to create a new post, which will be shown in their own feed as well as others who they are friends with.  
这个 HTTP 方法是让用户创建一个新的帖子，它将显示在他们自己的提要中以及他们的朋友中。

| Field 领域 | Value 值 |
| --- | --- |
| HTTP Method HTTP 方法 | `POST` |
| Path 路径 | `/posts` |
| Description 描述 | Creates a new post.创建新帖子。 |
| Parameters 参数 | `{ body: '...', media: '...' }` |

The parameters of the HTTP API will depend on the type of post made. In most cases, it's not a key discussion point during an interview.  
HTTP API 的参数将取决于发布的类型。在大多数情况下，这不是面试中的一个关键讨论点。

The response format can be either just the single post, or a list of latest posts in the feed. If a single post is returned, the API response will be similar to the feed post item within the feed API:  
响应格式可以是单个帖子，也可以是提要中最新帖子的列表。如果返回单个 post，则 API 响应将类似于 feed API 中的 feed post 项：

```json
{
  "id": "124",
  "author": {
    "id": "456",
    "name": "John Doe"
  },
  "content": "Hello world",
  "image": {
    "src": "https://www.example.com/feed-images.jpg",
    "alt": "An image alt" // Either user-provided, or generated on the server.
    // Other useful properties can be included too, such as dimensions.
  },
  "reactions": {
    "likes": 20,
    "haha": 15
  },
  "created_time": 1620639583
}
```

Given this new post data, the client store will need to prepend it to the start of the feed list.  
给定这个新的 post 数据，客户端存储需要将其添加到提要列表的开头。

---

## Optimizations and deep dive 优化和深度挖掘

As there are a few sections within a news feed application, it will be more organized to focus on one section at a time and looking at the optimizations that can be done on specific sections section:  
由于新闻提要应用程序中有几个部分，因此一次关注一个部分并查看可以在特定部分上进行的优化会更有组织：

- [General optimizations 一般优化](https://www.greatfrontend.com/questions/system-design/#general-optimizations)
- [Feed list optimizations 订阅源列表优化](https://www.greatfrontend.com/questions/system-design/#feed-list-optimizations)
- [Feed post optimizations 饲料岗位优化](https://www.greatfrontend.com/questions/system-design/#feed-post-optimizations)
- [Feed composer optimizations  
	Feed Composer 优化](https://www.greatfrontend.com/questions/system-design/#feed-composer-optimizations)

### General optimizations 一般优化

These optimizations are applicable to every section of the page.  
这些优化适用于页面的每个部分。

#### Code splitting JavaScript for faster performance 代码拆分 JavaScript 以提高性能

As an application grows, the number of pages and features increase which will result in more JavaScript and CSS code needed to run the application. Code splitting is a technique to split code needed on a page into separate files so that they can be loaded in parallel or when they're needed.  
随着应用程序的增长，页面和功能的数量也会增加，这将导致运行应用程序所需的更多 JavaScript 和 CSS 代码。代码拆分是一种将页面上所需的代码拆分到单独文件中的技术，以便它们可以并行加载或在需要时加载。

Generally, code splitting can be done on two levels:  
通常，代码分割可以在两个级别上完成：

- **Split on the page level**: Each page will only load the JavaScript and CSS needed on that page.  
	**在页面级别上拆分** ：每个页面将只加载该页面所需的 JavaScript 和 CSS。
- **Lazy loading resources within a page**: Load non-critical resources only when needed or after the initial render, such as code that's only needed lower down on the page, or code that's used only when interacted with (e.g. modals, dialogs).  
	**延迟加载页面内的资源** ：仅在需要时或初始呈现后加载非关键资源，例如仅在页面较低位置需要的代码，或仅在交互时使用的代码（例如模态，对话框）。

For a news feed application, there's only a single page, so page-level code splitting is not too relevant, however lazy loading can still be super useful for other purposes. Lazy loading is discussed in more detail under the feed post section as it's most relevant to the feed post UI.  
对于新闻提要应用程序来说，只有一个页面，因此页面级代码拆分并不太重要，但是延迟加载对于其他用途仍然非常有用。延迟加载将在 feed post 一节中详细讨论，因为它与 feed post UI 最相关。

For reference, Facebook splits their JavaScript loading into 3 tiers:  
作为参考，Facebook 将其 JavaScript 加载分为 3 层：

- **Tier 1**: Basic layout needed to display the first paint for the above-the-fold content, including UI skeletons for initial loading states.  
	**第 1 层** ：显示折叠上方内容的第一个绘制所需的基本布局，包括初始加载状态的 UI 框架。
- **Tier 2**: JavaScript needed to fully render all above-the-fold content. After Tier 2, nothing on the screen should still be visually changing as a result of code loading.  
	**第 2 层** ：需要 JavaScript 来完全呈现所有上层内容。在 Tier 2 之后，屏幕上的任何内容都不会因代码加载而发生视觉变化。
- **Tier 3**: Resources that are only needed after display that doesn't affect the current pixels on the screen, including logging code and subscriptions for live-updating data.  
	**第 3 层** ：仅在显示后需要的资源，不影响屏幕上的当前像素，包括日志代码和实时更新数据的订阅。

*Source: ["Rebuilding our tech stack for the new Facebook.com" blog post](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)  
来源： [“Rebuilding our tech stack for the new Facebook.com“博客文章](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)*

#### Keyboard shortcuts 键盘快捷键

Facebook has a number of news feed-specific shortcuts to help users navigate between the posts and perform common actions, super handy! Try it for yourself by hitting the " Shift + ?" keys on facebook.com.  
Facebook 有许多新闻提要特定的快捷方式，以帮助用户在帖子之间导航并执行常见操作，超级方便！在 facebook.com 上按下“ Shift + ? ”键，自己尝试一下。

![[_posts/architect/System Design/greatfrontend.com/questions/media/42feb94a75f0e6a72d52e728646d29c4_MD5.png]]

*Source: [Making Facebook.com accessible to as many people as possible](https://engineering.fb.com/2020/07/30/web/facebook-com-accessibility/)  
来源： [让尽可能多的人访问 Facebook.com](https://engineering.fb.com/2020/07/30/web/facebook-com-accessibility/)*

Clearly display error states if any network requests have failed, or when there's no network connectivity.  
如果任何网络请求失败或没有网络连接，则清楚地显示错误状态。

### Feed list optimizations 订阅源列表优化

The feed list refers to the container element that contains the feed post items.  
提要列表引用包含提要发布项的容器元素。

#### Infinite scrolling 无限滚动

An infinite scrolling feed works by fetching the next set of posts when the user has scrolled to the end of their current loaded feed. This results in the user seeing a loading indicator and a short delay where the user has to wait for the new posts to be fetched and displayed.  
无限滚动提要的工作原理是，当用户滚动到当前加载提要的末尾时，获取下一组帖子。这导致用户看到加载指示符和用户必须等待新帖子被获取和显示的短暂延迟。

A way to reduce or entirely eliminate the waiting time is to load the next set of feed posts before the user hits the bottom of the page so that the user never has to see any loading indicators.  
减少或完全消除等待时间的一种方法是在用户点击页面底部之前加载下一组提要帖子，这样用户就不必看到任何加载指示符。

A trigger distance of around one viewport height should be sufficient for most cases. The ideal distance is short enough to avoid false positives and wasted bandwidth but also wide enough to load the rest of the contents before the user scrolls to the bottom of the page. A dynamic distance can be calculated based on the network connection speed and how fast the user is scrolling through the feed.  
在大多数情况下，一个视口高度左右的触发距离应该足够了。理想的距离要足够短，以避免误报和浪费带宽，但也要足够宽，以在用户滚动到页面底部之前加载其余内容。动态距离可以根据网络连接速度和用户滚动提要的速度来计算。

There are two popular ways to implement infinite scroll. Both involve rendering a marker element at the bottom of the feed:  
有两种流行的方法来实现无限滚动。两者都涉及在提要底部呈现标记元素：

1. **Listen for the `scroll` event**: Add a `scroll` event listener (ideally throttled) to the page or a timer (via `setInterval`) that checks whether the position of the marker element is within a certain threshold from the bottom of the page. The position of the marker element can be obtained using [`Element.getBoundingClientRect`](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect).  
	**侦听 `滚动` 事件** ：向页面添加一个 `滚动` 事件侦听器（理想情况下是节流的）或一个计时器（通过 `setInterval` ），用于检查标记元素的位置是否在页面底部的特定阈值内。可以使用 [`Element.getBoundingClientRect`](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect) 获得标记元素的位置。
2. **Intersection Observer API**: Use the [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) to monitor when the marker element is entering or exiting another element or intersecting by a specified amount.  
	**相交观察器 API** ：使用 [相交观察器 API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API) 监视标记元素何时进入或退出另一个元素或相交指定的量。

The Intersection Observer API is a browser native API and is preferred over `Element.getBoundingClientRect()`.  
Intersection Observer API 是浏览器本机 API，优于 `Element.getBoundingClientRect()` 。

> The Intersection Observer API lets code register a callback function that is executed whenever an element they wish to monitor enters or exits another element (or the viewport), or when the amount by which the two intersect changes by a requested amount. This way, sites no longer need to do anything on the main thread to watch for this kind of element intersection, and the browser is free to optimize the management of intersections as it sees fit.  
> Intersection Observer API 允许代码注册一个回调函数，每当它们希望监视的元素进入或退出另一个元素（或视口）时，或者当两个元素相交的量按请求的量变化时，都会执行该回调函数。这样，网站不再需要在主线程上做任何事情来监视这种元素交叉，浏览器可以自由地优化交叉点的管理。

*Source: [Intersection Observer API | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)  
来源： [Intersection Observer API| MDN](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)*

#### Virtualized lists 虚拟化列表

With infinite scrolling, all the loaded feed items are on one single page. As the user scrolls further down the page, more posts are appended to the DOM and with feed posts having complex DOM structure (lots of details to render), the DOM size rapidly increases. As social media websites are long-lived applications (especially if single-page app) and the feed items list can easily grow really long quickly, the number of feed items can be a cause of performance issues in terms of DOM size, rendering, and browser memory usage.  
通过无限滚动，所有加载的提要项都在一个页面上。当用户进一步向下滚动页面时，更多的帖子被附加到 DOM，并且具有复杂 DOM 结构的提要帖子（需要呈现大量细节），DOM 大小迅速增加。由于社交媒体网站是长期存在的应用程序（特别是单页应用程序），并且提要项列表可以很容易地快速增长，因此提要项的数量可能会导致 DOM 大小，渲染和浏览器内存使用方面的性能问题。

Virtualized lists is a technique to render only the posts that are within the viewport. In practice, Facebook replaces the contents of off-screen feed posts with empty `<div>` s, add dynamically calculated inline styles (e.g. `style="height: 300px"`) to set the height of the posts so as to preserve the scroll position and add the [`hidden` attribute](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/hidden) to them. This will improve rendering performance in terms of:  
虚拟化列表是一种仅渲染视口中的帖子的技术。在实践中，Facebook 将屏幕外提要帖子的内容替换为空 `的<div>` s，添加动态计算的内联样式（例如 `style=“height：300 px”` ）来设置帖子的高度，以保留滚动位置并为其添加 [`隐藏` 属性](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/hidden) 。这将在以下方面提高渲染性能：

- **Browser painting**: Fewer DOM nodes to render and fewer layout computations to be made.  
	**浏览器绘制** ：需要渲染的 DOM 节点更少，需要进行的布局计算也更少。
- **Virtual DOM reconciliation (React-specific)**: Since the post is now a simpler empty version, it's easier for React (the UI library that Facebook is using to render the feed) to diff the virtual DOM vs the real DOM to determine what DOM updates have to be made.  
	**Virtual DOM reconciliation（React 专用）** ：由于帖子现在是一个更简单的空版本，React（Facebook 用来渲染提要的 UI 库）更容易区分虚拟 DOM 和真实的 DOM，以确定必须进行哪些 DOM 更新。

Both Facebook and Twitter websites use virtualized lists.  
Facebook 和 Twitter 网站都使用虚拟列表。

For users who scroll really fast, even though the browser kicks off the request for the next set of posts before the user even reaches the bottom of the page, the request might not have returned yet and a loading indicator should be shown to reflect the request status.  
对于滚动速度非常快的用户，即使浏览器在用户到达页面底部之前就开始请求下一组帖子，请求可能还没有返回，应该显示加载指示器以反映请求状态。

Rather than showing a spinner, a better experience would be to use a [shimmer loading effect](https://docs.flutter.dev/cookbook/effects/shimmer-loading) which resembles the contents of the posts. This looks more aesthetically pleasing and can be used also reduce layout thrash after the new posts are loaded.  
而不是显示一个微调，一个更好的经验将是使用 [微光加载效果](https://docs.flutter.dev/cookbook/effects/shimmer-loading) ，类似的内容的职位。这看起来更美观，也可以用来减少新文章加载后的布局颠簸。

An example of Facebook's feed loading shimmer:  
一个 Facebook 的 Feed 加载微光的例子：

![[_posts/architect/System Design/greatfrontend.com/questions/media/11cd4b0a6c14c8c78e18881e31097f32_MD5.jpg]]

As mentioned above in the "Interface" section, cursor-based pagination is more suitable for a news feed. A benefit of cursor-based pagination is that the client can change how many items to fetch in subsequent calls. We can use this to our advantage by customizing the number of posts to load based on the browser window height.  
正如前面在“界面”一节中提到的，基于光标的分页更适合于新闻提要。基于指针的分页的一个好处是，客户端可以在后续调用中更改要获取的项目数量。我们可以根据浏览器窗口的高度自定义要加载的帖子的数量。

For the initial load, we do not know the window height so we need to be conservative and overfetch the number of posts needed. But for subsequent fetches, we know the browser window height and can customize the number of posts to fetch based on that.  
对于初始负载，我们不知道窗口的高度，所以我们需要保守并过度提取所需的帖子数量。但是对于后续的抓取，我们知道浏览器窗口的高度，并可以根据它自定义要抓取的帖子数量。

#### Preserving feed scroll position on remounting 重新安装时保持送纸卷轴位置

Feed scroll positions should be preserved if users navigate to another page and back to the feed. This can be achieved in single-page applications if the feed list data is cached within the client store along with the scroll position. When the user goes back to the feed page, since the data is already on the client, the feed list can be read from the client store and immediately presented on the screen with the previous scroll position; no server round-trip is needed.  
如果用户导航到另一个页面并返回到提要，则应保留提要滚动位置。如果提要列表数据与滚动位置一起沿着在客户端存储中，则可以在单页应用程序中实现这一点。当用户返回到提要页面时，由于数据已经在客户端上，因此可以从客户端存储中读取提要列表，并立即在屏幕上显示先前的滚动位置;不需要服务器往返。

#### Stale feeds 陈饲料

It's not uncommon for users to leave their news feed application open as a browser tab and not refresh it at all. It'd be a good idea to prompt the user to refresh or refetch the feed if the last fetched timestamp is more than a few hours ago, as there can be new posts and the loaded feed is considered stale. When a new feed is refetched, the current feed can be entirely removed from memory to free up memory space.  
用户将其新闻提要应用程序作为浏览器选项卡打开并且根本不刷新它并不罕见。如果最后获取的时间戳超过几个小时，提示用户刷新或重新获取提要是一个好主意，因为可能有新的帖子，加载的提要被认为是过时的。当重新获取新的提要时，可以将当前提要从内存中完全删除以释放内存空间。

Another approach is to automatically append new feed posts to the top of the feed, but that might not be desired and extra care has to be made in order not to affect the scroll position.  
另一种方法是自动将新的提要帖子附加到提要的顶部，但这可能不是期望的，并且必须额外小心以免影响滚动位置。

As of writing, Facebook forces a feed refresh and scrolls to the top if the tab has been left open for some duration.  
在撰写本文时，Facebook 强制刷新提要，并在标签页打开一段时间后滚动到顶部。

### Feed post optimizations 饲料岗位优化

The feed post refers to the individual post element that contains details of the post: author, timestamp, contents, like/commenting buttons.  
feed post 是指包含 post 详细信息的单独 post 元素：作者，时间戳，内容，喜欢/评论按钮。

#### Delivering data-driven dependencies only when needed 仅在需要时提供数据驱动的依赖关系

News feed posts can come in many different formats (text, image, videos, polls, etc) and each post requires custom rendering code. In reality, Facebook feed supports over 50 different post formats!  
新闻提要帖子可以有许多不同的格式（文本，图像，视频，民意调查等），每个帖子都需要自定义渲染代码。事实上，Facebook Feed 支持超过 50 种不同的帖子格式！

One way to support all the post formats on the client is to have the client load the component JavaScript code for all possible formats upfront so that any kind of feed post format can be rendered. However, not all users' feed will contain all the post formats and there will likely be a lot of unused JavaScript. With the large variety of feed post formats, loading the JavaScript code for all of them upfront is sure to cause performance issues.  
在客户机上支持所有 post 格式的一种方法是让客户机预先为所有可能的格式加载组件 JavaScript 代码，以便可以呈现任何类型的提要 post 格式。然而，并不是所有用户的提要都包含所有的帖子格式，并且可能会有很多未使用的 JavaScript。由于提要发布格式的多样性，预先为所有格式加载 JavaScript 代码肯定会导致性能问题。

If only we could lazy load components depending on the data received! That's already possible but requires an extra network round-trip to lazy load the components after the data is fetched and we know the type of posts rendered.  
如果我们能根据接收到的数据延迟加载组件就好了！这已经是可能的，但需要额外的网络往返，以便在获取数据后延迟加载组件，并且我们知道呈现的帖子的类型。

Facebook fetches data from the server using [Relay](https://relay.dev/), which is a JavaScript-based GraphQL client. Relay couples React components with GraphQL to allow React components to declare exactly which data fields are needed and Relay will fetch them via GraphQL and provide the components with data. Relay has a feature called [data-driven dependencies](https://relay.dev/docs/glossary/#match) via the `@match` and `@module` GraphQL directives, which fetches component code along with the respective type of data, effectively solving the excess components problem mentioned above without an extra network round-trip. You only load the relevant code when a particular format for a post is being shown.  
Facebook 使用 [Relay](https://relay.dev/) 从服务器获取数据，Relay 是一个基于 JavaScript 的 GraphQL 客户端。Relay 将 React 组件与 GraphQL 耦合，以允许 React 组件准确声明需要哪些数据字段，Relay 将通过 GraphQL 获取它们并向组件提供数据。Relay 通过 `@match` 和 `@module` GraphQL 指令有一个称为 [数据驱动依赖的](https://relay.dev/docs/glossary/#match) 特性，它可以获取组件代码沿着相应类型的数据，有效地解决了上面提到的多余组件问题，而无需额外的网络往返。只有当文章的特定格式被显示时，你才加载相关的代码。

```js
// Sample GraphQL query to demonstrate data-driven dependencies.
... on Post {
  ... on TextPost {
    @module('TextComponent.js')
    contents
  }
  ... on ImagePost {
    @module('ImageComponent.js')
    image_data {
      alt
      dimensions
    }
  }
}
```

The above GraphQL query tells the back end to return the `TextComponent` JavaScript code along with the text contents if the post is a text-based post and return the `ImageComponent` JavaScript code along with the image data if the post has image attachments. There is no need for the client to load component JavaScript code for all the possible post formats upfront, reducing the initial JavaScript needed on the page.  
上面的 GraphQL 查询告诉后端，如果帖子是基于文本的帖子，则返回 `TextComponent` JavaScript 代码以及文本内容;如果帖子具有图像附件，则返回 `ImageComponent` JavaScript 代码沿着图像数据。客户端不需要预先为所有可能的发布格式加载组件 JavaScript 代码，从而减少了页面上所需的初始 JavaScript。

*Source: [Rebuilding our tech stack for the new Facebook.com](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)  
来源： [为新的 Facebook.com 重建我们的技术堆栈](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)*

#### Rendering mentions/hashtags 渲染提及/标签

You may have noticed that textual content within feed posts can be more than plain text. For social media applications, it is common to see mentions and hashtags.  
您可能已经注意到，提要帖子中的文本内容可能不仅仅是纯文本。对于社交媒体应用程序，常见的是看到提及和标签。

![[_posts/architect/System Design/greatfrontend.com/questions/media/907b77e52e7b8dc9e87570ff2e0b0b81_MD5.png]]

In Stephen Curry's post above, see that he used the "#AboutLastNight" hashtag and mentioned the "HBO Max" Facebook page. His post message has to be stored in a special format such that it contains metadata about these tags and mentions.  
在上面 Stephen 咖喱的帖子中，可以看到他使用了“#AboutLastNight”标签，并提到了“HBO Max”Facebook 页面。他的帖子必须以特殊格式存储，以便包含有关这些标签和提及的元数据。

What format should the message be so that it can store data about mentions/hashtags? Let's discuss the possible formats and their pros and cons.  
消息应该是什么格式，以便它可以存储有关提及/标签的数据？让我们讨论可能的格式及其优缺点。

**HTML format**: The simplest format is HTML, you store the message the way you want it to be displayed.  
**HTML 格式** ：最简单的格式是 HTML，您可以按照您希望的显示方式存储消息。

```md
<a href="...">#AboutLastNight</a> is here... and ready to change the meaning of date night...

Absolute comedy 🤣 Dropping 2/10 on <a href="...">HBO Max</a>!
```

Storing as HTML is usually bad because there's a potential for causing a Cross-Site Scripting (XSS) vulnerability. Also, in most cases it's better to decouple the message's metadata from displaying, perhaps in future you want to decorate the mentions/hashtags before rendering and want to add classnames to the links. HTML format also makes the API less reusable on non-web clients (e.g. iOS/Android).  
存储为 HTML 通常是不好的，因为有可能导致跨站点脚本（XSS）漏洞。此外，在大多数情况下，最好将消息的元数据与显示分离开来，也许将来您想要在呈现之前装饰提及/主题标签，并想要向链接添加类名。HTML 格式还使得 API 在非 Web 客户端（例如 iOS/Android）上的可重用性降低。

**Custom syntax**: A custom syntax can be used to capture metadata about hashtags and mentions.  
**自定义语法** ：自定义语法可用于捕获关于主题标签和提及的元数据。

- **Hashtags**: Hashtags don't actually need a special syntax, words that start with a "#" can be considered a hashtag.  
	**主题标签** ：主题标签实际上不需要特殊的语法，以“#”开头的单词可以被认为是一个主题标签。
- **Mentions**: A syntax like `[[#1234: HBO Max]]` is sufficient to capture the entity ID and the text to display. It's not sufficient to just store the entity ID because sites like Facebook allow users to customize the text within the mention.  
	**注意** ：像 `[[#1234：HBO Max]]` 这样的语法足以捕获实体 ID 和要显示的文本。仅仅存储实体 ID 是不够的，因为像 Facebook 这样的网站允许用户自定义提及中的文本。

Before rendering the message, the string can be parsed for hashtags and mentions using regex and replaced with custom-styled links. Custom syntax is a lightweight solution which is robust enough if you don't anticipate new kinds of rich text entities to be supported in future.  
在呈现消息之前，可以使用正则表达式解析字符串中的主题标签和提及，并替换为自定义样式的链接。自定义语法是一个轻量级的解决方案，如果您不期望将来支持新类型的富文本实体，它就足够健壮。

**Rich text editor format**: [Draft.js](https://draftjs.org/) is a popular rich text editor by Meta for composing rich text. Draft.js allows users to extend the functionality and create their own rich text entities such as hashtags and mentions. It defines a custom Draft.js editor state format which is being used by the Draft.js editor. In 2022, Meta released [Lexical](https://lexical.dev/), a successor to Draft.js and is using Lexical for rich text editing and displaying rich text entities on facebook.com. The underlying formats are similar and we will discuss Draft.js' format.  
**富文本编辑器格式** ： [Draft.js](https://draftjs.org/) 是 Meta 的一个流行的富文本编辑器，用于编写富文本。Draft.js 允许用户扩展功能并创建自己的富文本实体，如主题标签和提及。它定义了 Draft.js 编辑器使用的自定义 Draft.js 编辑器状态格式。2022 年，Meta 发布 [了 Lexical](https://lexical.dev/) ，这是 Draft.js 的继任者，并正在使用 Lexical 进行富文本编辑，并在 facebook.com 上显示富文本实体。底层格式相似，我们将讨论 Draft.js 的格式。

Draft.js is just one example of a rich text format, there are many out there to choose from. The editor state resembles an Abstract Syntax Tree and can be serialized into a JSON string to be stored. The benefits of using a popular rich text format is that you don't have to write custom parsing code and can easily extend to more types of rich text entities in future. However, these formats tend to be longer strings than a custom syntax version and will result in larger network payload size and require more disk space to store.  
Draft.js 只是富文本格式的一个例子，有很多可供选择。编辑器状态类似于抽象树，可以序列化为 JSON 字符串进行存储。使用流行的富文本格式的好处是，您不必编写自定义解析代码，并且将来可以轻松地扩展到更多类型的富文本实体。但是，这些格式往往是比自定义语法版本更长的字符串，并将导致更大的网络有效负载大小，并需要更多的磁盘空间来存储。

An example of how the post above can be represented in Draft.js.  
上面的帖子如何在 Draft.js 中表示的示例。

```js
{
  content: [
    {
      type: 'HASHTAG',
      content: '#AboutLastNight',
    },
    {
      type: 'TEXT',
      content: ' is here... and ready to change ... Dropping 2/10 on ',
    },
    {
      type: 'MENTION',
      content: 'HBO Max',
      entityID: 1234,
    },
    {
      type: 'TEXT',
      content: '!',
    },
  ];
}
```

#### Rendering images 渲染图像

Since there can be images in a feed post, we can also briefly discuss some image optimization techniques:  
由于 feed post 中可能会有图片，我们也可以简要讨论一些图片优化技术：

- **Content Delivery Network (CDN)**: Use a (CDN) to host and serve images for faster loading performance.  
	**内容交付网络（CDN）** ：使用（CDN）托管和提供图像，以获得更快的加载性能。
- **Modern image formats**: Use modern image formats such as [WebP](https://developers.google.com/speed/webp) which provides superior lossless and lossy image compression.  
	**现代图像格式** ：使用现代图像格式，如 [WebP](https://developers.google.com/speed/webp) ，它提供了上级无损和有损图像压缩。
- **`<img>` s should use proper `alt` text  
	`<img> %` s 应使用正确 `的 alt` 文本**
	- Facebook provides `alt` text for user-uploaded images by using Machine Learning and Computer Vision to process the images and generate a description.  
		Facebook 通过使用机器学习和计算机视觉来处理图像并生成描述，为用户上传的图像提供 `alt` 文本。
	- Generative AI models are also very good at doing that these days.  
		如今，生成式 AI 模型也非常擅长这样做。
- Image loading based on device screen properties  
	基于设备屏幕属性的图像加载
	- Send the browser dimensions in the feed list requests so that server can decide what image size to return.  
		在提要列表请求中发送浏览器尺寸，以便服务器可以决定返回的图像大小。
	- Use `srcset` if there are image processing (resizing) capabilities to load the most suitable image file for the current viewport.  
		如果有图像处理（ImageProcessing `，简写为 srcset` ）功能，可以使用 srcset 为当前视口加载最合适的图像文件。
- Adaptive image loading based on network speed  
	基于网络速度的自适应图像加载
	- **Devices with good internet connectivity/on WiFi**: Prefetch offscreen images that are not in the viewport yet but about to enter viewport.  
		**具有良好互联网连接/WiFi 的设备** ：预取尚未在视口中但即将进入视口的屏幕外图像。
	- **Poor internet connection**: Render a low-resolution placeholder image and require users to explicitly click on them to load the high-resolution image.  
		**互联网连接不良** ：渲染低分辨率占位符图像，并要求用户显式单击它们以加载高分辨率图像。

#### Lazy load code that is not needed for initial render 延迟加载初始呈现不需要的代码

Many interactions with a feed post are not needed on initial render:  
许多与 feed post 的交互在初始渲染时是不需要的：

- Reactions popover.反应 popover。
- Dropdown menu revealed by the top-right ellipsis icon button, which is usually meant to conceal additional actions.  
	下拉菜单由右上方的省略号图标按钮显示，通常用于隐藏其他操作。

The code for these components can be downloaded when:  
以下情况下可以下载这些组件的代码：

- The browser is idle as a lower-priority task.  
	浏览器作为低优先级任务处于空闲状态。
- On demand, when the user hovers over the buttons or clicks on them.  
	按需，当用户悬停在按钮上或点击它们时。

These are considered Tier 3 dependencies going by Facebook's tier definition above.  
根据 Facebook 上面的层定义，这些被认为是第 3 层依赖关系。

#### Optimistic updates 乐观的更新

Optimistic update is a performance technique where the client immediately reflect the updated state after a user interaction that hits the server and optimistically assume that the server request succeeds, which should be the case for most requests. This gives users instant feedback and improves the perceived performance. If the server request fails, we can revert the UI changes and display an error message.  
乐观更新是一种性能技术，其中客户端在用户交互命中服务器后立即反映更新的状态，并乐观地假设服务器请求成功，这应该是大多数请求的情况。这为用户提供了即时反馈，并提高了感知性能。如果服务器请求失败，我们可以恢复 UI 更改并显示错误消息。

For a news feed, optimistic updates can be applied for reaction interactions by immediately showing the user's reaction and an updated total count of the reactions.  
对于新闻馈送，可以通过立即显示用户的反应和反应的更新的总计数来将乐观更新应用于反应交互。

Optimistic updates is a powerful feature built into modern query libraries like [Relay](https://relay.dev/docs/guided-tour/updating-data/graphql-mutations/#optimistic-updates), [SWR](https://swr.vercel.app/docs/mutation#optimistic-updates) and [React Query](https://tanstack.com/query/latest/docs/framework/react/overview).  
乐观更新是现代查询库（如 [Relay](https://relay.dev/docs/guided-tour/updating-data/graphql-mutations/#optimistic-updates) ， [SWR](https://swr.vercel.app/docs/mutation#optimistic-updates) 和 [React Query）](https://tanstack.com/query/latest/docs/framework/react/overview) 中内置的强大功能。

Timestamp rendering is a topic worth discussing because of a few issues: multilingual timestamps and stale relative timestamps.  
时间戳呈现是一个值得讨论的话题，因为有几个问题：多语言时间戳和过时的相对时间戳。

**Multilingual timestamps**: Globally popular sites like Facebook and Twitter have to ensure their UI works well for different languages. There are a few ways to support multilingual timestamps:  
**多语言时间戳** ：全球流行的网站，如 Facebook 和 Twitter，必须确保其 UI 适用于不同的语言。有几种方法可以支持多语言时间戳：

1. **Server returns the raw timestamp**: Server returns the raw timestamp and the client renders in the user's language. This approach is flexible but requires the client to contain the grammar rules and translation strings for different languages, which can amount to significant JavaScript size depending on the number of supported languages,  
	**服务器返回原始时间戳** ：服务器返回原始时间戳，客户端以用户的语言呈现。这种方法很灵活，但需要客户端包含不同语言的语法规则和翻译字符串，根据支持的语言数量，这些字符串可能会达到相当大的 JavaScript 大小，
2. **Server returns the translated timestamp**: This requires processing on the server, but you don't have to ship timestamp formatting rules for various languages to the client. However, since translation is done on the server, clients cannot manipulate the timestamp on the client.  
	**服务器返回转换后的时间戳** ：这需要在服务器上进行处理，但您不必将各种语言的时间戳格式规则发送到客户端。但是，由于转换是在服务器上完成的，因此客户端无法操作客户端上的时间戳。
3. **`Intl` API**: Modern browsers can leverage `Intl.DateTimeFormat()` and `Intl.RelativeTimeFormat()` to transform a raw timestamp into translated datetime strings in the desired format.  
	**`Intl` API** ：现代浏览器可以利用 `Intl.DateTimeFormat（）` 和 `Intl.RelativeTimeFormat（）` 将原始时间戳转换为所需格式的转换日期时间字符串。

```js
const date = new Date(Date.UTC(2021, 11, 20, 3, 23, 16, 738));
console.log(
  new Intl.DateTimeFormat('zh-CN', {
    dateStyle: 'full',
    timeStyle: 'long',
  }).format(date),
); // 2021年12月20日星期一 GMT+8 11:23:16

console.log(
  new Intl.RelativeTimeFormat('zh-CN', {
    dateStyle: 'full',
    timeStyle: 'long',
  }).format(-1, 'day'),
); // 1天前
```

**Relative timestamps can turn stale**: If timestamps are displayed using a relative format (e.g. 3 minutes ago, 1 hour ago, 2 weeks ago, etc), recent timestamps can easily go stale especially for applications where users don't refresh the page. A timer can be used to constantly update the timestamps if they are recent (less than an hour old) such that any significant time that has passed will be reflected correctly.  
**相对时间戳可能会过时** ：如果时间戳使用相对格式（例如 3 分钟前、1 小时前、2 周前等）显示，则最近的时间戳很容易过时，特别是对于用户不刷新页面的应用程序。如果时间戳是最近的（小于一小时），则可以使用计时器来不断更新时间戳，以便正确反映已经过去的任何重要时间。

#### Icon rendering 图标渲染

Icons are needed within the action buttons of a post for liking, commenting, sharing, etc. There are a few ways to render icons:  
图标需要在一个帖子的操作按钮中用于喜欢，评论，分享等。有几种方法可以渲染图标：

| Approach 方法 | Pros 优点 | Cons 缺点 |
| --- | --- | --- |
| Separate image 分离图像 | Simple to implement.易于实施。 | Multiple download requests per image.   每个映像有多个下载请求。 |
| Spritesheet | One HTTP request to download all icon images.   一个 HTTP 请求下载所有图标图像。 | Complicated to set up.布置起来很复杂。 |
| Icon fonts 图标字体 | Scalable and crisp.可扩展且清晰。 | Need to download the entire font. Flash of unstyled content when font is loading.   需要下载整个字体。字体加载时未样式化内容的闪烁。 |
| SVG | Scalable and crisp. Cacheable.   可扩展且清晰。可缓存。 | Flickering when file is being downloaded. One download requests per image.   文件下载时闪烁。每个图像一个下载请求。 |
| Inlined SVG 内联 SVG | Scalable and crisp.可扩展且清晰。 | Cannot be cached.无法缓存。 |

Facebook and Twitter use inlined SVGs and that also seems to be the trend these days. This technique is not specific to news feeds, it's relevant to almost every web app.  
Facebook 和 Twitter 使用内联 SVG，这似乎也是目前的趋势。这种技术并不局限于新闻提要，它几乎适用于所有的 Web 应用程序。

*Source: ["Rebuilding our tech stack for the new Facebook.com" blog post](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)  
来源： [“Rebuilding our tech stack for the new Facebook.com“博客文章](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)*

#### Post truncation 后截断

Truncate posts which have super long message content and reveal the rest behind a "See more" button.  
截断具有超长消息内容的帖子，并在“查看更多”按钮后显示其余内容。

For posts with large amount of activity (e.g. many likes, reactions, shares), abbreviate counts appropriately instead of rendering the raw count so that it's easier to read and the magnitude is still sufficiently conveyed:  
对于具有大量活动的帖子（例如，许多喜欢，反应，分享），适当地重新计数，而不是呈现原始计数，以便更容易阅读并且仍然充分传达幅度：

- **Good**: John, Mary and 103K others  
	**好** ：约翰、玛丽和其他 103 K 人
- **Bad**: John, Mary and 103,312 others  
	**坏** ：约翰，玛丽和其他 103,312 人

This summary line can be either constructed on the server or the client. The pros and cons of doing on the server vs the client is similar to that of timestamp rendering. However, you should definitely not send down the entire list of users if it's huge as it's likely not needed or useful.  
此摘要行可以在服务器或客户端上构建。在服务器端和客户端上做的利弊类似于时间戳渲染。但是，如果用户列表太大，你绝对不应该发送整个列表，因为它可能不需要或没有用。

If time permits, we can discuss how the feed comments can be built. In general, the same rules apply to comments rendering and comment drafting:  
如果时间允许，我们可以讨论如何构建提要注释。一般来说，相同的规则适用于评论渲染和评论起草：

- Cursor-based pagination for fetching the list of comments.  
	基于指针的分页，用于获取注释列表。
- Drafting and editing comments can be done in a similar fashion as drafting/editing posts.  
	起草和编辑评论可以以与起草/编辑帖子类似的方式完成。
- Lazy load emoji/sticker pickers in the comment inputs.  
	在评论输入中惰性加载表情符号/贴纸选择器。
- Optimistic updates 乐观的更新
	- Immediately reflect new comments by appending the user's new comment to the existing list of comments.  
		通过将用户的新注释附加到现有注释列表中，立即反映新注释。
	- Immediately display new reactions and updated reaction counts.  
		立即显示新的反应和更新的反应计数。

#### Live comment updates 实时评论更新

Live updates for Facebook feed comments enhance user engagement and interaction by providing real-time visibility of new comments and updated reaction counts. This fosters a dynamic and responsive communication environment, encouraging users to actively participate in ongoing conversations without the need for manual refreshing. The immediacy of live updates contributes to increased user retention.  
Facebook Feed 评论的实时更新通过提供新评论和更新的反应计数的实时可见性来增强用户参与度和互动。这促进了一个动态和响应的通信环境，鼓励用户积极参与正在进行的对话，而无需手动刷新。实时更新的即时性有助于提高用户保留率。

The common ways to implement live updates on a client include:  
在客户端上实现实时更新的常见方法包括：

- **Short polling**: Short polling is a technique in which the client repeatedly sends requests to the server at fixed intervals to check for updates. The connection is closed after each request, and the server responds immediately with the current state or any available updates. While short polling is straightforward to implement, it may result in higher network traffic and server load compared to the more advanced techniques mentioned below.  
	**短轮询** ：短轮询是一种技术，在这种技术中，客户端以固定的间隔向服务器重复发送请求以检查更新。在每次请求之后，连接都会关闭，服务器会立即响应当前状态或任何可用的更新。虽然短轮询很容易实现，但与下面提到的更高级的技术相比，它可能会导致更高的网络流量和服务器负载。
- **Long polling**: Long polling extends on the idea of short polling by keeping the connection open until new data is available. While simpler to implement, it may introduce latency and increased server load compared to other approaches.  
	**长轮询** ：长轮询扩展了短轮询的思想，保持连接打开，直到新数据可用。虽然实现起来更简单，但与其他方法相比，它可能会引入延迟并增加服务器负载。
- **Server-Sent Events (SSE)**: SSE is a standard web technology that enables servers to push updates to web clients over a single HTTP connection. It's a simple and efficient mechanism for real-time updates, particularly well-suited for scenarios where updates are initiated by the server.  
	**服务器发送事件（SSE）** ：SSE 是一种标准的 Web 技术，使服务器能够通过单个 HTTP 连接将更新推送到 Web 客户端。这是一种简单而有效的实时更新机制，特别适合于由服务器发起更新的场景。
- **WebSockets**: WebSockets provide a full-duplex communication channel over a single, long-lived connection. This bidirectional communication allows both the server and the client to send messages to each other at any time. WebSockets are suitable for applications that require low latency and high interactivity.  
	**WebSockets** ：WebSockets 通过一个长期连接提供全双工通信通道。这种双向通信允许服务器和客户端随时互相发送消息。WebSockets 适用于需要低延迟和高交互性的应用程序。
- **HTTP/2 server push**: With HTTP/2, the server can push updates to the client without waiting for the client to request them. While HTTP/2 server push is not as widely used as other methods for live updates, it can be an efficient solution in certain scenarios.  
	**HTTP/2 服务器推送** ：使用 HTTP/2，服务器可以将更新推送到客户端，而无需等待客户端请求它们。虽然 HTTP/2 服务器推送不像其他实时更新方法那样广泛使用，但在某些情况下，它可能是一种有效的解决方案。

Facebook uses WebSockets for live updates on the website.  
Facebook 使用 WebSockets 在网站上进行实时更新。

While showing live updates is great, it is not efficient to be fetching updates for posts that have gone out of view in the feed. Clients can subscribe/unsubscribe to updates for posts based on whether the post is visible, which lightens the load on server infrastructure.  
虽然显示实时更新是伟大的，这是没有效率的获取更新的职位，已经走出了饲料的看法。客户端可以根据帖子是否可见来订阅/取消订阅帖子的更新，这减轻了服务器基础设施的负载。

Additionally, not all posts should be treated equally. Posts by users with many followers (e.g. celebrities and politicians) will be seen by many more people and hence more likely to receive an update. For such posts, new comments and reactions will be frequently added/updated and it will not be wise to fetch every new post or reaction since the update rate is too high for the user to read every new comment. Hence for such posts, the live updates can be debounced/throttled. Beyond a certain threshold, just fetching the updated comments and reactions count will be sufficient.  
此外，并非所有员额都应平等对待。拥有许多粉丝的用户（例如名人和政治家）的帖子将被更多的人看到，因此更有可能收到更新。对于这样的帖子，新的评论和反应将被频繁地添加/更新，并且获取每个新的帖子或反应将是不明智的，因为更新速率对于用户来说太高而无法阅读每个新的评论。因此，对于这样的帖子，实时更新可以被去抖动/节流。超过一定的阈值，只获取更新的评论和反应计数就足够了。

### Feed composer optimizations Feed Composer 优化

#### Rich text for hashtags and mentions 标签和提及的富文本

When drafting a post in the composer, it'd be a nice to have an WYSIWYG editing experience look like the result which contain hashtags and mentions. However, `<input>` s and `<textarea>` s only allows input and display of plain text. The [`contenteditable`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/contenteditable) attribute turns an element into an editable rich text editor.  
当在作曲家起草一个职位，这将是一个很好的有一个所见即所得的编辑经验看起来像结果，其中包含标签和提及。但是， `<input>` s 和 `<textarea>` s 只允许输入和显示纯文本。 [`contenteditable`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/contenteditable) 属性将元素转换为可编辑的富文本编辑器。

Try it for yourself here:  
在这里为自己尝试一下：

**editable and format-able** text thanks to `contenteditable`. You can even format the text (e.g. bold by using Ctrl/Cmd + B).

However, it is not a good idea to use `contenteditable="true"` as-is in production because it [comes with many issues](https://medium.engineering/why-contenteditable-is-terrible-122d8a40e480?gi=6b03d73f8e36). It'd be better to use battle-tested rich text editor libraries.  
但是，在生产环境中按原样使用 `contenteditable=“true”` 并不是一个好主意，因为它 [会带来许多问题](https://medium.engineering/why-contenteditable-is-terrible-122d8a40e480?gi=6b03d73f8e36) 。最好使用经过实战测试的富文本编辑器库。

Meta has built rich text editors like [Draft.js](https://draftjs.org/) (deprecated) and [Lexical](https://lexical.dev/) and use them for drafting + displaying post and comments. Other popular open sources alternatives include [TipTap](https://tiptap.dev/api/editor) and [Slate](https://www.slatejs.org/).  
Meta 已经构建了富文本编辑器，如 [Draft.js](https://draftjs.org/) （已弃用）和 [Lexical](https://lexical.dev/) ，并使用它们来起草+显示帖子和评论。其他流行的开源替代品包括 [TipTap](https://tiptap.dev/api/editor) 和 [Slate](https://www.slatejs.org/) 。

*Source: [Facebook open sources rich text editor framework Draft.js](https://engineering.fb.com/2016/02/26/web/facebook-open-sources-rich-text-editor-framework-draft-js/)  
Source： [Facebook 开源富文本编辑器框架 Draft.js](https://engineering.fb.com/2016/02/26/web/facebook-open-sources-rich-text-editor-framework-draft-js/)*

#### Lazy load dependencies 延迟加载依赖项

Like rendering news feed posts, users can draft posts in many different formats which require specialized rendering code per format. Lazy loading can be used to load the resources for the desired formats and optional features in an on-demand fashion.  
像渲染新闻提要帖子一样，用户可以以许多不同的格式起草帖子，这些格式需要专门的渲染代码。延迟加载可用于以按需方式加载所需格式和可选特性的资源。

Non-crucial features where the code can be lazy loaded on demand:  
非关键特性，其中代码可以按需延迟加载：

- Image uploader 图像上传器
- GIF picker GIF 拾取器
- Emoji picker 绘文字拣选器
- Sticker picker 标签拾取器
- Background images 背景图像

### Accessibility 无障碍

Here are some accessibility considerations for a news feed.  
以下是新闻提要的一些可访问性注意事项。

#### Feed list 馈送列表

- Add `role="feed"` to the feed HTML element.  
	在 feed HTML 元素中添加 `role=“feed”` 。

#### Feed posts 饲料岗位

- Add `role="article"` to each feed post HTML element.  
	添加 `role=“article”` 到每个 feed post HTML 元素。
- `aria-labelledby="<id>"` where the HTML tag containing the feed author name has that `id` attribute.  
	`aria-labelledby="<id>”` ，其中包含提要作者姓名的 HTML 标记具有该 `id` 属性。
- Contents within the feed post should be focusable via keyboards (add `tabindex="0"`) and the appropriate `aria-role`.  
	feed post 中的内容应该可以通过键盘（add `tabindex=“0”` ）和适当的 `aria-role 来` 聚焦。
- On Facebook website, users can have more reaction options by hovering over the "Like" button. To allow the same functionality for keyboard users, Facebook displays a button that only appears on focus and the reactions menu can be opened via that button.  
	在 Facebook 网站上，用户可以通过将鼠标悬停在“喜欢”按钮上来拥有更多的反应选项。为了允许键盘用户使用相同的功能，Facebook 显示了一个只出现在焦点上的按钮，可以通过该按钮打开反应菜单。
- Icon-only buttons should have `aria-label` s if there are no accompanying labels (e.g. Twitter).  
	如果没有附带标签（例如 Twitter），则仅图标按钮应具有 `aria-label` 。

---

## References 引用

- [Rebuilding our tech stack for the new Facebook.com  
	为新的 Facebook.com 重建我们的技术堆栈](https://engineering.fb.com/2020/05/08/web/facebook-redesign/)
- [Making Facebook.com accessible to as many people as possible  
	让尽可能多的人访问 Facebook.com](https://engineering.fb.com/2020/07/30/web/facebook-com-accessibility/)
- [Dissecting Twitter's Redux Store  
	剖析 Twitter 的 Redux 商店](https://medium.com/statuscode/dissecting-twitters-redux-store-d7280b62c6b1)
- [Twitter Lite and High Performance React Progressive Web Apps at Scale  
	Twitter Lite 和大规模高性能 React 渐进式 Web 应用](https://medium.com/@paularmstrong/twitter-lite-and-high-performance-react-progressive-web-apps-at-scale-d28a00e780a3)
- [Making Instagram.com faster: Part 1  
	使 www.example.com 更快：第 1 部分](https://instagram-engineering.com/making-instagram-com-faster-part-1-62cc0c327538)
- [Making Instagram.com faster: Part 2  
	使 Instagram.com 更快：第 2 部分](https://instagram-engineering.com/making-instagram-com-faster-part-2-f350c8fba0d4)
- [Making Instagram.com faster: Part 3 — cache first  
	使 Instagram.com 更快：第 3 部分-缓存优先](https://instagram-engineering.com/making-instagram-com-faster-part-3-cache-first-6f3f130b9669)
- [Evolving API Pagination at Slack  
	Slack 上的 API 分页](https://slack.engineering/evolving-api-pagination-at-slack)

## Changelog

- 2024/08/21
	- Added more properties to `image` field for payload response  
		为有效负载响应的 `图像` 字段添加了更多属性
	- Mentioned `Intl.RelativeTimeFormat` API  
		提到的 `Intl.RelativeTimeFormat` API
- 2023/12/04
	- Added section on live comments  
		添加了关于实时评论的部分

[![[_posts/architect/System Design/greatfrontend.com/questions/media/9677a3368ab294f7003501da61fd3d72_MD5.webp]]](https://www.greatfrontend.com/a/click?a=swag-overflow-in-content&u=https%3A%2F%2Fwww.swagoverflow.store%2Fproducts%2Fundefined-is-not-a-function-tee-unisex) Sponsor: [SwagOverflow](https://www.greatfrontend.com/a/click?a=swag-overflow-in-content&u=https%3A%2F%2Fwww.swagoverflow.store%2Fproducts%2Fundefined-is-not-a-function-tee-unisex)  
赞助商： [SwagOverflow](https://www.greatfrontend.com/a/click?a=swag-overflow-in-content&u=https%3A%2F%2Fwww.swagoverflow.store%2Fproducts%2Fundefined-is-not-a-function-tee-unisex)

SwagOverflow — Swag tailored for front end devs  
SwagOverflow -为前端开发人员量身定制的 Swag

Looking for a gift for a fellow dev, or a simple comfy t-shirt that expresses your passion for front end?

Check out [SwagOverflow](https://www.greatfrontend.com/a/click?a=swag-overflow-in-content&u=https%3A%2F%2Fwww.swagoverflow.store%2F), your ultimate destination for dev-centric apparel and accessories. Whether you’re coding at your favorite café or speaking at a local meetup, our products will help you stand out in any crowd. Here’s why you’ll love SwagOverflow:

- **Unique, dev-Inspired designs**: From HTML tags to CSS puns, show off your coding chops
- **Premium quality materials**: Made to handle all-night coding sessions or weekend hackathons
- **Variety for every stack**: React lover? TypeScript fanatic? We’ve got you covered
- **Perfect gifts for techies**: Surprise a friend (or yourself) with front end flair
- **Community-focused**: A portion of every purchase goes right back into supporting dev communities
[Visit SwagOverflow today ->](https://www.greatfrontend.com/a/click?a=swag-overflow-in-content&u=https%3A%2F%2Fwww.swagoverflow.store%2Fproducts%2Fundefined-is-not-a-function-tee-unisex)
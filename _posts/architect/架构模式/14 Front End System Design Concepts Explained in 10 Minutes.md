[14 Front End System Design Concepts Explained in 10 Minutes](https://www.youtube.com/watch?v=YO7R0rYWDl8)


https://youtu.be/5vyKhm2NTfw?si=MTFKSZcUOCaBdH2v



💡 Timestamps:
(0:00) Intro
(0:16) Static Site Generation (SSG)
(0:27) Incremental Static Regeneration (ISR)
(0:34) Server-Side Rendering (SSR)
(0:39) Client-Side Rendering (CSR)
(0:46) Partial Pre-Rendering
(0:51) 5 Web Rendering Strategies
(1:01) Micro Frontends & Webpack Module Federation
(1:51) Web Performance Metrics (Tools)
(2:34) Memoization Hooks
(3:12) Lazy Loading
(3:46) Tree Shaking, Mobile Friendly, Accessibility and more
(4:25) State Management
(6:31) API Caching w/ Expiration (React Query)
(7:22) GraphQL for Reducing Over-Fetching
(8:40) Rate Limiting & Debouncing
(9:47) Cursor vs. Offset Pagination
(12:18) Outro

---

视频讲述了**14个前端系统设计核心概念**，涵盖*渲染策略、架构、性能优化、API优化*等方面，助力构建更快、可扩展且高效的应用，具体如下：


- **前端内容渲染的五种核心策略**：[00:15]包括`静态站点生成`（SSG，构建时预构建页面，客户端加载速度最快）、`增量静态再生`（ISR，按需更新静态页面无需全量重建）、`服务端渲染`（SSR，每次请求生成页面，适合动态内容）、`客户端渲染`（CSR，先加载空白HTML，再在客户端获取数据并编译JS）、`部分预渲染`（PPR，混合预渲染与动态内容在页面中），并提及有专门视频深入讲解这些策略。
- **微前端架构应对应用扩展与部署风险**：[01:01]当前端应用增长，代码量变大导致部署风险升高时，微前端架构将应用拆分为独立可部署的模块，每个微应用可采用不同渲染策略（如产品页用SSG、结账用SSR、聊天通信用CSR），关键工具是Webpack 5的模块联邦，能动态加载不同微应用，且有专门视频讲解该架构的工作原理与本地搭建。
- **前端渲染性能的测量工具**：[01:51]可使用Chrome DevTools性能面板跟踪首次内容绘制和交互时间，了解用户看到并能交互应用的耗时；用React DevTools分析器查看频繁重渲染的组件及应用慢节点；还能借助DataDog、Lighthouse或WebPageTest，获取不同设备和网络速度下渲染性能的详细分析。
- **解决不必要组件重渲染的缓存钩子**：[02:34]React中父组件重渲染会导致子组件默认重渲染，可通过缓存钩子解决，如React.memo缓存组件，仅在props变化时重渲染；useCallback缓存函数，避免不必要的函数重建；useMemo缓存函数的耗时计算，防止每次渲染都重新计算，同时可借助Chrome DevTools和React分析器可视化查看引发不必要重渲染的组件。
- **组件懒加载减少初始加载压力**：[03:12]若应用一次性加载过多组件，会增加用户等待时间，懒加载可在用户需要时再加载组件（如仪表盘的图表和分析功能，用户点击时才加载），能减小初始包体积，提升应用加载速度。
- **额外的前端性能优化手段**：[03:46]包括利用树摇移除未使用代码、拆分代码包以加快页面加载，减少JS包体积；使用骨架屏、加载动画或通知提升应用响应感；针对移动设备和无障碍进行优化，确保应用在不同设备和场景下都能良好运行。
- **状态管理减少客户端与服务端请求**：[04:25]传统应用中客户端对数据的增删改查会产生大量重复GET请求，状态管理可将服务端返回的数据缓存到客户端（如本地存储或状态管理工具中），数据更新时直接修改缓存，无需频繁向服务端发送请求，同时可通过请求拦截器附加认证信息，响应拦截器将数据存入缓存，但需考虑缓存数据的同步问题（如设置缓存过期时间刷新数据）。
- **React Query实现API缓存与过期管理**：[06:31]使用React Query的useQuery进行API调用，可设置数据过期时间（staleTime），客户端发起查询时先检查缓存数据是否有效，有效则直接使用缓存数据，无效则重新请求，请求成功后将新数据存入缓存，减少不必要的API调用，同时保证数据时效性。
- **GraphQL减少API数据过度获取**：[07:22]传统API可能返回客户端不需要的数据，GraphQL允许客户端指定所需数据的属性（如仅获取产品的名称和价格），减少返回数据量，且Apollo Client等工具支持内存缓存，相同查询若缓存中有数据则直接从缓存获取，无需重复请求服务端，另有专门GraphQL教程 playlist 涵盖认证、发布订阅、分页等内容。
- **防抖实现客户端API请求限流**：[08:40]如搜索框中用户每输入一个字符就发送请求会产生大量API调用，使用防抖（如Lodash的debounce）设置延迟时间（如500毫秒），等待用户停止输入后再执行API调用，大幅减少客户端向服务端的请求次数，限流也可在服务端实现，客户端防抖可提前预防请求过量。
- **偏移分页与游标分页的应用**：[09:47]偏移分页通过页面大小和当前页码向服务端请求数据，服务端用SQL的offset和limit筛选数据，优点是支持查看总页数和跳转到指定页面，缺点是数据增删可能导致结果重复或遗漏；游标分页基于唯一且有序的标识（如ID、时间戳）请求数据（如获取游标ID之后的10条数据），服务端通过条件查询筛选数据，优点是数据增删不影响分页稳定性、减轻数据库负担，缺点是不支持跳转到指定页面，且需确保游标标识唯一有序，两种分页方式均可减少服务端向客户端传输的数据量。
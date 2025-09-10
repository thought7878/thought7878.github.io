[Frontend System Design Guide](https://github.com/devkodeio/frontend-system-design?tab=readme-ov-file)


# What is this Frontend System Design guide about?  
这个前端系统设计指南是关于什么的？

Frontend System Design guide attempts to cover the various factors to be considered while architecting a complex large-scale frontend application from scratch. Also the guide acts like a checklist that can be used as a reference for frontend system design. The guide aims to contain the breadth of the frontend systems while giving the directions to explore in depth for both individual contributors and system architects.  
前端系统设计指南试图涵盖从头开始构建复杂的大型前端应用程序时要考虑的各种因素。此外，该指南就像一个检查表，可以用作前端系统设计的参考。该指南旨在包含前端系统的广度，同时为个人贡献者和系统架构师提供深入探索的方向。

```
Guide is generic and not biased towards any library or framework
本指南具有通用性，不偏向任何库或框架。
```

- [Engineering Design   工程设计](https://github.com/devkodeio/frontend-system-design?tab=readme-ov-file#engineering-design)
- [High Level Design   高级设计](https://github.com/devkodeio/frontend-system-design?tab=readme-ov-file#high-level-design)
- [Low level Design   低级设计](https://github.com/devkodeio/frontend-system-design?tab=readme-ov-file#low-level-design)

---

# Engineering Design  工程设计

- Team size  团队规模
- User base  用户群
- Knowledge base  知识库
- Compliance/Governance  合规/治理
- User/Client expectations  用户/客户期望
- Open source vs proprietary  开源 VS 专有
- Documentation / PRD  文件/PRD
- Future Roadmaps  未来路线图

---

# High Level Design  高级设计

- Platform identification  平台标识
- SPA vs MPA
- SSR, SSG, CSR
- Tech stack  
- Search Engine Optimization 
- CI/CD
- User Experience 
- [A/B testing](https://philipwalton.com/articles/performant-a-b-testing-with-cloudflare-workers/)
- MVP planning 
- Server Side Architecture 服务器端架构
- Security  
- State Management  
- Internationalization  
- E2E testing
- Tools Integration  工具集成
- Authentication & Authorization  认证与授权
- Quality Assurance & Control  质量保证与控制
- User role management  用户角色管理


noSelect

## 产品需求文档（PRD）/设计文档
Product Requirement Document (PRD) / Design Document  


- Identify Scope/Requirement  
    确定范围/要求
- Review your understanding with stakeholders  
    与利益相关者一起审查您的理解

  

## 关于设计/线框的讨论
Discuss about Design/Wireframe  

- Think like an architect.  
    像建筑师一样思考。
- We should not consider team bandwidth, capacity and time.  
    我们不应该考虑团队的带宽、能力和时间。
- Discuss about Edge cases.  
    讨论边缘案例。
- Robustness: Handle SPOF (Single Point of Failure) ex: Monitoring, Logging  
    稳健性：处理 SPOF（单点故障），例如：监控、记录

  

## Identify Business  确定业务

- Is it B2B (business-to-business)?  
    B2B（企业对企业）？
- Is it B2C (business-to-consumer)?  
    B2C（企业对消费者）？
- Is it Internal Product?  
    是内部产品吗？
- Is it Customer facing product?  
    是面向客户的产品吗？

  

## Identify Platform  识别平台

- Desktop  桌面
- Mobile  移动的
- Tablet  平板

  

## 识别用户（了解您的受众）
Identify Users (Know your audience)  

- Conduct surveys  进行调查
- Discuss about Location and Devices  
    讨论位置和设备
- Internet speed  网速
- End Users knowledge base (ex: Technical user)  
    最终用户知识库（例如：技术用户）
- Pilot Product (sometimes to understand audience)  
    试用产品（有时是为了了解受众）

  

## 确定设计方法
Identify Design Approach  

- Responsive vs Adaptive design  
    响应式设计 vs 自适应设计
- Desktop first vs Mobile first  
    桌面优先 vs 移动的优先

  

## 确定 API
Identify APIs  

- Rest APIs / Graph APIs / RPC  
    
- JSON / Protocol buffers

  

##  角色管理
Role based management 

- Large system needs roles based access and permissions  
    大型系统需要基于角色的访问和权限
- Authentication and Authorization  
    认证和授权
- Read/Write/View Permissions  
    读/写/查看权限
- Discuss about Routes/Component access  
    讨论路由/组件访问

  


## 确定合适的平台（根据用例比较框架）
Identify Right Platform (compare frameworks based on the use case)  

- Single Page Applications (Unsuitable for Blogs/News based products)  
    单页应用程序（不适合基于博客/新闻的产品）
    - No reloading of a page at navigation  
        导航时不重新加载页面
    - No SEO  没有 SEO
- Multi Page Applications   多页应用程序
    - Reloading of a page at every page navigation.  
        在每一个页面导航的页面。
- Progressive Web Applications  
    渐进式 Web 应用程序
    - Provides offline support and native like functionality.  
        提供离线支持和类似本机的功能。
- Server Side Rendering   服务器端渲染
    - Better SEO  更好的 SEO
- Important points to discuss:  
    要讨论的要点：
    - Are users on mobile?  
        用户是否使用移动的？
    - Is SEO needed?  SEO 需要吗？
    - Is SPA enough?  SPA 够吗？
    - Is PWA enough? (Service worker, Web Worker)  
    - Compare SSR / SSG / CSR  
        比较 SSR / SSG / CSR
    - Any Pricing model? (optional) - Subscriptions based, Paid APIs  
        任何定价模式？（可选）-基于订阅的付费 API
    - Will my app be Frontend heavy? (or backend heavy)  
        我的应用程序会是重前端吗？(or 后端重）
    - Do I have resources for this skill?  
        我是否有资源来掌握这项技能？
    - Is your application Canvas (or SVG) heavy? (Figma, Draw.io)  
        你的应用程序 Canvas（或 SVG）很重吗？（Figma，Draw.io）
    - Is your application webRTC heavy? (Video streaming)  
        你的应用程序 webRTC 重吗？（视频流）

  

## Identify User Flow  识别用户流

- Discuss vision of a product.  
    讨论产品的愿景。
- Do we need to build from scratch or we can leverage some existing functionalities  
    我们需要从头开始构建还是可以利用一些现有功能
- Discuss about authentication and authorization (Google auth / OAuth)  
    讨论身份验证和授权（Google auth / OAuth）
- Interact with the Product manager to understand the scope before designing the application.  
    在设计应用程序之前，与产品经理互动以了解范围。
- Discuss happy scenarios.  
    讨论快乐的场景。
- Discuss edge cases.  讨论边缘案例。
- Discuss failing scenarios  
    讨论失败场景

  
## 确定MVP（最小可行产品）
Identify MVP (Minimum Viable Product)  

- Problem -> Solution -> Build MVP -> MVP to Customers  
    问题->解决方案->构建 MVP ->客户 MVP
- Discuss MVP phase with product manager  
    与产品经理讨论 MVP 阶段
- Discuss roadmaps and divide product in milestones  
    讨论路线图并将产品划分为里程碑
- After MVP release there can be a slight change in design/approach to make the product better  
    MVP 发布后，设计/方法可能会略有变化，以使产品更好

  

## Volume of Operations  业务量

- Discuss about the end users of the product  
    讨论产品的最终用户
- Identify QPS (Queries per second)  
    识别QPS（每秒查询数）
- Discuss about Load testing/Stress testing  
    负载测试/压力测试
- Inject analytics in application (ex: Google analytics, Sentry, NewRelic)  
    在应用程序中注入分析（例如：Google 分析，Sentry，NewRelic）
- Analytics data helps us to scale the system  
    分析数据帮助我们扩展系统

  
## SEO（搜索引擎优化）
SEO (Search Engine Optimization)  

- Crawling  爬行
- Use of Heading tags  
    标题标签的使用
- [Semantic tags   语义标签](https://htmlreference.io/)
- Site Ranking  网站排名
- Sitemap  网站地图
- Meta Keywords  Meta 关键词
- Organic approach vs Inorganic approach  
    有机方法 vs 无机方法
- Use of alt tags  
    使用 alt 标签
- 301 Redirects (bad for SEO)  
    301 重定向（对 SEO 不利）
- Robots.txt
- [Open graph protocol](https://ogp.me/) for social graph  
    用于社交图[的开放图协议](https://ogp.me/)

  
## 基于组件的设计
Component Based Design  

- Component wise deployment cycle (CI/CD)  
    组件部署周期（CI/CD）
- Monolith vs Microservice architecture  
    Monolith vs 微服务架构
- [Micro Frontend (independent dev & deployment for scalability)  微前端（独立开发、部署）](https://blog.bitsrc.io/how-we-build-micro-front-ends-d3eeeac0acfc)
- Static components vs Dynamic components  
    静态组件 vs 动态组件
- IFrame/[Shell](https://github.com/GoogleChromeLabs/sw-precache/tree/master/app-shell-demo) approach  
    IFrame/[Shell](https://github.com/GoogleChromeLabs/sw-precache/tree/master/app-shell-demo) 方法

  

## 状态管理
State Management  

- How to maintain state through the application?  
    如何通过应用程序维护状态？
- How to manage users' data?  
    如何管理用户数据？
- State management Libraries (Redux, Flux, NgRX)  
    状态管理库（Redux，Flux，NgRX）

  

## 处理 API
Handling APIs  

- Polling (Short and Long)  
    轮询（短和长）
- Web Sockets (Real-time) (ex: chat, shared editors)  
    Web 套接字（实时）（例如：聊天，共享编辑器）
- Batch requests  批处理请求
- GraphQL
- Caching GET APIs (Middleware concepts to cache response)  
    缓存 GET API（缓存响应的中间件概念）
- [Server-Sent Events (SSE)  
    服务器发送事件（SSE）](https://germano.dev/sse-websockets/)

  

## 优化图像
Optimizing Images  

- Add alt attributes (Images should be descriptive for SEO)  
    添加 alt 属性（图像应该是 SEO 的描述性）
- Load images based on screen size (img srcset)  
    根据屏幕大小加载图像（img srcset）
- Image compression (ex: JPEG 2000)  
    图像压缩（例如：JPEG 2000）
- Image sitemaps  网站地图
- Use SVGs for generic dimensions (in case of stretching of images)  
    通用尺寸使用 SVG（在拉伸图像的情况下）
- Discuss about image Sprites for icons  
    讨论图像精灵图标
- Discuss about progressive images (ex: Medium.com) - e.g. [blurhash](https://github.com/woltapp/blurhash)  
    讨论渐进式图像（例如：Medium.com）-例如 [blurhash](https://github.com/woltapp/blurhash)

  

## Instrumentation  仪表

- Measurement and tracking are key for a stable system  
    测量和跟踪是稳定系统的关键
- Monitoring  监测
- Error logging (for tracing)  
    错误日志记录（用于跟踪）
- Debugging  调试
- Logs/Track all events happened in the application  
    记录/跟踪应用程序中发生的所有事件
- Implement Analytics (GA)  
    实施分析（GA）
- Sentry (to capture errors)  
    哨兵（捕获错误）
- Newrelic (to detect failures)  
    Newrelic（检测故障）

  

## 版本控制
Versioning of artifacts  

- Artifacts tracking (ex: Confluence)  
    伪影跟踪（例如：Confluence）
- Rollback & backup mechanisms  
    回滚和备份机制

  

## Performance Optimization Techniques  
性能优化技术

- Webpack to optimized/compressed pages (Code splitting, [Brotli Compression](https://www.fastfwd.com/improve-http-compression-with-brotli/), [Gzip Compression](https://www.keycdn.com/support/enable-gzip-compression))  
    Webpack 优化/压缩页面（代码分割，[Brotli 压缩](https://www.fastfwd.com/improve-http-compression-with-brotli/) ，[Gzip 压缩](https://www.keycdn.com/support/enable-gzip-compression) ）
- [Web Vitals (FP, LCP, CLS, etc)  ](https://10up.github.io/Engineering-Best-Practices/performance/#core-web-vitals)
- Lighthouse / PageSpeed Insights  
    Lighthouse / PageSpeed 洞察
- [Fast Loading (Initial load should be fast)](https://web.dev/fast/)
- Smooth Operations (Loading indicators / Light/Smooth/Meaningful animations (to avoid jerks in transitions) / Splash screens) - (dialog with light animations)  
    平滑操作（加载指示器/灯光/平滑/有意义的动画（以避免过渡中的抖动）/启动画面）-（带有灯光动画的对话框）
- Animation directions should be the same (dialog coming from bottom should close in bottom) - (smooth animation should be added in sidebars for better UX)  
    动画方向应该是相同的（对话框来自底部应关闭底部）-（平滑动画应添加在侧边栏更好的用户体验）
- Animation between data fetching(APIs request) - Skeletal Loaders, Blurhash etc  
    数据获取之间的动画（API 请求）-骨架加载器、Blurhash 等
- [Discuss about Caching - ex: API, resource cache (Browser cache / Memory / CDN / Disk Cache)](https://roadmap.sh/guides/http-caching)
- Pagination vs Infinite Scroll  
    分页与无限滚动
- Meaningful animation  有意义的动画
- [Micro interactions   微观互动](https://userguiding.com/blog/microinteraction/)

  

## Internationalization (i18n) / Localization (i10n)  
国际化（i18 n）/本地化（i10 n）

- Localization  定位
- Numeric, date and time formats  
    数字、日期和时间格式
- Singular & Plurals  单数与复数
- Use of currency  使用货币
- Keyboard usage  键盘使用
- Symbols, icons and colors  
    符号、图标和颜色
- Text and graphics vary with different languages and religions, may be subject to misinterpretation or viewed as insensitive  
    文字和图形因语言和宗教而异，可能会被误解或被视为不敏感
- Varying legal requirements  
    不同的法律的要求

  

## Accessibility  
无障碍

- Alt attributes  Alt 属性
- Aria-labels
- Multi-device support, [slow network speed](https://addyosmani.com/blog/adaptive-loading/)  
    多设备支持， [网速慢](https://addyosmani.com/blog/adaptive-loading/)
- Color contrast, semantics tags  
    颜色对比，语义标签


## Security  

- MITM
- [XSS](https://auth0.com/blog/cross-site-scripting-xss/)
- CSRF
- Clickjacking  点击劫持
- [Content Security Policy (CSP) 内容安全策略（CSP）](https://ponyfoo.com/articles/content-security-policy-in-express-apps#to-get-started-let-s-use-report-only)
- CORS
- [Security headers   安全性报头](https://securityheaders.com/)
- Helpful Tools   有用的工具
    - [Testing your SSL web server](https://www.ssllabs.com/ssltest/analyze.html)
    - [HTTP/2 Test](https://tools.keycdn.com/http2-test)
    - [HTTP Header Checker](https://tools.keycdn.com/curl)
    - [Website Speed Test](https://tools.keycdn.com/speed)
    - [Performance Test](https://tools.keycdn.com/performance)
    - [What Does my site cost?我的网站费用是多少？](https://whatdoesmysitecost.com/)
    - [HTML5 Security Cheat Sheet  HTML5 安全备忘录](https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html)
    - [Production Best Practices : Security  生产最佳实践：安全性](https://expressjs.com/en/advanced/best-practice-security.html)
    - [Web Application Vulnerabiliies Index  Web 应用程序漏洞索引](https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/)

  
## 质量保证和控制
Quality assurance and control  

- Stable products are successful  
    稳定的产品是成功的
- Specify standards - Code level / Artifacts level / Asset level  
    指定标准-代码级别/工件级别/资产级别
- Git Hooks (pre commit hooks, husky)  
    Git Hooks（提交前钩子，husky）
- Linters / Static Analyzers  
    短绒/静态分析仪
- Unit testing  单元测试
- Workflow testing (User level flows) (Tools - Cypress)  
    工作流程测试（用户级流程）（工具- Cypress）
- Integration testing  集成测试
- Automation suite  自动化套件
- [Cross browsers testing   跨浏览器测试](https://github.com/philipwalton/blog/blob/main/articles/learning-how-to-set-up-automated-cross-browser-javascript-unit-testing.md)
- Cross platform testing  跨平台测试

  

## Governance  治理

- Controlling the workflows and protecting the assets  
    控制工作流程并保护资产
- UX Design -> Developers -> Product Managers -> UX Designing -> QA  
    UX 设计->开发人员->产品经理-> UX 设计-> QA
- Code level governance - like PRs approval (sets standard in your team) - e.g. [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)  
    代码级治理-如 PR 批准（在您的团队中设置标准）-例如 [Gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- Artifacts/Assets level governance (before go live)  
    工件/资产级治理（上线前）
- like Product Manager approval, Stakeholders approvals  
    如产品经理批准、利益相关者批准

  
## 基于实验的释放周期
Experiment based release cycle  

- Experiment flag, which can help in the release cycle  
    实验标志，这可以帮助在发布周期

  
## 非功能性需求（NFR）
NFR (Non Functional Requirement)  

- Discuss about CI/CD (Docker, Pipeline)  


---

# Low Level Design  低级设计

- Code/Folder architecture  代码/文件夹架构
- Desktop/Mobile first approach  桌面/移动的优先方法
- System breakdown  系统故障
- Component Design  组件设计
- Form development  表单开发
- Storage management  存储管理
- API Design  API 设计
- Instrumentation  仪表
- Design system  设计系统
- Routing management  路由管理
- [CSS optimizations](https://developer.mozilla.org/en-US/docs/Learn/Performance/CSS)
- [Lazy loading of modules  模块的延迟加载](https://web.dev/fast/#lazy-load-images-and-video)
- [Accessibility   无障碍](https://www.w3.org/WAI/tips/developing/)
- [Image optimizations   图像优化](https://web.dev/fast/#optimize-your-images)
- Pagination, Debouncing, Throttling  分页、去弹跳、调节
- [Performance](https://developer.mozilla.org/en-US/docs/Web/Performance): FCP, LCP, TTI, CLS  
- Versioning  版本控制
- Unit testing  单元测试

---

# Architecture Considerations  
体系结构注意事项

- [Client-side Architecture Basics  客户端架构基础](https://khalilstemmler.com/articles/client-side-architecture/introduction/)
- [idiomatic.js](https://github.com/rwaldron/idiomatic.js)

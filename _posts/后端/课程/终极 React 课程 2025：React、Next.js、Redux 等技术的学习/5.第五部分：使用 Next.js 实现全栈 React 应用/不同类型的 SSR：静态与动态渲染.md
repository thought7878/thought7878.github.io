
这份字幕文件详细讲解了 **Next.js 中的两种服务端渲染策略（静态渲染与动态渲染）**，并深入解析了与之相关的核心部署与网络概念。

以下是内容的结构化总结：

## 1. Next.js 渲染基础
- Next.js 会**按路由 (Route)** 逐一进行渲染工作。
- 整个应用不需要统一采用同一种渲染方式，**每个路由都可以独立选择**是*静态渲染*还是*动态渲染*。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/82ab1c8e54e106998ba3bd416d93f4c6_MD5.webp]]

## 2. 静态渲染 (Static Rendering)
- **触发时机**：在**构建时 (Build time)** 生成 HTML（由开发者运行 build 命令触发）。
- **适用场景**：*数据不经常变化*，且**不包含用户个性化数据**（例如：普通的产品详情页、博客文章）。
- **部署优势**：生成的静态资源可以轻松地托管在 **CDN** 上，*访问速度极快且节省服务器资源*。

## 3. 动态渲染 (Dynamic Rendering)
- **触发时机**：在**请求时 (Request time)** 为每一次访问*实时生成全新的 HTML（由用户访问触发）*。
- **适用场景**：*数据频繁变化、包含用户个性化数据*（例如：购物车），或*渲染依赖于请求本身的信息*。
- **部署方式**：在 Vercel 等平台上，*每个动态路由会被自动部署*为一个**无服务器函数 (Serverless Function)**。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/685e9b6e192cc27e2ffcaaab26539095_MD5.webp]]

## 4. Next.js 自动切换为“动态渲染”的 4 个条件
Next.js 默认倾向于静态渲染以保证最高性能。但如果路由中出现以下情况，框架会**自动**将其切换为动态渲染：
1.  路由包含**动态路由段 (Dynamic segments)**，且页面使用了 `params` 属性来渲染数据。
2.  页面组件读取了 URL 的**搜索参数 (Search params / Query params)**。
3.  组件树中的任何 Server Component 读取了传入的 **Headers 或 Cookies**。
4.  组件树中包含了**未缓存的数据请求 (Uncached data request)**（开发者可通过配置 fetch 缓存策略来强制触发动态渲染）。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/8e4ecfea80e8a8a0342a790e342564e8_MD5.webp]]

## 5. 核心术语解析 (必知概念)
- `CDN (内容分发网络)`：*全球分布*的服务器网络，用于**缓存网站的静态资源**（HTML/CSS/JS/图片）。它能从物理距离**离用户最近的节点交付内容，大幅减少数据传输延迟**。
- `Serverless Function (无服务器函数)`：一种**无需开发者自行管理服务器即可运行后端代码**的模式。**服务器仅在函数被调用时短暂启动**。在 Vercel 部署时，Next.js 的**动态路由会自动转化为独立的 Serverless Function**，并能根据流量**自动弹性扩缩容**。
- `Edge Computing (边缘计算)`：可以理解为 **“用于运行代码的 CDN”**。它**将 Serverless Function 部署在全球分布的边缘节点**上，**让后端代码在离用户物理距离最近的地方执行，从而获得极致的响应速度**。
- `ISR (增量静态再生, Incremental Static Regeneration)`：Next.js 的强大特性。允许在网站部署后，**按开发者设定的时间间隔，在后台自动重新获取数据并更新静态页面**。用户访问时无感知，**完美结合了静态页面的极速与数据的时效性**。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/467bad2561a9bcc38adad26e90f97828_MD5.webp]]

---
**💡 总结提示**：
在实际开发中，你通常不需要手动去“选择”渲染方式，Next.js 会根据你的代码逻辑（是否读取 params、cookies、headers 或使用了未缓存的 fetch）**自动推断**并应用最合适的渲染策略。理解这些底层机制有助于你更好地优化应用性能和排查问题。

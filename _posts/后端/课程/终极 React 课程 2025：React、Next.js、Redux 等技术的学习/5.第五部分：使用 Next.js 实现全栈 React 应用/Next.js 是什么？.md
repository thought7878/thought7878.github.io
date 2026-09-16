这份字幕文件正式引入了 **Next.js**，详细讲解了*它的定义、核心特性*，并重点*对比了 Next.js 中的两大路由系统*（App Router 与 Pages Router）。

以下是内容的结构化总结：

## 1. 什么是 Next.js？
[00:00]
*   **定义**：Vercel 官方称其为“The React framework for the web”。本质上，它是一个*构建在 React 之上的`元框架（Meta-framework`）*。
*   **约定优于配置（Opinionated）**：Next.js 提供了*一套关于路由、数据获取等常见需求*的*最佳实践和严格约定*。开发者*必须遵循这些规则，但这能大幅减少样板代码，提高团队协作效率*。
*   **全栈能力**：它实现了 React 团队的*全栈架构*愿景，让开发者能够利用 React Server Components (RSC) 和 Server Actions 等前沿特性，*构建复杂的全栈 Web 应用*。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/ce21b5aed9267ebb718844c746b60578_MD5.webp]]

## 2. Next.js 的四大核心要素
[04:10]
1.  **服务端渲染 (SSR)**：支持*动态渲染和静态渲染*，并且可以精确到**按路由（Route）** 进行选择和配置。
2.  **基于文件系统的路由 (File-system based routing)**：*无需手动编写路由配置代码*，只需在特定目录下创建文件夹和文件*即可自动生成路由*。同时支持*页面、布局、错误处理、加载状态*等特殊文件约定。
3.  **服务端数据获取与变更**：借助 RSC 和 Server Actions，开发者可以直接在**服务端组件**中*获取和修改数据*，彻底改变了以往纯客户端获取数据的模式。
4.  **极致的优化技术**：内置了*自动路由预加载、图片和字体优化、SEO 工具*等，能显著提升网站性能和搜索引擎排名。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/223ae179daecc2a0b379e5a9de70c0a8_MD5.webp]]

## 3. 两大路由系统对比：App Router vs Pages Router
[07:00]
Next.js 目前存在两套路由系统，它们几乎是*两种完全不同的开发范式：*

### App Router（现代/推荐）
*   地位：在 v13.4 稳定，是**新项目的首选和官方推荐**。
*   **核心优势**：
    *   **拥抱 React 前沿特性**：全面支持 RSC、Server Actions、Suspense、流式传输（Streaming）等。
    *   **数据获取极简**：无需学习 Next.js 专属 API，直接在任意 Server Component 中使用原生的 `fetch` 函数即可获取数据。
    *   **UI 构建更优雅**：实现全局布局、错误边界、加载状态非常简单。
    *   **高级路由**：支持并行路由（Parallel routing）和拦截路由（Intercepting routes）等高级模式。
*   **痛点/缺点**：
    *   **激进的缓存机制**：这是目前开发者最大的痛点，*缓存行为复杂、令人困惑，且有时会导致不符合预期的结果*。
    *   **学习曲线陡峭**：相比老路由，需要理解更多新概念（如 RSC、服务端/客户端边界等）。
 
### Pages Router（传统/遗留）
*   地位：自 2016 年 v1 版本以来的老路由。虽然**不再推荐用于新项目**，但官方仍会积极维护。
*   **特点**：
    *   数据获取依赖 Next.js 专属 API（如 `getStaticProps`, `getServerSideProps`），且只能在**页面级别**使用，无法在单个组件中使用。
    *   实现全局布局等功能相对繁琐。
    *   学习曲线相对平缓，概念更简单。

![[_posts/后端/课程/终极 React 课程 2025：React、Next.js、Redux 等技术的学习/5.第五部分：使用 Next.js 实现全栈 React 应用/media/2adf1215746033f6cba85362a2cae3d2_MD5.webp]]

## 4. 课程后续安排
*   本课程将**全面聚焦于现代的 App Router**，从零开始深入讲解。
*   在课程（第五部分）的最后，会提供一个简短的 Pages Router 基础速成模块，以便开发者在维护老旧项目时能够看懂。
*   **下一步**：准备动手搭建第一个 Next.js 项目。
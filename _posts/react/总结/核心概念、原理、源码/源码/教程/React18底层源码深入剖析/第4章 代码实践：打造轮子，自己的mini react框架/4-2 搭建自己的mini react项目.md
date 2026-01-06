

本视频讲述了**如何搭建一个mini react项目框架**，包括*初始化项目、配置mono repo结构、创建子模块、添加依赖关系*，并通过测试验证基础环境的正确性。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第4章 代码实践：打造轮子，自己的mini react框架/media/453f2701b289e7a92fc57524f0da5195_MD5.webp]]

### 课程概述与项目目标 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)

- 课程介绍
    - 开始**搭建用于后续手写实现React核心功能的mini react项目**。
    - 后续所有手写内容都基于该架子展开。
- 项目成品展示
    - 项目名为 `my-mini-react`。
    - 包含 `examples` 文件夹，为基于Vite构建的React项目。
    - 将使用自定义实现的React API（如 `react`, `react-dom`, `reconciler`, `scheduler`）替换官方API。
    - `scheduler` 文件夹中包含测试用例目录 `__tests__` 及其TS测试文件。
![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第4章 代码实践：打造轮子，自己的mini react框架/media/c79475338d83d5222da4a2ef1f4f170a_MD5.webp]]

### 初始化项目 
[01:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=82)

- 创建项目文件夹
    - 创建名为 `my-mini-react` 的根文件夹。
- 初始化命令
    - 使用 `pnpm` 初始化项目（需提前全局安装 `pnpm`）。
    - 命令：`pnpm init`
- 构建 examples 子项目
    - 使用 Vite 构建 React TS 项目：
    ```
        pnpm create vite@latest examples --template react-ts
    ```
    - 进入 `examples` 目录后安装依赖并启动开发服务器。

### 配置 mono repo 结构 
[02:51](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=171)

- 什么是 mono repo？
    - 一种软件开发策略，*多个子项目统一管理*。
    - 类似 React 等*大型开源项目采用此方式*。
- 创建 packages 文件夹
    - 用于存放*核心源码*模块。
- *初始化子项目*
    - 在 `packages` 下创建以下子项目并分别`pnpm init`初始化：
        - `react`
        - `react-dom`
        - `react-reconciler`
        - `scheduler`
        - `shared`
    - 注意执行顺序和路径切换问题。

### 安装公共依赖 
[03:59](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=239)

- 安装测试框架 vitest
    - 全局安装命令：
        ```
        pnpm add -Dw vitest
        ```
    - `-Dw` 表示*在 workspace 根目录下*安装*开发依赖*。
- 测试 shared 模块
    - 在 `shared` 中编写类型定义和工具函数（如 `isString`, `isArray`, `isFunction`）。
    - 在 `scheduler/__tests__/scheduler.test.ts`中引用并测试这些函数。
- 运行测试用例
    - 修改 `package.json` 添加测试脚本：
        ```json
        "scripts": {
          "test": "vitest"
        }
        ```
    - 执行命令：`pnpm test`
    - 成功运行两个测试用例，验证环境配置正确。

### 配置模块间依赖 
[07:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=420)

- 添加子项目间的依赖关系
    - 如 `scheduler` 依赖 `shared`，则在 `scheduler/package.json` 中添加：
        
        ```json
        "dependencies": {
          "shared": "*"
        }
        ```
        
    - 使用 `*` 表示*始终使用最新版本，避免频繁修改版本号*。
- 验证依赖是否生效
    - 在 `scheduler` 测试代码中成功导入 `shared` 模块函数并运行通过测试。

### 总结与后续准备 
[07:50](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=470)

- 本节课完成 mini react 项目的架子搭建。
- 包括项目初始化、mono repo 配置、子模块创建、依赖管理和测试验证。
- 架子搭好后即可进入后续的手写 React 核心源码课程。
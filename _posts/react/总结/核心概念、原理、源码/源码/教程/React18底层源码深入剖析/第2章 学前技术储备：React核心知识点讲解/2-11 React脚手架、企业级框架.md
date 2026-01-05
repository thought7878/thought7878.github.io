

本视频讲述了*React脚手架及企业级框架的选择与比较*，涵盖*CRA、Next.js、Remix、Gatsby、Vite、Dva、Umi等工具的功能、适用场景及优缺点分析*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/96c147bbf9b1208313bb76b5941a3823_MD5.webp]]

### 主旨概述 
[00:06](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=6)

- 背景介绍  
    视频围绕*如何开启一个React项目*展开，重点讲解*主流脚手架和企业级框架的使用与选择*。

### 第一部分：Create React App（CRA）
[00:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=32)

- CRA简介
    - CRA是Facebook官方提供的脚手架工具。
    - 曾长期被React官网推荐作为项目启动工具。
- 功能评价
    - *功能较为基础*，缺少状态管理、路由等功能。
    - 开发企业级项目需手动添加大量配置。
- 现状说明
    - 官网已不再主推，但仍在某些项目中使用。

### 第二部分：React官网推荐框架 
[02:06](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=126)

- Next.js [02:06](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=126)
    - *全栈式React框架*，广泛用于*静态博客、动态应用及SSR项目*。
    - *国内用户群体庞大*，由大厂维护，支持部署至Node.js服务器。
- Remix [03:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=184)
    - 同为*全栈式React框架*。
    - 支持多种部署目标，可通过适配器部署到Node或Serverless环境。
- Gatsby [03:23](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=203)
    - 适用于*快速构建CMS类网站*。
    - 插件系统丰富，适合需要连接Contentful等服务的项目。
- Expo [04:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=240)
    - *面向React Native原生应用开发*。
    - 更适合演示和沙盒环境，*不适合生产级原生应用开发*。

### 第三部分：非官方流行框架 
[04:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=272)

- Vite [04:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=272)
    - 用户群增长迅速。
    - 轻量级，适合快速开发，支持React生态。
    - 国内多个团队在实际项目中使用。
- Dva [05:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=314)
    - 基于Redux的状态管理方案。
    - 将Redux、action、service整合在一个model文件中。
    - Dva脚手架已停止维护，但其子包Dva Core仍被umi等使用。
- Umi [06:42](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=402)
    - *企业级React框架，功能完善*。
    - 提供开箱即用的*国际化、路由、状态管理、测试*等功能。
    - *缺点*是对自定义配置支持有限，适合标准化项目如CMS系统。
	- Umi版本差异 [08:35](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=515)
	    - 当前最新稳定版为Umi 4。
	    - Umi 5.2在企业中仍有使用。
	    - 不同版本间存在破坏性改动，升级时需注意API变化。
	- Umi内部机制 [09:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=569)
	    - 内部实现包含自研逻辑，如路由模块未完全采用react-router v6。
- Ant Design Pro [10:02](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=602)
    - 基于Umi开发的*后台管理系统*解决方案。
    - 提供丰富的预设组件与功能项，*适合快速搭建后台项目*。

### 第四部分：其他开发方式 
[12:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=727)

- 纯手工搭建 [12:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=727)
    - 可*从零开始搭建项目结构*。
    - 多数大型公司项目采用此方式。
- 工程化工具辅助 [12:20](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=740)
    - 使用Vite、Webpack、Rollup等工具创建模板。
    - Vite与IPad提供初始化命令，可直接生成React项目模板。
    - 手动搭建对开发者要求较高，但灵活性更强。

### 总结与建议 
[11:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=689)

- 根据项目类型选择合适框架：
    - 企业级标准项目推荐使用Umi或Ant Design Pro。
    - 快速搭建可选用Next.js、Gatsby或Vite。
    - 需高度定制项目可考虑纯手工搭建结合工程化工具。
- 不同团队可根据自身技术栈选择阿里系或其他公司开源框架。
- 框架选择应综合考虑star数、社区活跃度、文档完整性和维护频率。
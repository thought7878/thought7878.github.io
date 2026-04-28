本视频讲述了在 Vite 开发环境下配置 devServer proxy 解决前后端分离开发中的跨域及接口未就绪问题。通过安装 Axios、配置 Vite 代理规则将 /api 请求转发至本地 Node.js 服务器（端口 8081），并结合 TypeScript 可选链操作符安全发送请求，最终验证了正向代理的成功转发与数据获取。

- **Vite Dev Server Proxy 背景与概念 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=0)**
    
    - **开发环境痛点**  
        前端与后端并行开发时，服务端 API 可能尚未完成。此时需将前端请求代理到本地服务器或使用 Mock 接口模拟数据 [00:06](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=6)。
    - **跨域与正向代理**  
        模拟接口可能涉及跨域问题。通过 Vite 做代理（正向代理）可避免跨域限制。后续章节将对比反向代理 [00:35](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=35)。
- **环境准备：安装 Axios [00:59](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=59)**
    
    - **工具选择**  
        使用 Axios 作为 HTTP 请求库，可通过 GitHub 查看文档 [01:06](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=66)。
    - **安装步骤**  
        在项目目录下执行 `npm install axios`。安装成功后，`package.json` 中会显示 Axios 版本信息 [01:18](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=78)。
- **配置 Vite 代理规则 [01:41](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=101)**
    
    - **配置文件定位**  
        打开 `vite.config.ts`，找到 `devServer` 配置项。默认仅包含端口号（如 `port: 3000`）[01:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=108)。
    - **Proxy 规则设置**  
        配置 `proxy` 对象，设定规则：监听以 `/api` 开头的请求，将其代理转发至 `http://localhost:8081`[01:57](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=117)。
    - **目标端口说明**  
        目标端口 8081 对应之前演示的 Node.js HTTP Server（基于观察者模式实现），该服务已启动并监听 8081 端口 [02:27](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=147)。
    - **代理逻辑**  
        所有以 `/api` 开头的请求将被 Vite 拦截并转发至 8081 端口。若无代理，直接请求前端端口（3000）下的 `/api` 路径将返回 404，因为前端服务本身不提供该接口 [03:07](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=187)。
- **前端代码实现：TypeScript 与 Axios [03:25](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=205)**
    
    - **DOM 元素获取与安全访问**  
        使用 `document.getElementById('btn1')` 获取按钮元素。由于 TypeScript 中 `getElementById` 返回值可能为 `HTMLElement`或 `null`，需使用可选链操作符 `?.` [03:35](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=215)。
    - **可选链操作符 `?.` 的作用**  
        若前方表达式结果为 `null` 或 `undefined`，则停止执行后续方法调用且不报错；若非空，则正常执行 `addEventListener` [04:16](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=256)。
    - **发送请求逻辑**  
        引入 `axios`。在点击事件中调用 `axios.get('/api/getInfo')`。注意请求 URL 必须以 `/api` 开头以命中代理规则 [04:47](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=287)。
    - **结果处理**  
        请求成功后，通过 `.then` 回调打印结果。建议打印 `results.data` 以获取具体响应内容 [05:18](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=318)。
- **运行验证与结果分析 [05:26](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=326)**
    
    - **启动服务**  
        启动 Vite 开发服务器，刷新页面 [05:34](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=334)。
    - **网络请求观察**  
        点击“发送请求”按钮，在浏览器 Network 面板观察：
        - 请求地址显示为 `http://localhost:3000/api/getInfo`[05:57](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=357)。
        - 返回状态码虽在预览中可能显示异常，但实际获取到数据 "Hello" [06:13](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=373)。
    - **后端日志确认**  
        查看 Node.js 服务器控制台，显示接收到 URL 为 `/api/getInfo` 的请求，证明请求已成功转发至 8081 端口 [06:26](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=386)。
    - **前端控制台输出**  
        浏览器 Console 打印出 Axios 响应对象，其中 `data` 字段值为 "Hello"，与 Node.js 服务器返回一致 [07:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=420)。
- **总结：代理模式的应用 [07:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=443)**
    
    - **无代理的后果**  
        若无代理，前端直接请求 `localhost:3000/api/getInfo` 会因前端服务无此路由而返回 404 [07:37](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=457)。
    - **代理架构角色**
        - **使用方**：网页前端，只与 Vite 服务器（3000 端口）交互 [08:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=495)。
        - **代理方**：Vite Dev Server，负责接收请求并转发 [07:55](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=475)。
        - **目标方**：Node.js Server（8081 端口），实际处理业务逻辑并返回数据 [08:19](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=499)。
    - **核心价值**  
        通过 Vite 的正向代理，实现了前端开发与后端接口的解耦，解决了跨域及后端未就绪时的调试问题 [07:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=468)。
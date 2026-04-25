本视频讲述了postMessage跨文档通信机制的原理、应用场景及网页与iframe双向通信的完整实现过程，包括发送方调用`postMessage`方法、接收方注册`message`事件监听器、目标窗口获取方式（`contentWindow`/`parent`）、目标源参数（`*`通配符）等核心要点，并强调其本质为发布-订阅模式。


- **postMessage的应用场景概述 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=0)**
    
    - **核心概念：postMessage通讯机制** [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=0)  
        postMessage是前端广泛应用的跨文档通信机制，用于网页与iframe、多进程（Node.js）、Web Worker、WebSocket等场景的通讯。
    - **演示范围限定说明** [00:17](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=17)  
        因多进程、Web Worker、WebSocket等需复杂实例环境，本课程仅以网页与iframe通信作为典型示例统一演示，其余场景通讯形式相同。
    - **通讯模式本质** [00:37](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=37)  
        所有基于postMessage的通讯均采用发布-订阅模式，发送方与接收方无需相互认知，可位于不同页面或组件中。
- **开发环境搭建与Demo初始化 [00:55](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=55)**
    
    - **本地服务配置** [00:55](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=55)  
        需安装ATP server库，通过命令`atp-server -p 8881`启动本地HTTP服务，端口可自定义。
    - **Demo访问与文件结构** [01:31](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=91)  
        访问`http://localhost:8881`加载demo.html；项目包含`main.html`（主页面）与`child.html`（iframe嵌入页面）两个HTML文件。
- **主页面（main.html）通信实现 [02:26](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=146)**
    
    - **iframe嵌入与DOM引用** [02:26](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=146)  
        在`main.html`中创建`<iframe id="iframe1" src="child.html">`，通过`window.iframe1.contentWindow`获取子页面window对象。
    - **向iframe发送消息** [03:36](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=216)  
        为按钮绑定click事件，调用`window.iframe1.contentWindow.postMessage("Hello", "*")`，其中`"*"`表示不限制目标源域名。
- **子页面（child.html）消息接收 [04:11](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=251)**
    
    - **监听message事件** [04:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=282)  
        在`child.html`中调用`window.addEventListener("message", (event) => { console.log("child received", event.data); })`接收主页面发送的消息。
- **子页面（child.html）向主页面发送消息 [05:34](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=334)**
    
    - **获取父窗口引用** [05:54](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=354)  
        在`child.html`中通过`window.parent`获取主页面window对象。
    - **调用postMessage发送** [06:10](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=370)  
        按钮点击时执行`window.parent.postMessage("World", "*")`，向主页面发送字符串"World"。
- **主页面（main.html）消息接收 [06:30](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=390)**
    
    - **主页面监听逻辑** [06:30](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=390)  
        在`main.html`中添加`window.addEventListener("message", (event) => { console.log("main received", event.data); })`接收子页面发来的消息。
- **双向通信验证与模式总结 [07:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=441)**
    
    - **通信流程验证** [07:02](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=422)  
        点击主页面“发送消息”按钮，控制台输出`child received Hello`；点击子页面按钮，控制台输出`main received World`，双向通信成功。
    - **发布-订阅模式本质** [07:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=441)  
        通信双方解耦：一方监听`message`事件（订阅者），另一方调用`postMessage`（发布者），二者无需直接引用，符合发布-订阅设计范式。
    - **与自定义事件的等价性** [07:50](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=470)  
        尽管语法不同，postMessage机制在设计思想与运行机制上与自定义事件完全一致。
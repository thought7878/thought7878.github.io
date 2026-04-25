本视频讲述了前端开发中自定义事件的实现原理与实践，重点对比了轻量级库 `mitt` 与老牌库 `events` 的核心用法：`mitt` 支持 `on`/`emit`/`off`，但缺失 `once` 功能；`events` 补全 `once` 语义并支持多参数传递，二者均基于发布-订阅模式，依赖全局唯一事件发射器实例（单例）实现跨组件通信。


- **自定义事件场景引入与版本演进说明 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=0)**
    
    - **Vue2 与 Vue3 的功能变迁 [00:04](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=4)**  
        Vue2 自带自定义事件能力，Vue3 已移除此功能，转而推荐使用第三方库 `mitt` 或 `events`。
    - **库命名解析：`mitt` 与 `events` [00:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=28)**  
        `mitt` 非标准英文单词，由 `emit` 去掉首字母 `e` 构成，体现其轻量级特性；`events` 库名直接取自 `emitter` 核心概念，MITT 即 `emit` 的变体。
- **轻量级库 `mitt` 的安装与初始化 [01:29](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=89)**
    
    - **安装命令与体积特征 [01:32](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=92)**  
        通过 `npm install mitt` 安装，库体积仅约 200 字节，强调极简、稳定。
    - **类型声明处理 [02:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=120)**  
        TypeScript 环境下需手动添加单词 `mitt` 至词典以消除拼写提示警告。
- **`mitt` 的核心 API：`on` 绑定与 `emit` 触发 [02:54](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=174)**
    
    - **事件绑定语法 [03:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=180)**  
        `mittInstance.on('change', callback)`，事件名 `'change'` 可任意自定义。
    - **回调函数执行 [03:04](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=184)**  
        绑定多个同名事件（如 `change`）时，所有回调均被依次执行，输出 `change 1`、`change 2`。
    - **发布-订阅模式验证 [03:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=222)**  
        `mitt` 实例为单例，可在 A 组件绑定事件、B 组件触发事件，实现解耦通信。
    - **`off` 方法说明 [04:27](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=267)**  
        `mittInstance.off('change', callback)`用于取消指定事件监听，功能类比 `removeEventListener`。
- **`mitt` 的局限性：缺失 `once` 语义 [05:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=315)**
    
    - **`once` 语义定义 [05:17](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=317)**  
        `once` 表示事件仅触发一次，后续 `emit` 不再执行对应回调。
    - **`mitt` 的行为特征 [05:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=321)**  
        `on` 绑定的事件每次 `emit` 均会执行全部回调，无自动解绑机制。
- **老牌库 `events` 的引入与类型配置 [05:43](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=343)**
    
    - **安装与稳定性 [05:53](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=353)**  
        通过 `npm install events` 安装，发布超五年，下载量高，稳定性强。
    - **TypeScript 类型支持 [06:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=402)**  
        需额外安装开发依赖 `@types/events`（命令：`npm install -D @types/events`），否则 TS 编译报错。
- **`events` 的增强 API：`once` 与多参数传递 [07:27](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=447)**
    
    - **`once` 绑定验证 [07:47](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=467)**  
        `eventEmitter.once('change', callback)`绑定后，仅首次 `emit('change')` 触发回调，后续无效。
    - **多事件混合绑定 [08:09](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=489)**  
        同一事件名可同时存在 `on` 和 `once` 绑定；首次触发时全部执行，后续仅 `on` 绑定持续生效。
    - **参数传递机制 [08:57](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=537)**  
        `emit('change', value)` 可传入任意数量参数（如字符串 `'ABC'`、对象 `{name: 'string'}`），回调函数形参顺序接收。
        - 第二次 `emit` 未传参时，对应形参值为 `undefined`。
- **发布-订阅模式本质与单例约束 [10:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=600)**
    
    - **跨组件通信前提 [10:05](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=605)**  
        所有模块必须引用**同一个 `mitt` 或 `events` 实例**，该实例须为全局单例（如通过 `export const emitter = mitt()` 导出）。
    - **解耦性强调 [10:13](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a191ff84cab54538af78c790dfebe79e#?seek_t=613)**  
        事件绑定方与触发方无需相互引用或知晓对方存在，仅依赖共享的事件发射器实例即可完成通信。


本视频讲述了*React组件复用的三大核心条件、常见性能优化错误*，包括*key值不规范导致的组件无法复用问题*，并通过虚拟DOM对比过程和具体代码示例进行详细说明。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/804bfc2bd9903d108817eab253b63a15_MD5.webp]]

### 性能优化概述 
[00:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=4)

- 主要方法  
    总结了三条关于组件的常见性能优化方法。

### 第一条：组件复用原则 
[00:09](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=9)

- `复用组件`概念解释 [00:13](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=13)  
    组件复用类似于“在家已有99万基础上只赚1万”的策略，强调*利用已有资源更新页面*。
- React源码判断机制 [01:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=76)  
    React通过判断新旧组件是否为同一组件决定是否复用。
- 虚拟DOM匹配原理 [01:31](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=91)  
    React使用diff算法判断虚拟DOM节点是否可复用。

### Diff算法策略 
[02:32](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=152)

- **组件复用，三个必要条件：** [02:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=157)
    - 同一层级下
    - 同一类型
    - 同一个key值
- 深度优先遍历策略 [02:58](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=178)  
    从根节点开始逐层向下进行diff比较。
- 复用失败案例分析 [03:28](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=208)  
    类型不同的节点（如文本节点与函数组件）无法复用，需重新挂载。
- 层级变更限制 [04:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=296)  
    同一层级指相同父节点下的子节点，跨层级节点无法复用。
- 全局查找成本分析 [06:04](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=364)  
    跨层级查找将增加O(n)时间复杂度，实际场景中层级变更较少，不值得为此付出额外开销。

### 组件更新流程演示 
[08:10](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=490)

- 删除不可复用节点 [08:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=494)  
    老的vdom节点及其对应的web dom节点均需删除。
- 位置变更处理 [08:34](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=514)  
    同一层级下位置变更可通过web dom操作更新，不影响复用性。
- 最终完成更新 [09:01](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=541)  
    深度优先便利完成后，整个组件树更新完毕。

### Key值使用规范 
[09:29](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=569)

- 稳定性要求 [09:14](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=554)  
    需保证层级、类型和key值三者稳定。
- 常见错误写法 [09:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=577)
    - 使用`Math.random()`生成key值  
        每次挂载生成新key，组件永远无法复用。
    - 使用index作为key值  
        顺序变化时key不稳定，影响组件复用。
- 推荐做法 [13:27](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=807)
    - 使用唯一ID或拼接方式确保key稳定
    - 静态渲染可接受index，但动态操作中易出错

### 总结与建议 
[14:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=840)

- 复用组件是提升性能的核心策略
- 开发者需理解diff算法机制，避免因key值不规范导致性能问题
- 该类问题虽不表现为功能错误，但属于性能层面的潜在bug，需引起重视
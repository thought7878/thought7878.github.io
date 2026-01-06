

本视频讲述了如何通过修改项目配置实现React源码调试，包括*替换node_modules路径为本地源码目录、使用书签工具Bookmarks、代码辅助插件Better Command*等方法，并*提供了已配置好的压缩包*以简化操作流程。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第3章 学习前：底层源码的高效学习方法分享/media/30a2ff4f0dcb3db4a51210aa4da6d09d_MD5.webp]]

- 课程目标与调试思路 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)
    - 介绍如何debug React源码以掌握其运行逻辑。
    - 提出问题：如何确认React执行的具体代码路径。
- 源码调试配置方法 [00:22](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=22)
    - 默认情况下import的React API来自node_modules。
    - node_modules中的文件多为压缩版本，不适合直接调试。
    - 解决方案：使用GitHub下载的React源码替代node_modules路径。
    - 修改项目配置，将路径指向本地源码目录（如SRC/react/packages）。
    - 涉及的核心文件包括react、react-dom、scheduler等。
- 调试验证示例 [02:08](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=128)
    - 在example page页面渲染时添加log输出。
    - 展示log路径是否指向本地源码目录。
    - 可在源码中打印fiber结构，查看其具体属性。
- 项目配置注意事项 [04:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=245)
    - 修改配置项技术难度不高但较繁琐。
    - React源码频繁更新导致配置易失效。
    - 推荐使用讲师提供的压缩包，内含最新React 18.2版本及已完成配置。
    - 压缩包可避免手动修改配置带来的变动性问题。
- 推荐调试工具 [08:11](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=491)
    - Bookmarks：
        - 用于标记源码阅读位置。
        - 支持快速跳转至特定函数或逻辑段。
        - 快捷键：Command + Option + K（Mac）。
    - Better Command：
        - 支持添加注释并高亮显示。
        - 可记录学习过程中的理解与疑问。
    - 小机器人（AI代码助手）：
        - 智能补全代码和注释。
        - 当前阶段免费使用，未来可能收费。

# 夸克总结

本视频讲述了React开发的核心规则，包括*组件化、状态管理与单项数据流*的概念及应用，并*通过类比和实例帮助理解如何用React的方式进行开发*。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/7905de20019db3639dcd9ed4fa673844_MD5.webp]]

##  引入：什么是Thinking in React 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=0)
    
- `React的开发方式`需要**遵循其规则**，不能简单套用其他框架（如Vue）的经验。
- *类比靠左行驶在中国行不通*，不按React规则写代码可能导致问题。

## React的三大核心规则概述 
[01:54](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=114)
    
- 第一条：**组件化**。
- 第二条：**状态**。
- 第三条：**单项数据流**。

## 第一条规则：组件化 
[02:12](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=132)
    
- React是一个*构建Web或原生交互界面*的库。
- 组件化在React中更自然，无需手动处理数据隔离等问题。
- 示例说明*组件化的拆分与复用：*
	- 页面*可拆分为多个组件*。
	- 组件*可在不同页面复用*，*提升开发效率与后期维护性*。
- 不合理组件划分的*反例：*
	- 某同事将整个页面写成一个大组件，导致代码臃肿（5k~10k行），难以维护与复用。
- **建议：**
	- *控制组件颗粒度*。
	- *降低组件间耦合性*。
	- 使用props、context、状态管理库实现组件通信。

## 第二条规则：状态（State） 
[05:43](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=343)
    
- `状态`是**变量**，**data变化时UI应随之更新**。
- **公式：**`UI = f(data)`，类比数学函数 `f(x) = x + 1`。
- 特殊情况：
	- 拦截机制：
		- 类组件可*通过 `shouldComponentUpdate`、`PureComponent`拦截更新*。
		- 函数组件可*通过 `React.memo`缓存组件*。
	- useState vs useReducer：
		- `useState` *在值未改变时不触发更新*（React源码有拦截）。
		- `useReducer` *不做此优化，即使state未变也会更新组件*。
- 状态定义方式：
	- 函数组件：`useState`、`useReducer`。
	- 类组件：`this.state` + `setState()`。
- 状态管理库简介：
	- Redux、MobX、Recoil、Zustand等。
	- Recoil为Facebook官方支持的状态管理库。
	- 其他库为第三方JS库，常与React配合使用。

### 与其他类型值的区分 
[09:46](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=586)
    
- 状态值（State）：
	- 变量，改变后组件需更新。
- 引用值（Ref）：
	- 如DOM节点引用，使用 `useRef` 定义，**不触发更新**。
- 普通变量：
	- 无须记忆的临时变量，直接定义即可。
- 昂贵计算结果：
	- 使用 `useMemo` 或 `useCallback` 进行缓存，避免重复计算。

## 总结与预告 
[11:56](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=716)
    
- 当前仅介绍了单个组件内的状态使用。
- 后续章节将深入讲解组件间状态共享、状态管理库的选型与实现等内容。
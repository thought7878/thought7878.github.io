
## What

 **Virtual DOM 是什么：**

1. 虚拟 DOM 是对真实 DOM 的描述。 
2. 虚拟 DOM 是 JS 对象

是*JS 和 DOM 之间的一个映射缓存*，它在形态上表现为一个能够描述 DOM 结构及其属性信息的 *JS 对象*

![[react/总结/media/7adfa16a872f73241e91e02dae65c251_MD5.png]]

### 虚拟 DOM 大致是如何工作的

**React 中的虚拟 DOM 大致是如何工作的**。虚拟 DOM 在 React 组件的挂载阶段和更新阶段都会作为“关键人物”出镜，其参与的工作流程如下：

- **挂载阶段**，React 将结合 JSX 的描述，构建出虚拟 DOM 树，然后通过 ReactDOM.render 实现虚拟 DOM 到真实 DOM 的映射（触发渲染流水线）；
- **更新阶段**，页面的变化在作用于真实 DOM 之前，会先作用于虚拟 DOM。新与旧的虚拟 DOM ，将在 JS 层，借助算法先对比出具体有哪些真实 DOM 需要被改变，然后再将这些改变作用于真实 DOM。
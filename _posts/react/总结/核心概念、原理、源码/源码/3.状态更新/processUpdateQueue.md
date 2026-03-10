**通过`baseState`、`Update`，计算新的state。**

![[_posts/react/总结/核心概念、原理、源码/源码/教程/2.自顶向下学 React 源码/5.实现篇之状态更新/media/3de2db3b44a1c661e1a84023c05d9d3d_MD5.webp]]

主要功能是：

1. **处理更新队列**：遍历fiber节点上的更新队列，按优先级处理状态更新
2. **优先级管理**：根据渲染优先级决定是否处理某个更新，低优先级更新会被跳过
3. **状态计算**：依次应用有效更新到基础状态，生成新的组件状态
4. **队列维护**：将已处理更新移至基础队列，保留未处理更新供后续渲染使用
5. **副作用收集**：收集需要执行的回调函数到effects队列中

## 源码
`packages/react-reconciler/src/ReactFiberClassUpdateQueue.old.js`

```ts


```

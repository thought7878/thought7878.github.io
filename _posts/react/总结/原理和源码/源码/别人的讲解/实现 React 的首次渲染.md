
# React的渲染流程
[00:00]
创建一堆Fiber节点，即创建Fiber树。进而，实现首次渲染。

[[React的渲染流程-DS]]

## 深度优先遍历
[00:41]

参考：[[深度优先搜索、遍历（DFS）]]、[[14、遍历专题：DFS 与 BFS]]、[6分钟学深度优先搜索DFS](https://www.bilibili.com/video/BV1B1QpYwELv/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)

# 源码
[03:10]
## index.js

## react.js
### createElement()
在编译时，Babel 将JSX转换成createElement()的调用。

输入：type、config、children
输出：ReactElement 对象

### Component 类

### render()
模拟ReactDom.render()

renderRootSync()：渲染阶段
commitRoot()：commit阶段



### 
```js
const root = {
	container: document.getElementById('root')
}

const hostRootFiber: FiberNode = createHostRootFiber(); // 根fiber节点

root.current = hostRootFiber // 保存根fiber节点
hostRootFiber.stateNode = root // stateNode表示真实DOM节点

let workInProgress = createWorkInProgress(hostRootFiber,{children:element}); // 根fiber节点的副本
```

### renderRootSync()
[10:36]
深度优先搜索：
- 先从上到下，遍历节点
- 再从左到右，遍历节点
- 没有下也没有右了，遍历完子节点、兄弟节点，向上回退


### beginWork()
[14:23]

输入：ReactElement
输出：Fiber节点

### completeWork()


## fiber.js
### FiberNode()
Fiber节点的构造函数

### createFiber()


### createFiberFromElement()

## constants.js
常量：fiber tag 类型、协调过程中产生的副作用 flag、渲染优先级 lane

# 相关资料
[实现 React 的首次渲染](https://www.bilibili.com/video/BV16H89epER9/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)
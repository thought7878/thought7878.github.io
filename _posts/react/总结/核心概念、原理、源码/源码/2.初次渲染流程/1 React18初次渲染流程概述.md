>1. 创建根节点与初始化，这个详细介绍一下。比如，函数的调用流程，每个函数的功能

React 18的初次渲染流程采用了全新的并发模式架构，主要分为以下几个关键阶段：

## 1. 创建根节点与初始化
参考：[[1.1 创建根节点与初始化]]

- 通过`createRoot(container)` API**创建Fiber根节点（FiberRootNode）**
	- **创建根Fiber节点（rootFiber）** 并**初始化更新队列**
- **调用`root.render(<App/>)`触发渲染流程**

## 2. 更新调度阶段
参考：[[1.2 更新调度阶段]]

- 生成初始Update对象，放入UpdateQueue
- 调用`scheduleUpdateOnFiber`**进行任务调度**
- 确定优先级（初次渲染通常为SyncLane，同步优先级）

## 3. Render阶段（协调/Reconciliation）
参考：[[1.3 Render阶段（协调、Reconciliation）]]

- **beginWork流程**：深度优先遍历Fiber树
  - HostRoot: 处理根节点
  - 函数组件：执行函数获取children
  - 类组件：实例化组件，调用render方法
  - 原生DOM节点：准备创建DOM节点

- **completeWork流程**：
  - 为原生DOM节点创建真实DOM元素
  - 构建Fiber节点间的父子/兄弟指针
  - 收集副作用（effects）链表
  - 为DOM节点设置props（如onClick等事件）

## 4. 提交阶段（Commit，不可中断）
参考：[[1.4 提交阶段（Commit，不可中断）]]

分为三个子阶段：
- **before mutation**：读取DOM状态，触发getSnapshotBeforeUpdate
- **mutation**：执行DOM变更（插入、更新、删除节点）
- **layout**：执行useLayoutEffect和componentDidMount等同步生命周期

## 5. 渲染后处理
参考：[[1.5 渲染后处理]]

- 异步执行useEffect钩子
- 触发onRender完成回调
- 启用并发特性（如自动批处理）

## React 18特有机制
- **并发渲染**：即使初次渲染也使用可中断的Fiber架构
- **自动批处理**：合并多个状态更新
- **严格模式改进**：开发环境下会*故意双渲染*以*检测副作用*
- **Suspense支持**：可通过Suspense在初次渲染时进行数据请求
- **lane模型**：*使用更细粒度的优先级系统*替代expirationTime

**整个流程遵循"render-commit"两阶段架构：**
- `render阶段`可中断恢复，
- `commit阶段`同步执行，确保UI更新的高效性与一致性。

React 18通过这些优化显著提升了初次渲染性能和用户体验。
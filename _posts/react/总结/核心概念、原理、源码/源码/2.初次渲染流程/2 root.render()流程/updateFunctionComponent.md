主要功能：
- **执行组件函数体，获取新的ReactElement，处理所有 hooks 调用**
- **如果无需更新则提前退出(bailout)**：如果当前组件没有接收到任何更新（即 props/state 没有变化），可以跳过后续的协调过程，直接复用之前的渲染结果
- **协调生成子 Fiber 节点**：基于新生成的 React Element和现有的 Fiber 树 (current)，创建或更新对应的 Fiber 节点，构建新的 work-in-progress Fiber 树
- 返回生成的workInProgress.child子 Fiber 节点，继续下一个循环，构建fiber树
*详细解释queue的pending属性和interleaved属性之间的区别*。

## pending 属性

1. **用途**：存储*常规的待处理更新*队列（如点击事件产生的更新），*这些更新在进入渲染阶段之前已经被调度*
2. **数据结构**：循环链表的最后一个节点
3. **处理时机**：在渲染开始前，会将[pending](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberClassUpdateQueue.old.js#L178-L178)队列中的更新合并到基础队列([baseQueue](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberHooks.old.js#L852-L852))中
4. **特点**：这是主要的更新队列，在正常情况下接收所有更新

## interleaved 属性

1. **用途**：专门用于*存储在渲染过程中新调度的更新（交错更新）*
2. **数据结构**：循环链表的最后一个节点
3. **处理时机**：在并发渲染期间，当组件正在渲染时新产生的更新会被放入这个队列
4. **特点**：这些更新不会在当前渲染周期中处理，而是等待当前渲染完成后在下一次渲染中处理

## 主要区别

### 1. 产生场景不同
- [pending](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberClassUpdateQueue.old.js#L178-L178)：**在渲染开始前就已经存在的更新**
- [interleaved](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberConcurrentUpdates.old.js#L72-L72)：**在渲染过程中新产生的更新**

### 2. 处理时机不同
- [pending](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberClassUpdateQueue.old.js#L178-L178)：**当前渲染周期处理**
- [interleaved](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberConcurrentUpdates.old.js#L72-L72)：**当前渲染周期不处理，延迟到下一轮渲染**

### 3. 实现并发安全
- [pending](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberClassUpdateQueue.old.js#L178-L178)：普通更新流程使用
- [interleaved](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberConcurrentUpdates.old.js#L72-L72)：并发更新流程使用，通过分离这两个队列，React可以*确保在渲染过程中新产生的更新不会干扰当前渲染的一致性*

### 4. 最终合并
**在渲染完成后，通过[finishQueueingConcurrentUpdates](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberConcurrentUpdates.old.js#L51-L70)函数，[interleaved](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberConcurrentUpdates.old.js#L72-L72)队列中的更新会被转移合并到[pending](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberClassUpdateQueue.old.js#L178-L178)队列中，等待下一次处理。**

这种设计使React能够在并发模式下安全地处理渲染过程中的新更新，确保了渲染的一致性和更新不会丢失。
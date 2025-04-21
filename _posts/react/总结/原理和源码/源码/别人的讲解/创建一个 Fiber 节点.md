
## 创建Fiber节点：根据已有ReactElement


## 创建Fiber节点：根据已有Fiber节点
[03:30]

`stateNode`属性：当前Fiber节点对应的真实DOM节点。

**根据已有Fiber节点，创建新的Fiber节点，应用场景**：*首次渲染后的第一次更新*，此时内存中只有一棵Fiber树。current Fiber节点还没有alternate属性，就根据current Fiber节点创建一个新的副本Fiber节点。

## 相关资料
- [创建一个 Fiber 节点](https://www.bilibili.com/video/BV1C9gDeNEzF/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)


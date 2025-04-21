## React15面临的问题
视频：[00:25]

**同步渲染阻塞**，React15中的Reconcile调和过程

参考：[[同步渲染阻塞-DS]]

## Fiber节点
视频：[02:30]
`Fiber节点`，是JS对象，保存了当前节点的所有信息

### Fiber节点 VS ReactElement
视频：[02:40]

Fiber节点，是在ReactElement基础之上，得到的新对象。

**有了ReactElement，为什么还需要Fiber节点？为什么不只使用ReactElement？**
可以将两者结合。但是，官方*乐意*再构建一个Fiber节点。

### Fiber节点的属性
视频：[03:10]
参考：[[Fiber节点的属性]]

#### 来自ReactElement的属性

从ReactElement上拆解出来的属性：tag、type、props、ref、key

#### 链表属性

child、sibling、return



## 相关资料
- [React 源码 -- Fiber](https://www.bilibili.com/video/BV13mVaeMErm/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)
- [创建一个 Fiber 节点](https://www.bilibili.com/video/BV1C9gDeNEzF/?share_source=copy_web&vd_source=9c1e19a73fa7bd23bb37aa8d7467d862)


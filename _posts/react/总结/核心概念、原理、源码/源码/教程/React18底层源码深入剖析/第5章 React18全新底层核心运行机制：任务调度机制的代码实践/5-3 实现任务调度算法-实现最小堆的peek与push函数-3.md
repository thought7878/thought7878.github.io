

本视频讲述了*最小堆数据结构的实现原理与核心操作*，包括*定义堆节点类型、实现peek获取堆顶元素、push插入元素时的从下往上调整策略*，以及*通过 siftUp 函数维持最小堆特性*，详细演示了如何在 TypeScript 中构建并操作最小堆。

文件路径：`packages/scheduler/src/SchedulerMinHeap.ts`

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/4c529ccc205a5bf83f8e9539abb956b9_MD5.webp]]

## 最小堆的定义
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=0)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/c347a865bf9aac25b161e40fcb5e29be_MD5.webp]]

### 核心概念

- `最小堆`：满足父节点小于等于子节点的完全二叉树
- 数据结构：使用数组实现，节点关系通过索引计算
- 节点类型定义：

```typescript
export type Node = {
  id: number;       // 任务的唯一标识
  sortIndex: number; // 排序依据 / 值
};
```

### 堆的类型定义

```typescript
export type Heap<T extends Node> = Array<T>;
```

## peek函数：获取堆顶元素 
[02:16](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=136)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/b8536182c672d355db8a8b1ce9ee4c35_MD5.webp]]

### 函数实现

```typescript
export function peek<T extends Node>(heap: Heap<T>): T | null {
  return heap.length === 0 ? null : heap[0];
}
```

### 核心逻辑

1. **空堆检查**：当数组长度为0时返回null
2. **堆顶访问**：*直接返回数组第一个元素*`heap[0]`
3. **时间复杂度**：O(1)

### 测试用例

```typescript
it("empty heap returns null", () => {
  const tasks: Heap<Node> = [];
  expect(peek(tasks)).toBeNull();
});
```

## push函数：插入元素并调整堆 
[03:37](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=217)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/96a3209f6d2f19baaf9f33fce8b47b83_MD5.webp]]

### 插入流程

1. 尾部插入：将新元素添加到数组末尾
2. 从下往上调整：通过`siftUp`维护最小堆性质

### 函数实现

```typescript
export function push<T extends Node>(heap: Heap<T>, node: T): void {
  const index = heap.length;
  heap.push(node);
  siftUp(heap, node, index);
}
```

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/16fdeae29583ed60b7e4c903c1502ed2_MD5.webp]]

### 调整过程示意图

插入元素5的调整过程：

```
初始状态：
        3
      /   \
     7     4
   /  \   / \
 10   12 9   6
 / \
15 14
插入5：
        3
      /   \
     7     4
   /  \   / \
 10   12 9   6
 / \  /
15 14 5
第一次交换：
        3
      /   \
     7     4
   /  \   / \
 10   5 9   6
 / \  /
15 14 12
第二次交换：
        3
      /   \
     5     4
   /  \   / \
 10   7 9   6
 / \  /
15 14 12
```

## siftUp函数：从下往上的堆调整 
[08:45](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=649abdd1fba04df493057be1e8facc1d#?seek_t=525)

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/7e0f10e8ffa87d8d2688a7258dcd5fab_MD5.webp]]

### 函数实现

```typescript
function siftUp<T extends Node>(heap: Heap<T>, node: T, i: number): void {
  let index = i;
  while (index > 0) {
    const parentIndex = (index - 1) >> 1; // 位运算获取父节点索引
    const parent = heap[parentIndex];
    
    if (compare(parent, node) > 0) {
      // 父节点大于子节点，交换位置
      heap[parentIndex] = node;
      heap[index] = parent;
      index = parentIndex;
    } else {
      return; // 调整完成
    }
  }
}
```

### 比较函数

```typescript
function compare(a: Node, b: Node): number {
  const diff = a.sortIndex - b.sortIndex;
  return diff !== 0 ? diff : a.id - b.id;
}
```

### 时间复杂度分析

- 最坏情况：O(log n)，需要调整到根节点
- 最好情况：O(1)，插入元素本身满足堆性质
- 空间复杂度：O(1)，原地调整

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第5章 React18全新底层核心运行机制：任务调度机制的代码实践/media/37ef56e2f886860aacb15ee07e70476e_MD5.webp]]

### 完整调整过程示意图

插入元素5的完整调整流程：

```
初始堆：
        3
      /   \
     7     4
   /  \   / \
 10   12 9   6
 / \
15 14
插入5：
        3
      /   \
     7     4
   /  \   / \
 10   12 9   6
 / \  /
15 14 5
比较5和12：
        3
      /   \
     7     4
   /  \   / \
 10   5 9   6
 / \  /
15 14 12
比较5和7：
        3
      /   \
     5     4
   /  \   / \
 10   7 9   6
 / \  /
15 14 12
比较5和3：
停止调整
```
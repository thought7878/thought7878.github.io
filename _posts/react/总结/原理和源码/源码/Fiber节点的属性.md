React 的 `Fiber 节点` 是 *Fiber 架构的核心数据结构*，每个 React 元素（组件、DOM 节点等）对应一个 Fiber 节点，负责跟踪渲染状态、协调过程和副作用。

以下是 Fiber 节点的关键属性及其作用的系统性总结：

---

### 一、Fiber 节点的核心属性
#### 1. 标识与类型
   - **`tag`**：*标识 Fiber 节点的类型*（如函数组件、类组件、DOM 节点等），对应 `ReactWorkTags` 常量。
     ```typescript
     enum ReactWorkTags {
       FunctionComponent = 0,
       ClassComponent = 1,
       HostComponent = 5, // 如 div、span
       HostRoot = 3,      // 根节点（ReactDOM.render 挂载的节点）
       Fragment = 7,
       // ...其他类型
     }
     ```
   - **`key`**：唯一标识符，用于协调算法优化（*同层级节点对比*）。
   - **`type`**：关联的组件类型或 DOM 元素类型（如*函数组件引用、`'div'` 字符串*）。

#### 2. 树结构指针
   - **`child`**：指向第一个子 Fiber 节点。
   - **`sibling`**：指向下一个兄弟 Fiber 节点。
   - **`return`**（或 `parent`）：指向父 Fiber 节点。
   - **`index`**：当前节点在兄弟节点中的位置索引。

#### 3. 实例与状态
   - **`stateNode`**：关联的实例对象：
     - 对 DOM 元素：指向对应的真实 DOM 节点。
     - 对类组件：指向组件实例（`this`）。
     - 对根节点：指向 `FiberRoot`（应用根容器）。
   - **`pendingProps`**：本次渲染待处理的 props。
   - **`memoizedProps`**：上一次渲染完成的 props。
   - **`memoizedState`**：上一次渲染完成的状态（如 `useState` 的值）。
   - **`updateQueue`**：存储状态更新的队列（如 `setState` 的更新对象）。

#### 4. 副作用与更新标记
   - **`flags`**（旧版为 `effectTag`）：*标记当前节点需要执行的副作用类型*（如插入、更新、删除）。
     ```typescript
     enum ReactFiberFlags {
       Placement = 0b00000000000010, // 插入 DOM
       Update = 0b00000000000100,    // 更新属性
       Deletion = 0b00000000001000,  // 删除 DOM
       // ...其他副作用
     }
     ```
   - **`subtreeFlags`**：子树中所有副作用的聚合标记，用于优化遍历。
   - **`lanes`**：优先级标记（并发模式下），标识更新任务的优先级。
   - **`childLanes`**：子树的优先级标记。

#### 5. 双缓冲与协调
   - **`alternate`**：指向当前 Fiber 节点的“镜像”节点，用于双缓冲机制（[[双缓冲机制-QW]]）：
     - `current` 树：*当前已渲染*的 Fiber 树。
     - `workInProgress` 树：*正在构建*的新 Fiber 树。

---

### 二、Fiber 节点的生命周期相关属性
#### 1. 渲染阶段
   - **`mode`**：渲染模式（如 `ConcurrentMode`、`BlockingMode`、`LegacyMode`）。
   - **`expirationTime`**（旧版）：任务的过期时间（时间切片调度）。
   - **`lanes`**（新版）：替代 `expirationTime`，表示*任务优先级*。

#### 2. 提交阶段
   - **`firstEffect`**、**`lastEffect`**：当前节点及其子树的副作用链表首尾指针。
   - **`nextEffect`**：指向下一个需要处理的副作用节点。

---

### 三、函数组件与 Hooks
#### 1. Hooks 链表
   - **`memoizedState`**：函数组件的 Hooks 链表头节点。
     - 每个 Hook 节点存储 `useState`、`useEffect` 等的状态。
     ```typescript
     type Hook = {
       memoizedState: any,       // 当前状态值
       baseState: any,           // 基础状态（用于更新计算）
       baseQueue: Update<any>[], // 待处理的更新队列
       queue: UpdateQueue<any>,  // 更新队列（如 setState 的队列）
       next: Hook | null,        // 下一个 Hook
     };
     ```

#### 2. 副作用处理
   - **`flags`** 标记与 `useEffect`、`useLayoutEffect` 关联的副作用。

---

### 四、Fiber 节点的创建与更新流程
#### 1. 创建阶段
   - **协调（Reconciliation）**：*根据 React 元素创建或复用 Fiber 节点*，设置 `pendingProps` 和 `type`。
   - **标记副作用**：通过 Diff 算法确定 `flags` 和 `subtreeFlags`。

#### 2. 提交阶段
   - **副作用链表遍历**：根据 `firstEffect` 和 `nextEffect` 执行 DOM 操作。
   - **双缓冲切换**：将 `workInProgress` 树切换为 `current` 树。

---

### 五、示例：Fiber 节点结构
```javascript
// 一个典型的 Fiber 节点（简化版）
{
  tag: HostComponent,       // 标签类型
  key: 'header',            // 唯一标识
  type: 'div',              // DOM 元素类型
  stateNode: divDOM,        // 关联的真实 DOM
  return: parentFiber,      // 父节点
  child: firstChildFiber,   // 第一个子节点
  sibling: nextSiblingFiber,// 下一个兄弟节点
  pendingProps: { className: 'active' }, // 待处理的 props
  memoizedProps: { className: 'container' }, // 上次渲染的 props
  memoizedState: { count: 0 }, // 组件状态
  updateQueue: [/* 状态更新队列 */],
  flags: Update,            // 需要更新属性
  subtreeFlags: 0,
  lanes: NoLanes,
  alternate: previousFiber, // 镜像节点
  // ...其他属性
}
```

---

### 六、总结
- **Fiber 节点** 是 React 协调与渲染的核心单元，通过链表结构管理组件树。
- **关键属性** 包括树结构指针（`child`、`sibling`、`return`）、状态（`memoizedState`）、副作用标记（`flags`）和双缓冲（`alternate`）。
- **设计目标** 是实现可中断的异步渲染（时间切片）、优先级调度和高效的 DOM 更新。

理解 Fiber 节点的属性与机制，是深入掌握 React 渲染流程、性能优化和调试复杂问题的关键。
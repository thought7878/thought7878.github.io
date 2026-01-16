参考：[[Fiber节点的属性1]]

在 React 的 Fiber 架构中，**Fiber 节点**是核心数据结构，代表**组件树中的一个工作单元**。它不仅**包含组件/DOM 节点的信息**，还**存储了协调（Reconciliation）和渲染过程中的状态**。

以下是 Fiber 节点关键属性的详细说明（基于 React 18 源码）：

---
## 属性
### 核心结构属性
1. **`tag`**  
   - **类型**: `number`  
   - **作用**: 标识 Fiber 节点类型（*对应 React 内部组件类型*枚举）。  
   - **常见值**:  
     - `0`: `FunctionComponent`  
     - `1`: `ClassComponent`  
     - `5`: `HostComponent`（原生 DOM 节点，如 `div`）  
     - `6`: `HostText`（文本节点）  
     - `7`: `Fragment`  
     - `10`: `ContextProvider`  
     - `12`: `SuspenseComponent`  
     - `13`: `MemoComponent`  
     - `16`: `LazyComponent`  
     - `3`: `HostRoot`（根节点，`ReactDOM.render` 容器）

2. **`type`**  
   - **类型**: `any`  
   - **作用**: 组件的类型或 DOM 节点名称。  
   - **示例**:  
     - 函数组件：`() => <div/>`  
     - 类组件：`class App extends Component`  
     - 原生节点：`'div'`, `'span'`  
     - 特殊类型：`Context.Provider`, `Suspense`

3. **`key`**  
   - **类型**: `string | null`  
   - **作用**: 唯一标识符，用于优化列表 diff（同层级节点复用）。

4. **`stateNode`**  
   - **类型**: `any`  
   - **作用**: 关联的实例对象。  
   - **具体值**:  
     - 类组件 → 组件实例  
     - 原生 DOM 节点 → 真实 DOM 元素（如 `HTMLDivElement`）  
     - 函数组件 → `null`（无实例）  
     - `HostRoot` → `ReactDOMRoot` 实例

---

### 树结构指针
5. **`return`**  
   - **类型**: `Fiber | null`  
   - **作用**: 指向父 Fiber 节点。

6. **`child`**  
   - **类型**: `Fiber | null`  
   - **作用**: 指向第一个子 Fiber 节点。

7. **`sibling`**  
   - **类型**: `Fiber | null`  
   - **作用**: 指向右侧兄弟 Fiber 节点。

8. **`index`**  
   - **类型**: `number`  
   - **作用**: 在兄弟节点中的位置索引（用于列表 diff）。

---

### 更新与状态管理
9. **`pendingProps`**  
   - **类型**: `any`  
   - **作用**: **新**的 props（等待被处理）。

10. **`memoizedProps`**  
    - **类型**: `any`  
    - **作用**: **上一次渲染使用**的 props（用于对比变化）。

11. **`memoizedState`**  
    - **类型**: `any`  
    - **作用**: **上一次渲染使用**的 state。  
    - **关键用途**:  
      - 类组件：存储实例的 `state`  
      - 函数组件：存储 `useState`/`useReducer` 的状态链表

12. **`updateQueue`**  
    - **类型**: `UpdateQueue | null`  
    - **作用**: *存储待处理的更新*（`setState`、`useState` 触发的更新）。  
    - **结构**: 链表形式，包含 `action`（更新内容）和优先级信息。

13. **`dependencies`**  
    - **类型**: `{ lanes: Lanes, firstContext: ContextDependency } | null`  
    - **作用**: 存储依赖项（如 Context、事件监听）。当依赖变化时触发更新。

---

### 副作用与提交阶段
14. **`flags`** (旧版 `effectTag`)  
    - **类型**: `number` (位掩码)  
    - **作用**: *标记提交阶段（Commit Phase）需执行的副作用*。  
    - **常见位掩码**:  
      - `Placement` (0b10): 节点需插入 DOM  
      - `Update` (0b100): 节点需更新（如属性、文本）  
      - `Deletion` (0b1000): 节点需删除  
      - `Ref` (0b10000): 需更新 `ref`  
      - `Passive` (0b1000000000000): `useEffect` 回调

15. **`subtreeFlags`**  
    - **类型**: `number`  
    - **作用**: 子树中所有 Fiber 的 `flags` 合并，*优化提交阶段遍历*。

16. **`deletions`**  
    - **类型**: `Array<Fiber> | null`  
    - **作用**: 存储需删除的子节点（如子树被移除时）。

---

### 优先级与调度
17. **`lanes`**  
    - **类型**: `Lanes` (32 位整数)  
    - **作用**: *标记当前 Fiber 的更新优先级*（React 18 用 **Lane 模型** 替代过期时间）。  
    - **原理**: 每个位代表一个优先级（如用户交互 > 数据获取 > 背景任务）。

18. **`childLanes`**  
    - **类型**: `Lanes`  
    - **作用**: 子树中所有更新的最高优先级（用于调度中断恢复）。

---

### 双缓冲技术 (Work-in-Progress Tree)
19. **`alternate`**  
    - **类型**: `Fiber | null`  
    - **作用**: *指向另一个 Fiber 节点*，形成 **双缓冲树**。  
    - **核心流程**:  
      1. 当前树（Current）渲染到屏幕。  
      2. 更新时在备用树（Work-in-Progress）中计算变更。  
      3. 提交完成后，两棵树角色互换。  
    - **优势**: 避免渲染到一半时状态不一致，支持时间切片（Time Slicing）。

---

### 其他重要属性
20. **`ref`**  
    - **类型**: `any`  
    - **作用**: 存储 `ref` 对象（如 `useRef().current` 或 `createRef()`）。

21. **`mode`**  
    - **类型**: `number` (位掩码)  
    - **作用**: 标记*渲染模式（严格模式、并发模式等）*。

22. **`elementType`**  
    - **类型**: `any`  
    - **作用**: 原始组件类型（未被 `forwardRef`/`memo` 包装前的类型）。

23. **`effectList`** 链表指针  
    - **`firstEffect`/`lastEffect`**: 提交阶段遍历副作用链表的头尾指针。  
    - **`nextEffect`**: 副作用链表的下一个节点（提交阶段遍历用）。

24. **性能分析属性**（仅开发环境）  
    - `actualDuration`: 本次渲染耗时（含子树）  
    - `treeBaseDuration`: 估算的子树渲染时间  
    - `actualStartTime`: 渲染开始时间戳

---

## Fiber 节点生命周期中的变化
- **渲染阶段**:  
  - 创建/更新 `pendingProps`、`updateQueue`。  
  - 根据优先级（`lanes`）决定是否跳过渲染。  
- **提交阶段**:  
  - 合并 `flags` 生成 `effectList`。  
  - 执行 DOM 操作（插入/更新/删除）、调用生命周期（`useLayoutEffect`）。  
- **双缓冲切换**:  
  - 提交完成后，`work-in-progress` 树通过 `alternate` 替换 `current` 树。

---

## 示例：Fiber 节点结构简化版
```javascript
{
  tag: 5, // HostComponent (div)
  key: null,
  type: 'div',
  stateNode: <div>, // 真实 DOM
  return: Fiber, // 父节点
  child: Fiber, // 第一个子节点 (如文本节点)
  sibling: null, // 无兄弟节点
  index: 0,
  pendingProps: { children: 'Hello' },
  memoizedProps: { children: 'Hi' },
  memoizedState: null,
  updateQueue: null,
  flags: 2, // Placement
  lanes: 0b100, // 用户交互优先级
  alternate: Fiber, // 双缓冲树
  // ... 其他属性
}
```

---

## 关键设计思想
1. **增量渲染**: 通过 `lanes` 和双缓冲，拆分渲染任务，避免主线程阻塞。  
2. **可中断/恢复**: 调度器根据优先级中断低优先级更新。  
3. **副作用分离**: `flags` 将 DOM 操作集中到提交阶段，避免不一致状态。  
4. **状态持久化**: `memoizedState` 和 `updateQueue` 保存组件状态，支持异步渲染。

> **注意**：Fiber 是 React 内部实现细节，**不建议在应用代码中直接操作**。理解其原理有助于深入掌握 React 的并发特性（Concurrent Mode）和性能优化。实际开发中，应通过 Hooks 和组件 API 与 React 交互。
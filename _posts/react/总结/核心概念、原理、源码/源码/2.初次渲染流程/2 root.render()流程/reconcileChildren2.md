
## reconcileChildren

我来详细解释这段 [reconcileChildren](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L301-L309) 函数的代码，它位于 React 的 reconciler 中，*是 React 协调算法的关键部分*。

这段代码*是 React 协调算法的核心部分*，负责**比较新ReactElement与旧fiber，并决定如何更新 DOM**。

1. 参数解释：
   - [current](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L3079-L3079): *当前已存在的 Fiber 节点，首次渲染时为 null*
   - [workInProgress](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L3080-L3080): *正在处理的 Fiber 节点*，代表*未来要更新的 Fiber 节点*
   - `nextChildren`: **当前workInProgress fiber的子ReactElement。如，rootFiber的<App/> ReactElement；组件函数本次执行Component()，调用jsx()产生的新 children（ReactElement）**
   - [renderLanes](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L3082-L3082): *渲染优先级*

2. 两种情况的处理：
   - **首次渲染**（[current](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L3079-L3079) 为 null）：使用 [mountChildFibers](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L1367-L1367) 函数***创建新的子节点，无需比较，因为没有先前的节点。首次渲染，rootFiber已经有两个，不走这个，走下面更新渲染***
	   - ![[_posts/react/总结/核心概念、原理、源码/源码/2.初次渲染流程/2 root.render()流程/media/04e2f7f1431c7b7ea075cab9e9c3b075_MD5.webp]]
   - **更新渲染**（[current](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberBeginWork.new.js#L3079-L3079) 不为 null）：使用 [reconcileChildFibers](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L1369-L1369) 函数***比较新旧子节点，找出差异并应用必要的更新***

3. 优化策略：
   - 首次渲染时，由于不存在之前的节点，因此可以跳过比较逻辑，直接创建子节点
   - 更新时，会进行完整的协调算法，包括 key 比较、节点复用等

4. 重要性：
   - 这个函数是 React 高效更新 UI 的关键所在，它确保只更新实际发生变化的部分
   - 通过避免不必要的 DOM 操作，提高了应用性能

总之，[reconcileChildren](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L301-L309) 函数是 React 协调过程中的核心部分，负责决定如何从旧的子节点过渡到新的子节点，是 React 高效性的关键组成部分。

```javascript
export function reconcileChildren(
  current: Fiber | null,        // 当前屏幕显示的已存在的 Fiber 节点，如果是首次渲染则为 null
  workInProgress: Fiber,        // 正在处理的 Fiber 节点（将来的 Fiber）
  nextChildren: any,            // ！！！新ReactElement，如，组件函数本次执行Component()，调用jsx()产生的新 children（ReactElement）
  renderLanes: Lanes,           // 渲染优先级
) {
  // ！！！首次渲染。rootFiber特殊，即使是首次渲染（首次渲染已有两个rootFiber），也不走首次渲染，走下面更新渲染；除了rootFiber，其他fiber正常
  if (current === null) {
    // 如果是尚未渲染过的新组件，我们不会通过应用最小副作用来更新其子集
    // 相反，我们会在渲染前将它们全部添加到子节点中
    // 这意味着我们可以通过不追踪副作用来优化此次协调过程
    workInProgress.child = mountChildFibers(
      workInProgress,
      null,                     // 没有之前的兄弟节点
      nextChildren,             // ！！！新的子ReactElement
      renderLanes,              // 渲染优先级
    );
  } else {
    // ！！！更新渲染
    // 如果当前子节点与正在进行的工作相同，这意味着我们还没有开始对这些子节点进行任何工作
    // 因此，我们使用克隆算法创建所有当前子节点的副本
    
    // 如果我们有任何已经进行的工作，此时它是无效的，所以让我们丢弃它
    workInProgress.child = reconcileChildFibers(
      workInProgress,           // 当前正在处理的 Fiber 节点
      current.child,            // 之前渲染的子节点
      nextChildren,             // ！！！新的子ReactElement，如<App/>的ReactELement
      renderLanes,              // 渲染优先级
    );
  }
}
```


## reconcileChildFibers/mountChildFibers
reconcileChildFibers/mountChildFibers与ChildReconciler的关系：

```js
// 更新渲染，调用  
export const reconcileChildFibers = ChildReconciler(true);
// 首次渲染，调用
export const mountChildFibers = ChildReconciler(false);

```

---

### ChildReconciler

这段代码定义了一个叫 [ChildReconciler](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L259-L1365) 的工厂函数，*这是 React 协调算法的核心部分*。
*它创建了两个具体的函数：*[reconcileChildFibers](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L1369-L1369) 和 [mountChildFibers](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L1367-L1367)，用于*处理 React 组件的子节点协调（reconciliation）*。

`协调`是 React 的核心概念之一，指的是**比较虚拟 DOM 树的变化并更新真实 DOM 的过程**。

---

```javascript
// 这个包装函数存在是因为我们需要为每个路径克隆代码
// 以便通过早期分支单独优化每个路径。这需要一个编译器或者我们可以手动完成。
// 不需要这种分支的辅助函数位于此函数之外。
function ChildReconciler(shouldTrackSideEffects) {
```

这个函数接收一个布尔参数 `shouldTrackSideEffects`，**用于控制是否需要追踪副作用（side effects），首次渲染为false？？？，更新渲染为true**。`副作用`包括*插入、删除、移动节点*等操作。

---

以下是该函数中一些关键的辅助函数：

#### 1. 删除相关函数

```javascript
function deleteChild(returnFiber: Fiber, childToDelete: Fiber): void {
  if (!shouldTrackSideEffects) {
    // 首次渲染，什么都不做
    return;
  }
  const deletions = returnFiber.deletions;
  if (deletions === null) {
    returnFiber.deletions = [childToDelete];
    returnFiber.flags |= ChildDeletion;
  } else {
    deletions.push(childToDelete);
  }
}

function deleteRemainingChildren(
  returnFiber: Fiber,
  currentFirstChild: Fiber | null,
): null {
  if (!shouldTrackSideEffects) {
    // 首次渲染，什么都不做
    return null;
  }

  // 遍历并删除所有剩余的子节点
  let childToDelete = currentFirstChild;
  while (childToDelete !== null) {
    deleteChild(returnFiber, childToDelete);
    childToDelete = childToDelete.sibling;
  }
  return null;
}
```

#### 2. 创建和更新节点的函数

```javascript
function updateTextNode(
  returnFiber: Fiber,
  current: Fiber | null,
  textContent: string,
  lanes: Lanes,
) {
  if (current === null || current.tag !== HostText) {
    // 插入新文本节点
    const created = createFiberFromText(textContent, returnFiber.mode, lanes);
    created.return = returnFiber;
    return created;
  } else {
    // 更新现有文本节点
    const existing = useFiber(current, textContent);
    existing.return = returnFiber;
    return existing;
  }
}

function updateElement(
  returnFiber: Fiber,
  current: Fiber | null,
  element: ReactElement,
  lanes: Lanes,
): Fiber {
  // ...
}
```

#### 3. 协调不同类型的子节点

React 支持多种类型的子节点，包括：
- 单个 React 元素
- 文本节点
- 数组/迭代器
- Portal
- Fragment
- Lazy 组件

针对每种类型都有相应的处理函数：

```javascript
function reconcileSingleElement(
  returnFiber: Fiber,
  currentFirstChild: Fiber | null,
  element: ReactElement,
  lanes: Lanes,
): Fiber {
  // 处理单个 React 元素
  // ...
}

function reconcileSingleTextNode(
  returnFiber: Fiber,
  currentFirstChild: Fiber | null,
  textContent: string,
  lanes: Lanes,
): Fiber {
  // 处理单个文本节点
  // ...
}

function reconcileChildrenArray(
  returnFiber: Fiber,
  currentFirstChild: Fiber | null,
  newChildren: Array<*>,
  lanes: Lanes,
): Fiber | null {
  // 处理数组类型的子节点
  // 实现了复杂的协调算法，包括 key 比较、移动、插入和删除操作
  // ...
}
```

#### 4. 核心的 reconcileChildFibers 函数

```javascript
function reconcileChildFibers(
  returnFiber: Fiber,
  currentFirstChild: Fiber | null,
  newChild: any,
  lanes: Lanes,
): Fiber | null {
  // 这个函数不是递归的
  
  // 处理顶级未指定 key 的 Fragment，将其视为数组
  const isUnkeyedTopLevelFragment =
    typeof newChild === 'object' &&
    newChild !== null &&
    newChild.type === REACT_FRAGMENT_TYPE &&
    newChild.key === null;
  if (isUnkeyedTopLevelFragment) {
    newChild = newChild.props.children;
  }

  // 处理对象类型
  if (typeof newChild === 'object' && newChild !== null) {
    switch (newChild.$$typeof) {
      case REACT_ELEMENT_TYPE:
        return placeSingleChild(
          reconcileSingleElement(
            returnFiber,
            currentFirstChild,
            newChild,
            lanes,
          ),
        );
      case REACT_PORTAL_TYPE:
        // 处理 Portal
        // ...
      case REACT_LAZY_TYPE:
        // 处理懒加载组件
        // ...
    }

    if (isArray(newChild)) {
      // 处理数组
      return reconcileChildrenArray(...)
    }

    if (getIteratorFn(newChild)) {
      // 处理可迭代对象
      return reconcileChildrenIterator(...)
    }

    throwOnInvalidObjectType(returnFiber, newChild);
  }

  if (
    (typeof newChild === 'string' && newChild !== '') ||
    typeof newChild === 'number'
  ) {
    // 处理文本节点
    return placeSingleChild(
      reconcileSingleTextNode(...)
    );
  }

  // 其他情况都被视为空
  return deleteRemainingChildren(returnFiber, currentFirstChild);
}
```

#### 关键特性

1. **副作用追踪**：通过 [shouldTrackSideEffects](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L259-L1365) 参数*控制是否追踪副作用*，**首次挂载时不追踪，更新时才追踪**。

2. **高效协调算法**：对于数组类型的子节点，实现了 React 的经典 diff 算法，包括：
   - 使用 key 来识别节点的身份
   - 处理节点的插入、删除、移动
   - 针对常见场景进行优化

3. **多种节点类型支持**：能够处理各种类型的 React 子节点，包括元素、文本、Fragment、Portal 等。

这个协调器是 React 高效更新 UI 的关键部分，通过智能地比较前后两次的子节点，只更新真正变化的部分，避免不必要的 DOM 操作。

#### placeSingleChild
[placeSingleChild](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L358-L365) 函数的代码，它是 React 协调算法中处理单个子节点的关键部分。

##### 代码详解：
1. **函数目的**：这个函数用于*处理单个子节点的放置逻辑*。当 React 协调算法*发现需要插入一个新的单个子节点时*，会调用这个函数。

2. **参数说明**：
   - [newFiber](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L177-L177): 需要处理的新的 Fiber 节点

3. **条件判断**：
   - [shouldTrackSideEffects](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L259-L1365)：这是一个在 [ChildReconciler](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L259-L1365) 函数中定义的参数，用于控制是否追踪副作用（如节点插入、删除、移动等）
   - [newFiber.alternate === null](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L187-L187)：检查新 Fiber 是否没有对应的 alternate 节点，alternate 为 null 表示这是一个新创建的节点，而不是复用的节点

4. **Placement 标志**：当满足条件时，使用按位或运算（`|=`）将 [Placement](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiberFlags.js#L84-L84) 标志添加到 [newFiber.flags](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L212-L212) 中。Placement 标志告诉 React 这个节点需要被插入到 DOM 中。

5. **返回值**：返回处理后的 Fiber 节点。

##### 作用：
这个函数的**作用**是*在协调单个子节点时，判断是否需要将新节点标记为"插入"操作*。如果一个节点是新创建的（没有对应的 alternate），并且当前处于需要追踪副作用的模式下（更新阶段而非初始挂载），那么就需要设置 Placement 标志，这样在提交阶段 React 就知道需要将这个节点插入到实际的 DOM 中。

这是 React 高效更新 UI 的关键机制之一，通过准确地标记需要插入、更新或删除的节点，React 可以最小化 DOM 操作，提高渲染性能。

```javascript
function placeSingleChild(newFiber: Fiber): Fiber {
  // This is simpler for the single child case. We only need to do a
  // placement for inserting new children.
  // 对于单个子节点的情况，这更简单。我们只需要为插入新子节点执行放置操作。
  if (shouldTrackSideEffects && newFiber.alternate === null) {
    // 检查是否需要追踪副作用并且新 Fiber 没有对应的 alternate（即没有对应的当前 Fiber）
    // 这意味着这是一个新插入的节点，需要标记为 Placement 
    newFiber.flags |= Placement;  // 使用按位或运算设置 Placement 标志
  }
  return newFiber;  // 返回处理后的 Fiber 节点
}
```


### **reconcileChildFibers**

[reconcileChildFibers](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L1269-L1348) 函数的代码，它是 React 协调算法的核心部分。

#### 代码详解：

1. 参数解释：参考下面源码
2. **函数目的**：这个函数是 React 协调算法的核心，用于**比较新的wip的子ReactElement与旧的current.child/wip.alternate.child fiber（第一个子fiber），确定需要执行的最小更新集合**（如添加、删除、移动节点等）。

3. Fragment 处理：首先检查是否为未指定 key 的 Fragment，如果是，则直接获取其子元素进行处理。

4. **根据不同的ReactElement类型，走不同的处理流程**：
   - REACT_ELEMENT_TYPE：单个 React 元素，如 `<div />`
   - REACT_PORTAL_TYPE：Portal 类型元素
   - REACT_LAZY_TYPE：懒加载组件，需要初始化后再处理
   - 数组：多个子元素，需要进行 diff 算法
   - 迭代器：可迭代对象，如 Map、Set 等

4. **基本类型处理**：*字符串和数字会被转换为文本节点*。

5. 兜底处理：如果新子节点为 null 或其他无效值，则删除所有现有的子节点。

#### 关键特点：

- 非递归：此函数本身不是递归的，但会调用其他函数来处理子树
- 类型判断：通过 [$$typeof](file:///Users/ll/Desktop/资料/编程/仓库/react/react-18.2.0/packages/react/src/ReactElement.js#L150-L150) 属性判断 React 元素的类型
- 性能优化：通过 [placeSingleChild](file:///Users/ll/Desktop/资料/编程/仓库/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L358-L365) 等函数优化单子节点的处理
- 错误处理：对无效的子节点类型给出明确的错误提示

这个函数是 React 高效更新 UI 的关键部分，它实现了 React 的 diff 算法，确保只对必要的部分进行 DOM 操作。

```javascript

function reconcileChildFibers(
  returnFiber: Fiber,              // wip Fiber，要构建fiber的父级 Fiber 节点
  currentFirstChild: Fiber | null, // wip.alternate.child/current.child，当前第一个子 Fiber 节点，diff时使用
  newChild: any,                   // wip的子ReactElement，新的 React 元素，新的子节点（可能是 React 元素、字符串、数组等）
  lanes: Lanes,                    // 优先级相关的 lanes
): Fiber | null {                  // ！！！新构建的第一个子fiber（新创建的fiber，或复用的fiber）

  
  const isUnkeyedTopLevelFragment =
    typeof newChild === 'object' &&
    newChild !== null &&
    newChild.type === REACT_FRAGMENT_TYPE &&
    newChild.key === null;
  if (isUnkeyedTopLevelFragment) {
    // 如果是顶层未指定 key 的 Fragment，将其子元素作为普通数组处理
    newChild = newChild.props.children;
  }

  // Handle object types - 处理对象类型
  if (typeof newChild === 'object' && newChild !== null) {
    switch (newChild.$$typeof) {
      case REACT_ELEMENT_TYPE:
        // ！！！处理单个 React 元素，如首次渲染的根 React Element <App/>
        return placeSingleChild(
          reconcileSingleElement(
            returnFiber,
            currentFirstChild,
            newChild,
            lanes,
          ),
        );
      case REACT_PORTAL_TYPE:
        // 处理 Portal 元素
        return placeSingleChild(
          reconcileSinglePortal(
            returnFiber,
            currentFirstChild,
            newChild,
            lanes,
          ),
        );
      case REACT_LAZY_TYPE:
        // 处理懒加载组件（lazy/Suspense）
        const payload = newChild._payload;
        const init = newChild._init;
        // TODO: This function is supposed to be non-recursive.
        return reconcileChildFibers(
          returnFiber,
          currentFirstChild,
          init(payload),
          lanes,
        );
    }

    // 检查是否为数组
    if (isArray(newChild)) {
      // 处理数组类型的子节点
      return reconcileChildrenArray(
        returnFiber,
        currentFirstChild,
        newChild,
        lanes,
      );
    }

    // 检查是否为可迭代对象（如 Map、Set 等）
    if (getIteratorFn(newChild)) {
      // 处理迭代器类型的子节点
      return reconcileChildrenIterator(
        returnFiber,
        currentFirstChild,
        newChild,
        lanes,
      );
    }

    // 如果是对象类型但不是以上任何一种，抛出错误
    throwOnInvalidObjectType(returnFiber, newChild);
  }

  // 处理字符串和数字类型（将它们转换为文本节点）
  if (
    (typeof newChild === 'string' && newChild !== '') ||
    typeof newChild === 'number'
  ) {
    return placeSingleChild(
      reconcileSingleTextNode(
        returnFiber,
        currentFirstChild,
        '' + newChild,
        lanes,
      ),
    );
  }

  // 开发环境下处理函数类型的错误情况
  if (__DEV__) {
    if (typeof newChild === 'function') {
      warnOnFunctionType(returnFiber);
    }
  }

  // 剩余的所有情况都被当作空处理，删除所有现有的子节点
  return deleteRemainingChildren(returnFiber, currentFirstChild);
}
```

### reconcileSingleElement

[reconcileSingleElement](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L1129-L1204) 函数的代码，它是 React 协调算法中**处理单个 ReactElement 元素**的核心部分。

#### 代码详解：
1. 参数解释：参考下面源码

2. **函数目的**：这个函数*处理单个 ReactElement 的协调（如首次渲染的`<App/>`），尝试在现有fiber中找到匹配的fiber进行复用，如果没有找到则创建新fiber*。

3. Key 匹配：首先通过*比较 key 值来寻找可复用的节点*，这是 React diff 算法的关键。

4. 类型匹配：找到 key 匹配的节点后，*进一步检查元素类型是否相同，以确定是否可以复用节点*。

5. Fragment 特殊处理：Fragment 类型需要特别处理，因为它不会创建实际的 DOM 节点。

6. 懒加载组件处理：对于懒加载组件，需要解析其实际类型并与现有节点比较。

7. 节点复用 vs 创建：
   - 如果找到匹配的节点，使用 [useFiber](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactChildFiber.new.js#L335-L340) *复用现有节点并更新 props*
   - 如果没找到匹配节点，则使用 [createFiberFromElement](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L436-L459) 或 [createFiberFromFragment](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L474-L479) 创建新节点

8. 删除操作：对于不匹配的节点或其后续节点，会执行删除操作。

这个函数体现了 React 协调算法的核心思想：通过 key 和类型匹配来复用尽可能多的现有节点，减少不必要的创建和销毁操作，从而提升性能。


```javascript
function reconcileSingleElement(
    returnFiber: Fiber,               // wip Fiber，要构建fiber的父级 Fiber 节点
    currentFirstChild: Fiber | null,  // wip.alternate.child/current.child，当前第一个子 Fiber 节点，diff时使用
    element: ReactElement,            // wip的子ReactElement，新的 React 元素 
    lanes: Lanes,                     // 优先级相关的 lanes
  ): Fiber {                          // 新构建的fiber（新创建的fiber，或复用的fiber）
  
    // 获取新元素的 key
    const key = element.key;
    // 从current.child（workInProgress对应的current的第一个子节点）开始遍历
    let child = currentFirstChild;
    // 遍历现有的子节点链表（这是更新渲染的流程，因为首次渲染child、currentFirstChild为null）
    while (child !== null) {
      // TODO: If key === null and child.key === null, then this only applies to
      // the first item in the list.
      // ！！！比较 key 是否匹配，这是 React diff 算法的关键一步
      if (child.key === key) {
        // 获取新元素的类型
        const elementType = element.type;
        // 如果是 Fragment 类型
        if (elementType === REACT_FRAGMENT_TYPE) {
          // 检查现有节点是否也是 Fragment 类型
          if (child.tag === Fragment) {
            // 删除该节点之后的所有兄弟节点
            deleteRemainingChildren(returnFiber, child.sibling);
            // 复用现有节点，只更新 props
            const existing = useFiber(child, element.props.children);
            existing.return = returnFiber;
            // 开发环境下添加调试信息
            if (__DEV__) {
              existing._debugSource = element._source;
              existing._debugOwner = element._owner;
            }
            return existing;
          }
        } else {
          // 非 Fragment 类型的元素
          if (
            // ！！！检查元素类型是否相同
            child.elementType === elementType ||
            // 在开发环境下，为了热重载功能做额外检查
            (__DEV__
              ? isCompatibleFamilyForHotReloading(child, element)
              : false) ||
            // 对于懒加载组件，需要解析实际类型进行比较
            (typeof elementType === 'object' &&
              elementType !== null &&
              elementType.$$typeof === REACT_LAZY_TYPE &&
              resolveLazy(elementType) === child.type)
          ) {
            // 匹配成功，删除该节点之后的所有兄弟节点
            deleteRemainingChildren(returnFiber, child.sibling);
            // ！！！复用现有节点，更新 props
            const existing = useFiber(child, element.props);
            // 处理 ref 更新
            existing.ref = coerceRef(returnFiber, child, element);
            existing.return = returnFiber;
            // 开发环境下添加调试信息
            if (__DEV__) {
              existing._debugSource = element._source;
              existing._debugOwner = element._owner;
            }
            return existing;
          }
        }
        // 没有匹配成功，删除这个节点及其所有兄弟节点
        // Didn't match.
        deleteRemainingChildren(returnFiber, child);
        break;
      } else {
        // key 不匹配，删除当前节点
        deleteChild(returnFiber, child);
      }
      // 移动到下一个兄弟节点
      child = child.sibling;
    }

    // ！！！如果遍历完current上的兄弟节点都没有找到匹配项，创建一个新的 Fiber 节点
    if (element.type === REACT_FRAGMENT_TYPE) {
      // 如果是 Fragment 类型，创建 Fragment Fiber
      const created = createFiberFromFragment(
        element.props.children,  // 子元素
        returnFiber.mode,        // 渲染模式
        lanes,                   // 优先级
        element.key,             // key
      );
      created.return = returnFiber;
      return created;
    } else {
      // ！！！普通元素类型，创建 ReactElement 对应的 Fiber
      const created = createFiberFromElement(element, returnFiber.mode, lanes);
      // 处理 ref
      created.ref = coerceRef(returnFiber, currentFirstChild, element);
      created.return = returnFiber;
      return created;
    }
  }
```

### reconcileChildrenArray
参考：[[reconcileChildrenArray]]

### createFiberFromElement
我来详细解释这段 [createFiberFromElement](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L604-L629) 函数的代码，它是 React 协调算法中创建 Fiber 节点的重要部分。

#### 代码详解：

1. **函数目的**：这个函数的主要作用是**从一个 React 元素（ReactElement）创建一个对应的 Fiber 节点**，这是 React 协调算法中的关键步骤。

2. 参数说明：
   - [element](file:///Users/ll/Desktop/资料/编程/仓库/react/react-18.2.0/packages/react/src/ReactElement.js#L148-L160): *要转换为 Fiber 节点的 React 元素*
   - [mode](file:///Users/ll/Desktop/资料/编程/仓库/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L144-L144): 渲染模式，*决定 Fiber 如何处理并发*
   - [lanes](file:///Users/ll/Desktop/资料/编程/仓库/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L151-L151): 优先级车道，用于*确定更新的优先级*

1. 开发环境处理：在开发模式下，保存元素的所有者信息用于调试目的。

2. 信息提取：从 React 元素中提取关键信息（type、key、props）。

3. Fiber 创建：调用 [createFiberFromTypeAndProps](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L530-L592) 函数实际创建 Fiber 节点。

4. 调试信息：在开发模式下，添加调试相关的信息以帮助开发者定位问题。

这个函数体现了 React 的设计理念：将虚拟 DOM 元素转换为内部的 Fiber 数据结构，以便进行高效的协调和更新。Fiber 节点包含了足够的信息来执行更新、渲染和回收操作。

```javascript
// 导出创建 Fiber 节点的函数，用于从 React 元素创建对应的 Fiber 对象
export function createFiberFromElement(
  element: ReactElement,  // 输入的 React 元素，包含组件类型、props、key 等信息
  mode: TypeOfMode,      // 渲染模式（如 ConcurrentMode、BatchedMode 等）
  lanes: Lanes,          // 优先级相关的 lanes，用于确定更新优先级
): Fiber {

  // 初始化 owner 为 null，默认情况下没有所有者信息
  let owner = null;
  // 在开发环境下，获取元素的所有者信息，用于调试
  if (__DEV__) {
    owner = element._owner;  // 保存创建该元素的组件实例，有助于错误追踪
  }
  // 提取元素的类型（如 'div', 'span' 或自定义组件）
  const type = element.type;
  // 提取元素的 key 属性，用于 diff 算法中的节点匹配
  const key = element.key;
  // 提取元素的 props，包含传递给组件的所有属性
  const pendingProps = element.props;
  // 使用提取的信息创建 Fiber 节点
  const fiber = createFiberFromTypeAndProps(
    type,     // 组件类型
    key,      // 元素的 key
    pendingProps,  // 组件的 props
    owner,    // 元素的所有者（用于调试）
    mode,     // 渲染模式
    lanes,    // 优先级车道
  );
  // 在开发环境下，添加调试信息
  if (__DEV__) {
    fiber._debugSource = element._source;  // 记录元素的源码位置，用于错误定位
    fiber._debugOwner = element._owner;    // 记录元素的所有者，用于调试
  }
  // 返回创建好的 Fiber 节点
  return fiber;
}
```

### createFiberFromTypeAndProps

我将详细解释这段 [createFiberFromTypeAndProps](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L530-L592) 函数的代码，这是 React 协调算法中创建 Fiber 节点的核心部分。

#### 代码详解：
1. **函数目的**：*根据组件类型和属性创建合适的 Fiber 节点*，这是 React 协调算法中的关键步骤。

2. 类型判断：函数通过检查 [type](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L166-L166) 的类型来确定 Fiber 的标签（tag）：
   - 函数类型：分为类组件和函数组件
   - 字符串类型：原生 DOM 元素
   - 特殊 React 类型：如 Fragment、Suspense、Context 等

1. 特殊处理：某些类型（如 Fragment、Suspense）会直接创建对应的 Fiber 并返回，而不是继续往下执行。

2. 错误处理：当遇到无效的组件类型时，会抛出清晰的错误信息，帮助开发者定位问题。

3. Fiber 创建：使用 [createFiber](file:///Users/ll/Desktop/%E8%B5%84%E6%96%99/%E7%BC%96%E7%A8%8B/%E4%BB%93%E5%BA%93/react/react-18.2.0/packages/react-reconciler/src/ReactFiber.new.js#L468-L488) 函数创建基本的 Fiber 节点，并设置相应的属性。

4. 开发环境增强：在开发环境下，添加调试信息以帮助错误追踪。

这个函数是 React 渲染机制的核心部分，它确保了每种类型的组件都能被正确地映射到相应的 Fiber 标签，从而使得 React 能够正确地协调和更新 UI。

```javascript
export function createFiberFromTypeAndProps(
  type: any, // React$ElementType - 组件类型，如 'div'、MyComponent 等
  key: null | string, // 元素的 key 属性，用于 diff 算法
  pendingProps: any, // 组件的 props
  owner: null | Fiber, // 创建该元素的组件实例（用于调试）
  mode: TypeOfMode, // 渲染模式（如 ConcurrentMode、BatchedMode 等）
  lanes: Lanes, // 优先级相关的 lanes
): Fiber {

  // 初始化 Fiber 标签为不确定组件类型
  let fiberTag = IndeterminateComponent;
  // 解析后的类型，用于热重载等场景
  let resolvedType = type;
  
  // ！！！根据组件类型type确定 Fiber 标签tag
  // ！！！函数类型组件
  if (typeof type === 'function') {
    // ！！！类组件
    if (shouldConstruct(type)) {
      // 如果函数具有构造函数特征（类组件），则标记为 ClassComponent
      fiberTag = ClassComponent;
      if (__DEV__) {
        // 开发环境下进行热重载处理
        resolvedType = resolveClassForHotReloading(resolvedType);
      }
    } else {
      // ！！！函数组件
      if (__DEV__) {
        // 开发环境下进行热重载处理
        resolvedType = resolveFunctionForHotReloading(resolvedType);
      }
    }
  } else if (typeof type === 'string') {
    // 原生 DOM 组件
    fiberTag = HostComponent;
  } else {
    // 处理特殊 React 组件类型
    getTag: switch (type) {
      case REACT_FRAGMENT_TYPE:
        // Fragment 类型，直接创建 Fragment Fiber 并返回
        return createFiberFromFragment(pendingProps.children, mode, lanes, key);
      case REACT_STRICT_MODE_TYPE:
        // 严格模式类型
        fiberTag = Mode;
        mode |= StrictLegacyMode;
        if (enableStrictEffects && (mode & ConcurrentMode) !== NoMode) {
          // 严格模式效果应该永远不会在 legacy 根节点上运行
          mode |= StrictEffectsMode;
        }
        break;
      case REACT_PROFILER_TYPE:
        // 性能分析器类型
        return createFiberFromProfiler(pendingProps, mode, lanes, key);
      case REACT_SUSPENSE_TYPE:
        // Suspense 类型
        return createFiberFromSuspense(pendingProps, mode, lanes, key);
      case REACT_SUSPENSE_LIST_TYPE:
        // Suspense List 类型
        return createFiberFromSuspenseList(pendingProps, mode, lanes, key);
      case REACT_OFFSCREEN_TYPE:
        // 离屏类型
        return createFiberFromOffscreen(pendingProps, mode, lanes, key);
      case REACT_LEGACY_HIDDEN_TYPE:
        if (enableLegacyHidden) {
          // Legacy Hidden 类型
          return createFiberFromLegacyHidden(pendingProps, mode, lanes, key);
        }
      // 注意：这里没有 break，继续执行到下一个 case
      case REACT_SCOPE_TYPE:
        if (enableScopeAPI) {
          // Scope 类型
          return createFiberFromScope(type, pendingProps, mode, lanes, key);
        }
      // 注意：这里没有 break，继续执行到下一个 case
      case REACT_CACHE_TYPE:
        if (enableCache) {
          // 缓存类型
          return createFiberFromCache(pendingProps, mode, lanes, key);
        }
      // 注意：这里没有 break，继续执行到下一个 case
      case REACT_TRACING_MARKER_TYPE:
        if (enableTransitionTracing) {
          // 追踪标记类型
          return createFiberFromTracingMarker(pendingProps, mode, lanes, key);
        }
      // 注意：这里没有 break，继续执行到下一个 case
      case REACT_DEBUG_TRACING_MODE_TYPE:
        if (enableDebugTracing) {
          // 调试追踪模式
          fiberTag = Mode;
          mode |= DebugTracingMode;
          break;
        }
      // 注意：这里没有 break，继续执行到下一个 case
      default: {
        // 处理对象类型的组件（如 Context、ForwardRef 等）
        if (typeof type === 'object' && type !== null) {
          switch (type.$$typeof) {
            case REACT_PROVIDER_TYPE:
              fiberTag = ContextProvider;
              break getTag; // 跳出外层 switch
            case REACT_CONTEXT_TYPE:
              // Context Consumer
              fiberTag = ContextConsumer;
              break getTag;
            case REACT_FORWARD_REF_TYPE:
              fiberTag = ForwardRef;
              if (__DEV__) {
                resolvedType = resolveForwardRefForHotReloading(resolvedType);
              }
              break getTag;
            case REACT_MEMO_TYPE:
              fiberTag = MemoComponent;
              break getTag;
            case REACT_LAZY_TYPE:
              fiberTag = LazyComponent;
              resolvedType = null; // 懒加载组件的类型暂时未知
              break getTag;
          }
        }
        // 如果到这里还没匹配到有效类型，则抛出错误
        let info = '';
        if (__DEV__) {
          if (
            type === undefined ||
            (typeof type === 'object' &&
              type !== null &&
              Object.keys(type).length === 0)
          ) {
            info +=
              ' You likely forgot to export your component from the file ' +
              "it's defined in, or you might have mixed up default and " +
              'named imports.';
          }
          const ownerName = owner ? getComponentNameFromFiber(owner) : null;
          if (ownerName) {
            info += '\n\nCheck the render method of `' + ownerName + '`.';
          }
        }

        throw new Error(
          'Element type is invalid: expected a string (for built-in ' +
            'components) or a class/function (for composite components) ' +
            `but got: ${type == null ? type : typeof type}.${info}`,
        );
      }
    }
  }

  // 创建基本的 Fiber 节点
  const fiber = createFiber(fiberTag, pendingProps, key, mode);
  // 设置元素类型和解析后的类型
  fiber.elementType = type;
  fiber.type = resolvedType;
  // 设置优先级
  fiber.lanes = lanes;

  // 开发环境下设置调试信息
  if (__DEV__) {
    fiber._debugOwner = owner;
  }

  // 返回创建的 Fiber 节点
  return fiber;
}
```

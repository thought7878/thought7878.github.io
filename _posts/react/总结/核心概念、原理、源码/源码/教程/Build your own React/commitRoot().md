选中代码定义了 `commitRoot` 函数，其**主要作用**是*将整个工作中的 Fiber 树（`wipRoot`）的更改提交到实际的 DOM 中*。

完整代码：

```js

```

下面详细解释代码逻辑：

### 函数定义

```javascript
function commitRoot() {
```

定义 `commitRoot` 函数，该函数会在所有工作单元处理完成后被调用，负责将 Fiber 树的变更应用到 DOM 上。

### 删除操作

```javascript
  // 遍历 deletions 数组，删除所有需要删除的节点。
  // 当我们提交对 DOM 的更改时，我们也使用deletions数组中的 fiber
  deletions.forEach(commitWork);
```

- `deletions` 是一个数组，在 `reconcileChildren` 函数中，当发现需要从 DOM 中移除的旧 Fiber 节点时，会将这些 Fiber 节点添加到 `deletions` 数组中。
- `forEach(commitWork)` 会遍历 `deletions` 数组，对每个需要删除的 Fiber 节点调用 `commitWork` 函数，`commitWork` 函数会根据 Fiber 节点的 `effectTag` 为 `DELETION` 来执行实际的 DOM 删除操作。

### 提交子节点更改

```javascript
  commitWork(wipRoot.child);
```

- `wipRoot` 代表工作中的 Fiber 树的根节点。
- `commitWork(wipRoot.child)` 会递归调用 `commitWork` 函数，从根节点的第一个子节点开始，将整个 Fiber 树的更改提交到 DOM 中。`commitWork` 函数会根据每个 Fiber 节点的 `effectTag`（如 `PLACEMENT`、`UPDATE`、`DELETION`）来执行相应的 DOM 操作，如添加、更新或删除节点。

### 更新当前 Fiber 树

```javascript
  currentRoot = wipRoot;
  wipRoot = null;
```

- `currentRoot` 表示最后一次成功提交到 DOM 的 Fiber 树的根节点。将 `wipRoot` 赋值给 `currentRoot`，*意味着本次工作中的 Fiber 树已经成功提交到 DOM，成为当前生效的 Fiber 树*。
- 将 `wipRoot` 置为 `null`，表示当前没有正在进行的工作中的 Fiber 树，为下一次渲染工作做准备。

### 总结

`commitRoot` 函数的核心任务是将工作中的 Fiber 树的更改应用到实际的 DOM 中，包括删除需要移除的节点、添加新节点、更新现有节点，最后更新当前生效的 Fiber 树。
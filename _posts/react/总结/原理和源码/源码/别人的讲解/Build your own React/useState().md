`useState` 钩子函数，它是 React 中用于*在函数组件里添加状态*的核心功能。

下面逐步解释代码逻辑：

### 函数定义与参数

```javascript
function useState(initial) {
```

定义 `useState` 函数，接收一个参数 `initial`，它是*状态的初始值*。

### 获取旧的 Hook 信息

```javascript
  const oldHook =
    wipFiber.alternate &&
    wipFiber.alternate.hooks &&
    wipFiber.alternate.hooks[hookIndex];
```

- `wipFiber` 是*当前正在处理的工作中的 Fiber 节点*。
- `wipFiber.alternate` 指向*旧的 Fiber 节点*。
- *通过 `hookIndex` 从旧 Fiber 节点的 `hooks` 数组中获取对应的旧 Hook*。若旧 Hook 存在则获取，否则为 `null`。

### 初始化新的 Hook

```javascript
  const hook = {
    state: oldHook ? oldHook.state : initial,
    queue: [],
  };
```

*创建一个新的 Hook 对象，包含两个属性：*

- `state`：若旧 Hook 存在，就使用旧 Hook 的状态值；若不存在，则使用传入的初始值 `initial`。
- `queue`：用于*存储状态更新操作的队列*，初始为空数组。

### 处理旧的状态更新操作

```javascript
  const actions = oldHook ? oldHook.queue : [];
  actions.forEach((action) => {
    hook.state = action(hook.state);
  });
```

- 若旧 Hook 存在，获取其 `queue` 中的所有状态更新操作；若不存在，则使用空数组。
- 遍历 `actions` 数组，*依次执行每个更新操作，更新新 Hook 的 `state` 值*。

### 定义 `setState` 函数

```javascript
  const setState = (action) => {
    // 添加到hook的队列中
    hook.queue.push(action);
    // 重新渲染
    // 做了一些类似于 render 函数中做的事情，将一个新的工作进行中的根（wipRoot）设置为下一个工作单元，以便工作循环（workLoop）可以开始一个新的渲染阶段。
    wipRoot = {
      dom: currentRoot.dom,
      props: currentRoot.props,
      alternate: currentRoot,
    };
    nextUnitOfWork = wipRoot;
    deletions = [];
  };
```

- `setState` 函数接收一个 `action` 参数，*它可以是一个新的状态值或者一个返回新状态值的函数*。
- 将 `action` 添加到当前 Hook 的 `queue` 队列中。
- 重置 `wipRoot` 为一个新的工作中的根节点，引用当前的 `currentRoot`。
- 将 `nextUnitOfWork` 设置为 `wipRoot`，触发新一轮的渲染工作循环。
- 清空 `deletions` 数组。

### 更新 Hook 数组与索引并返回状态和 `setState` 函数

```javascript
  wipFiber.hooks.push(hook);
  hookIndex++;
  return [hook.state, setState];
}
```

- 将新创建的 Hook 对象添加到 `wipFiber` 的 `hooks` 数组中。
- `hookIndex` 加 1，为下一个 `useState` 调用做准备。
- 返回一个数组，包含当前的状态值 `hook.state` 和 `setState` 函数，供组件使用。

### 总结

`useState` 函数实现了在函数组件中管理状态的功能。它能保留组件上一次渲染时的状态，处理状态更新操作，并且提供 `setState` 函数来触发状态更新和重新渲染。
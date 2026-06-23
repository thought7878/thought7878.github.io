https://github.com/mui/base-ui/issues/5103

## Bug 详解：NavigationMenu 配合 Suspense 子组件导致无限更新循环

### 问题现象

当 `NavigationMenu.Content` 内部包含一个**首次渲染会触发 Suspense** 的子组件（如 `React.lazy` 或会 suspend 的数据查询），且最近的 `Suspense` 边界在 `NavigationMenu` **上方**时，鼠标悬停触发打开弹窗会抛出：

```
Maximum update depth exceeded
```

### 触发条件

1. 多个 `NavigationMenu.Item`（单个不会触发）
2. `NavigationMenu.Content` 内有会 suspend 的子组件
3. 最近的 `<Suspense>` 在 `NavigationMenu.Root` 的外层（而不是包裹在 `Content` 内部）
4. 首次 hover 触发打开弹窗时

### 根本原因

问题出在 **CompositeList 的 `flushSync` 机制与 React Suspense 的交互**上。调用链如下：

```
handleMouseMove (hover 触发)
  → NavigationMenuTrigger.handleOpenChange
    → ReactDOM.flushSync          ← 同步刷新
      → useCompositeListItem      ← 注册/更新列表项
        → CompositeList dispatchSetState  ← 触发状态更新
          → 再次触发渲染 → 再次 suspend → 再次触发 flushSync → 循环
```

具体过程：

1. **Hover 触发打开**：鼠标移入 trigger，调用 `handleOpenChange`，内部使用 `ReactDOM.flushSync` 强制同步更新 DOM（这是为了确保弹窗位置计算正确）
2. **CompositeList 注册**：`flushSync` 期间，`useCompositeListItem` 尝试将当前项注册到 composite list 中，调用 `dispatchSetState`
3. **Suspense 中断**：子组件 suspend，React 向上查找 `Suspense` 边界，找到外层的 `<Suspense>`，整个 `NavigationMenu` 被卸载并显示 fallback
4. **Suspense 解析后重新挂载**：子组件加载完成，`NavigationMenu` 重新挂载，hover 状态仍在，再次触发打开逻辑
5. **无限循环**：重新挂载 → 再次 `flushSync` → 再次注册 → 再次 suspend → 循环往复

**关键点**：`flushSync` 要求同步完成更新，但 Suspense 打断了这个过程。每次 suspend 都会导致整个 NavigationMenu 被卸载重建，重建后又因为 hover 状态立即触发同样的流程。

### 为什么单个 item 不会触发

多个 `NavigationMenu.Item` 时，CompositeList 需要协调多个列表项的位置和索引，每次注册都会触发 `dispatchSetState` 更新整个列表。单个 item 时没有这种协调开销，不会形成循环。

### 临时解决方案

在 `NavigationMenu.Content` 内部再加一层 `<Suspense>` 边界，让 suspend 在内部消化，不影响外层：

```tsx
<NavigationMenu.Content>
  <React.Suspense fallback={<div>loading...</div>}>
    <SuspendingContent id={id} />
  </React.Suspense>
</NavigationMenu.Content>
```

这样 suspend 时只会显示内层的 fallback，不会导致整个 `NavigationMenu` 被卸载，从而打断循环。

### 相关修复

这个问题与 [#3700](https://github.com/mui/base-ui/pull/3700) 相关，该 PR 曾修复过类似的 `Maximum update depth exceeded` 问题，但显然没有完全覆盖这种场景（Suspense 边界在外层的情况）。
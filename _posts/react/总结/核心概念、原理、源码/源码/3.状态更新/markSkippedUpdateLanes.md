- **作用**：*记录在本次渲染周期中被跳过的更新优先级*。*当一个更新被跳过时，将其lane记录到workInProgressRootSkippedLanes中，以便在后续渲染周期中能够正确处理这些被跳过的更新*。
- **实现**：通过mergeLanes函数合并新旧跳过lanes，维护到`workInProgressRootSkippedLanes`全局变量中


## 源码
`packages/react-reconciler/src/ReactFiberWorkLoop.old.js`

```ts
/**
 * 标记跳过的更新优先级（lanes）
 * 当一个更新被跳过时，将其lane记录到workInProgressRootSkippedLanes中，
 * 以便在后续渲染周期中能够正确处理这些被跳过的更新
 *
 * @param lane - 被跳过的更新的通道(Lane)或通道集合(Lanes)，用于标识被跳过的更新优先级
 * @returns 无返回值
 */
export function markSkippedUpdateLanes(lane: Lane | Lanes): void {
  // 将传入的lane与当前已跳过的优先级进行合并，并更新 workInProgressRootSkippedLanes 的值
  workInProgressRootSkippedLanes = mergeLanes(
    lane,
    workInProgressRootSkippedLanes,
  );
}
```
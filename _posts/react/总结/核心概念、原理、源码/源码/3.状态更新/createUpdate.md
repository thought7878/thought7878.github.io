
## 源码
`packages/react-reconciler/src/ReactFiberClassUpdateQueue.old.js`

```ts
/**
 *
 * 创建一个新的更新对象的函数
 * @param {number} eventTime - 事件发生的时间戳，用于优先级计算和调度
 * @param {Lane} lane - 更新的优先级车道，决定更新的执行顺序
 * @returns {Update<*>} 返回一个具有默认值的更新对象
 */
export function createUpdate(eventTime: number, lane: Lane): Update<*> {
  const update: Update<*> = {
    eventTime,
    lane,

    tag: UpdateState,
    payload: null,
    callback: null,

    next: null,
  };
  return update;
}
```
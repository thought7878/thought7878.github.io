

`packages/scheduler/src/SchedulerPriorities.js`

```ts
// 定义调度器中使用的优先级级别类型，包括从无优先级到立即执行优先级的不同等级
export type PriorityLevel = 0 | 1 | 2 | 3 | 4 | 5;

// TODO: Use symbols?
// 调度器优先级常量定义，用于标识不同任务的执行优先级
export const NoPriority = 0;        // 无优先级，通常表示不需要特殊处理的任务
export const ImmediatePriority = 1; // 立即执行优先级，最高优先级，需要立即处理（同步更新）
export const UserBlockingPriority = 2; // 用户阻塞优先级，高优先级，影响用户体验的任务（用户触发的更新）
export const NormalPriority = 3;    // 普通优先级，常规任务的默认优先级（从服务端加载完数据后的更新）
export const LowPriority = 4;       // 低优先级，可以延迟执行的次要任务（Suspense）
export const IdlePriority = 5;      // 空闲优先级，最低优先级，在空闲时才执行的任务
```
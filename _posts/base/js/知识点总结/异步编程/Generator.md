
`Generator（生成器）` 是 ES6 引入的**特殊函数类型**，它*打破了传统函数“一气呵成”的执行模型，允许函数在执行过程中**暂停与恢复**，并能在暂停期间与外部进行**双向数据通信***。
它是 *JS 协程（Coroutine）的**落地实现**，也是 `async/await` 的**底层基石***。

---

## 一、基础语法与核心特征

```js
// 1. 声明：function 与函数名之间加 *
function* gen() {
  console.log('start');
  const a = yield 1;       // 暂停，产出 1
  console.log('received:', a);
  const b = yield 2;       // 暂停，产出 2
  return 3;                // 结束
}

// 2. 调用：不执行函数体，返回 Generator 对象
const g = gen();
console.log(g); // Generator {<suspended>}

// 3. 驱动：通过 next() 逐步执行
console.log(g.next());      // { value: 1, done: false } 输出: start
console.log(g.next('A'));   // { value: 2, done: false } 输出: received: A
console.log(g.next('B'));   // { value: 3, done: true }
console.log(g.next());      // { value: undefined, done: true }
```

### **三大特征**

| 特征       | 说明                                                 |
| -------- | -------------------------------------------------- |
| **惰性执行** | 调用 `gen()` 不执行内部代码，仅返回迭代器对象                        |
| **分段暂停** | 遇到 `yield` 或 `return` 时暂停，保存完整执行上下文（局部变量、作用域、执行位置） |
| **双向通信** | `yield` 既能向外产出值，又能通过 `next(arg)` 接收外部传入的值          |

---

## 二、底层运行机制：状态机与上下文保存

JS 引擎在编译 Generator 时，会将其转换为一个**隐式状态机**。**每次调用 `next()` 时：**
1. *恢复上次暂停的调用栈与词法环境*
2. *执行到下一个 `yield` 或 `return`*
3. *将当前状态序列化保存，挂起执行线程*
4. 返回 `{ value, done }`

```js
// 伪代码示意引擎转换逻辑
function* gen() { yield 1; yield 2; }
// ≈ 编译为
function gen() {
  let state = 0;
  return {
    next() {
      switch(state) {
        case 0: state = 1; return { value: 1, done: false };
        case 1: state = 2; return { value: 2, done: false };
        case 2: return { value: undefined, done: true };
      }
    }
  };
}
```
> 💡 正因上下文被完整保存，Generator 内的局部变量**不会在暂停时被垃圾回收**，直到 `done: true` 或迭代器失去引用。

---

## 三、核心 API 与双向通信

### 1. `next(value)`
- 恢复执行，直到下一个 `yield`/`return`
- **传入的 `value` 会作为上一个 `yield` 表达式的返回值**
- ⚠️ **第一次 `next()` 传参无效**（因为此时还没执行到任何 `yield`，无“上一个”可赋值）

```js
function* calc() {
  const x = yield 10;
  const y = yield x * 2;
  return y + 5;
}
const g = calc();
g.next();       // { value: 10, done: false }
g.next(3);      // x = 3 → { value: 6, done: false }
g.next(4);      // y = 4 → { value: 9, done: true }
```

### 2. `return(value)`
- 强制终止 Generator，直接返回 `{ value, done: true }`
- 后续所有 `next()` 均返回 `{ value: undefined, done: true }`
- 会触发内部 `finally` 块执行

```js
function* gen() {
  try { yield 1; yield 2; }
  finally { console.log('cleanup'); }
}
const g = gen();
g.next();          // { value: 1, done: false }
g.return('end');   // 输出: cleanup → { value: 'end', done: true }
g.next();          // { value: undefined, done: true }
```

### 3. `throw(error)`
- 向 Generator 内部注入错误，从暂停处抛出
- 若内部有 `try...catch` 可捕获并继续执行；否则错误向外冒泡，Generator 终止

```js
function* gen() {
  try { yield 1; } catch(e) { console.log('caught:', e); yield 'recovered'; }
  yield 2;
}
const g = gen();
g.next();                // { value: 1, done: false }
g.throw(new Error('fail')); // 输出: caught: Error: fail → { value: 'recovered', done: false }
g.next();                // { value: 2, done: false }
```

---

## 四、Generator **与迭代协议的关系**
参考：[[迭代协议（Symbol.iterator）]]

`Generator 对象`**同时实现了 Iterable 与 Iterator 协议**：
- 自带 `[Symbol.iterator]()`，返回自身
- 自带 `next()`，符合迭代器规范
- 可直接用于 `for...of`、展开运算符、`Array.from()` 等

```js
function* range(n) {
  for (let i = 0; i < n; i++) yield i;
}
console.log([...range(3)]); // [0, 1, 2]

// ⚠️ 注意：for...of 会忽略 return() 的值，只消费 yield
function* gen() { yield 1; yield 2; return 3; }
for (const v of gen()) console.log(v); // 仅输出 1 2
```

---

## 五、高级特性

### 1. `yield*` 委托（Delegation）
将执行权交给另一个 Iterable 或 Generator，常用于组合、扁平化、复用逻辑。

```js
function* inner() { yield 'a'; yield 'b'; }
function* outer() {
  yield 1;
  yield* inner(); // 委托执行
  yield 2;
}
console.log([...outer()]); // [1, 'a', 'b', 2]

// 等价于手动展开：
// for (const v of inner()) yield v;
```
`yield*` 的返回值是**被委托迭代器 `return()` 的值**：
```js
function* delegated() { yield 1; return 'done'; }
function* main() { const res = yield* delegated(); console.log(res); }
[...main()]; // 输出: 'done'
```

### 2. 异步流程控制（`async/await` 的前身）
Generator + Promise 可*实现同步写法处理异步逻辑*。**现代 `async/await` 本质是语法糖+自动执行器**。

```js
// 手动实现简易 co 自动执行器
function run(genFn) {
  const g = genFn();
  function next(data) {
    const { value, done } = g.next(data);
    if (done) return value;
    // value 必须是 Promise
    return value.then(next);
  }
  return next();
}

function* fetchUser() {
  const res1 = yield fetch('/api/user');
  const user = yield res1.json();
  const res2 = yield fetch(`/api/posts?uid=${user.id}`);
  return res2.json();
}

//
run(fetchUser).then(console.log);
```

---

## 六、典型应用场景

| 场景 | 示例/说明 |
|------|-----------|
| **惰性求值 / 无限序列** | 斐波那契、ID 生成器、分页懒加载，按需计算不爆内存 |
| **复杂状态机** | 游戏回合、工作流审批、多步表单，用 `yield` 切分阶段 |
| **数据管道 / 流式处理** | 配合 `yield*` 组合过滤、映射、批处理逻辑 |
| **测试 Mock / 可控时序** | 精确控制异步步骤的执行顺序与返回值 |
| **底层库开发** | Redux-Saga、co、task-runner 等核心调度机制 |

```js
// 无限 ID 生成器（惰性+状态保持）
function* idGenerator(prefix = 'ID') {
  let count = 1;
  while (true) yield `${prefix}-${count++}`;
}
const ids = idGenerator('USER');
console.log(ids.next().value); // USER-1
console.log(ids.next().value); // USER-2
```

---

## 七、注意事项与最佳实践

| 陷阱 | 说明 | 规避方案 |
|------|------|----------|
| **首次 `next()` 传参无效** | 执行起点无 `yield` 接收参数 | 首次调用不传参，或通过闭包/构造函数传初始值 |
| **`for...of` 忽略 `return`** | 仅消费 `yield` 产出的值 | 需获取终值时手动调用 `next()` 或改用 `reduce` |
| **错误未捕获导致终止** | 外部 `throw()` 或内部异常未 `catch` 会直接结束 | 关键步骤包裹 `try...catch`，或使用自动执行器统一处理 |
| **内存泄漏风险** | 暂停期间局部变量不被 GC，长期持有迭代器引用会驻留内存 | 用完及时置空引用，或确保走到 `done: true` |
| **性能开销** | 上下文切换比纯函数慢 10%~30% | 避免在高频同步循环中使用；适合 I/O 等待、复杂流程分步 |

> 🔄 **现代选型建议**：  
> - 异步流程控制 → 优先 `async/await`（更简洁、错误处理更直观）  
> - 同步惰性计算 / 状态机 / 底层调度 → Generator 仍是最佳选择  
> - 需要取消/重试/并发控制 → 考虑结合 `AbortController` 或任务队列库

---

## 八、总结

Generator 是 JavaScript 中**唯一原生支持暂停/恢复执行与双向通信**的函数形态。它通过 `function*` 与 `yield` 将同步代码切片，底层由状态机维护上下文，对外暴露标准迭代器接口。虽然异步场景已被 `async/await` 取代，但 Generator 在**惰性序列、复杂状态机、数据管道、底层任务调度**中依然不可替代。

掌握 Generator 的关键在于理解：
1. `yield` 是**表达式**而非语句，兼具产出与接收能力
2. `next(arg)` 的参数流向是**“赋给上一个 yield”**
3. Generator 对象 = Iterable + Iterator + 状态机上下文

如需深入 `async/await` 编译原理、Redux-Saga 的 effect 调度模型，或 Generator 在 TypeScript 中的类型推导技巧，可提供具体方向，我将补充对应实战指南。
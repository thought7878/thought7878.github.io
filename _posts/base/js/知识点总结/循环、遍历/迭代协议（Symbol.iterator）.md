参考：[[7-11 迭代器模式的场景-迭代器在JS中的实际应用]]


`Symbol.iterator` 是 ES6 引入的`迭代协议（Iteration Protocol）` 的核心。
**它定义了 JavaScript 中“如何遍历一个对象”的标准契约**，是 *`for...of`、`展开运算符...`、`Array.from()`、`解构赋值`等现代语法的**底层基石***。

---

## 一、核心概念：什么是 `Symbol.iterator`？

- `Symbol.iterator` 是一个*内置的 Well-Known Symbol*。
- *任何对象只要部署了 `[Symbol.iterator]()` 方法*，就被称为 `可迭代对象（Iterable）`。
- *该方法被调用时，必须返回一个 `迭代器（Iterator）`。*
- ***作用***：**让自定义数据结构能够被 JS 原生遍历语法统一消费，打破“只有数组能遍历”的限制**。

---

## 二、协议的两层结构：Iterable vs Iterator

`迭代协议`实际上**由两个角色组成**，务必区分清楚：

| 角色                  | 定义      | 必须实现的方法               | 职责                                     |
| ------------------- | ------- | --------------------- | -------------------------------------- |
| **Iterable（可迭代对象）** | 数据源容器   | `[Symbol.iterator]()` | 被调用时返回一个 Iterator                      |
| **Iterator（迭代器）**   | 遍历游标/指针 | `next()`              | 每次调用返回 `{ value: any, done: boolean }` |

```js
// `迭代协议`实际上**由两个角色组成**，关系示意：

// Iterable（可迭代对象）
const iterable = { /* 数据 */ };
const iterator = iterable[Symbol.iterator](); // 获取迭代器

// Iterator（迭代器）
iterator.next(); // { value: ..., done: false }
iterator.next(); // { value: ..., done: false }
iterator.next(); // { value: undefined, done: true }
```

> 💡 简单比喻：  
> `Iterable` 是一本书，`[Symbol.iterator]()` 是“翻开书并递给你一个书签”的动作，`Iterator` 是那个书签，`next()` 是“翻到下一页并告诉你内容”。

---

## 三、`for...of` 的底层执行流程

当你写 `for (const item of obj)` 时，JS 引擎实际执行以下步骤：

```js
// 伪代码还原 for...of 行为
const iterator = obj[Symbol.iterator](); // 1. 获取迭代器
let result = iterator.next();            // 2. 首次调用 next()
while (!result.done) {                   // 3. 检查是否结束
  const item = result.value;             // 4. 取出值赋给循环变量
  // 执行你的循环体代码...
  result = iterator.next();              // 5. 继续下一次
}
```

如果对象没有 `[Symbol.iterator]` 方法，或该方法返回的不是合法迭代器，会抛出 `TypeError: obj is not iterable`。

---

## 四、如何 自定义可迭代对象？

### 1. 手动实现（完整协议）
```js
// 定义
const range = {
  from: 1,
  to: 5,
  
  // 部署 Iterable 协议
  [Symbol.iterator]() {
    let current = this.from;
    const last = this.to;
    
    // 返回 Iterator 对象
    return {
      next() {
        if (current <= last) {
          return { value: current++, done: false };
        }
        return { value: undefined, done: true };
      }
    };
  }
};

// 使用
for (const n of range) console.log(n); // 1 2 3 4 5
console.log([...range]);               // [1, 2, 3, 4, 5]
```

### 2. 使用 Generator 语法糖（推荐 ✅）
`Generator 函数` (`function*`) **会自动实现完整的迭代协议，代码量锐减：**
```js
const range = {
  from: 1,
  to: 5,
  *[Symbol.iterator]() { // 注意 * 号
    for (let i = this.from; i <= this.to; i++) {
      yield i; // 自动包装为 { value: i, done: false }
    }
    // 函数结束自动返回 { value: undefined, done: true }
  }
};
```
> Generator 对象**同时实现了 Iterable 和 Iterator**，自带 `[Symbol.iterator]()`（返回自身）和 `next()`。

---

## 五、哪些内置对象 原生支持？

| 类型                            | 是否可迭代 | 遍历内容                |
| ----------------------------- | ----- | ------------------- |
| `Array`                       | ✅     | 元素值                 |
| `String`                      | ✅     | Unicode 字符（正确处理代理对） |
| `Map`                         | ✅     | `[key, value]` 数组   |
| `Set`                         | ✅     | 值                   |
| `arguments`                   | ✅     | 参数列表                |
| `NodeList` / `HTMLCollection` | ✅     | DOM 节点              |
| `TypedArray`                  | ✅     | 数值元素                |
| **普通 `Object`**               | ❌     | 默认不可迭代（设计使然）        |

> 📌 **为什么普通对象默认不可迭代？**  
> 对象常用于存储配置、方法、原型链属性。若默认迭代，会意外遍历到方法或继承属性，破坏数据边界。需显式使用 `Object.keys/values/entries()` 转换。

---

## 六、核心应用场景

### 1. 统一数据消费接口
函数接收 `Iterable` 而非具体数组，**提升泛型能力**：
```js
function sum(iterable) {
  let total = 0;
  for (const n of iterable) total += n;
  return total;
}
sum([1, 2, 3]);           // 6
sum(new Set([4, 5]));     // 9
sum("123");               // 6 (字符串迭代出字符)
```

### 2. 懒加载 / 无限序列
迭代器是**按需计算**的，*适合处理大数据或无限流：*
```js
function* fibonacci() {
  let [a, b] = [0, 1];
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}
const fib = fibonacci();
console.log(fib.next().value); // 0
console.log(fib.next().value); // 1
// 不会卡死，每次 next 才计算下一步
```

### 3. 配合现代语法无缝转换
```js
const set = new Set([1, 2, 3]);
const arr1 = [...set];           // 展开运算符，依赖 iterator
const arr2 = Array.from(set);    // Array.from，依赖 iterator
const [a, b] = set;              // 解构赋值，依赖 iterator
```

---

## 七、完整迭代器协议（进阶）

除了 `next()`，规范还定义了可选方法，用于**提前终止或异常清理**：

| 方法 | 触发时机 | 用途 |
|------|----------|------|
| `return(value)` | `for...of` 中 `break`/`return`/抛出异常时调用 | 清理资源（如关闭文件、断开连接） |
| `throw(error)` | 消费端主动注入错误时调用 | 错误处理/状态回滚 |

```js
const iter = {
  [Symbol.iterator]() { return this; },
  next() { return { value: 1, done: false }; },
  return() {
    console.log('清理资源');
    return { done: true };
  }
};

for (const v of iter) {
  console.log(v);
  break; // 触发 return()
}
// 输出: 1 \n 清理资源
```
> Generator 会自动处理 `return()`，手动实现时若涉及外部资源建议补充。

---

## 八、注意事项与最佳实践

| 问题 | 说明 | 解决方案 |
|------|------|----------|
| **迭代器是一次性的** | `done: true` 后无法重置，再次遍历需重新调用 `[Symbol.iterator]()` | 每次 `for...of` 都会重新获取迭代器，无需担心；手动持有 iterator 时注意 |
| **普通对象不可迭代** | `for...of obj` 会报错 | 用 `Object.entries(obj)` 或自定义 `[Symbol.iterator]` |
| **Generator 性能** | 比手动 `next()` 略慢（约 10~20%） | 业务开发无感；极端性能场景（如游戏循环/渲染）用手动实现 |
| **异步数据流** | `Symbol.iterator` 仅支持同步 | 使用 `Symbol.asyncIterator` + `for await...of`（ES2018） |

---

## 九、一句话总结

> *`Symbol.iterator` 是 JS 的**遍历标准化契约**：对象提供 `[Symbol.iterator]()` 返回迭代器，迭代器通过 `next()` 按需产出 `{value, done}`。* 它**让`自定义数据结构`无缝接入 `for...of`、展开运算符等现代语法，是函数式编程、懒计算、流式处理的底层基石**。

扩展了解 `Symbol.asyncIterator` 异步迭代协议、或如何在 TypeScript 中正确声明 Iterable 类型。
JavaScript 的`垃圾回收（Garbage Collection, GC）`是一种**自动化的内存管理机制**，_由 JavaScript 引擎负责执行_。它的**主要目的**是*释放不再使用的内存，避免程序因内存泄漏或内存耗尽而导致性能问题或崩溃*。

---

## 1. 什么是垃圾回收？

`垃圾回收`是指 *JavaScript 引擎自动检测并回收那些不再被程序使用的内存空间*的过程。开发者无需手动分配或释放内存（与 C/C++ 等语言不同），这大大降低了内存管理的复杂性。

---

## 2. 垃圾回收的核心概念

### 可达性（Reachability）

- **定义**：可达性是指某个值是否可以*通过引用*链从根对象（如全局对象 `window` 或 `global`）*访问到*。
- **规则**：
  - 如果一个值是可达的，则它不会被垃圾回收。
  - 如果一个值不可达，则它会被标记为垃圾并回收。

**示例：可达性**

```javascript
let user = {
  name: "Alice",
};

user = null; // user 对象现在不可达
```

在这个例子中，当 `user` 被设置为 `null` 后，原来的对象 `{ name: "Alice" }` *不再有引用指向它，因此变得不可达，随后会被垃圾回收*。

---

### 内存泄漏（Memory Leak）

- **定义**：`内存泄漏`是指*程序中某些内存无法被垃圾回收，导致可用内存逐渐减少*。
- **常见原因**：
  - *未清理的定时器或事件监听器*。
  - 全局变量过多。
  - 循环引用（在现代引擎中通常已解决）。

**示例：内存泄漏**

```javascript
let element = document.getElementById("myElement");
element.addEventListener("click", function () {
  console.log("Clicked!");
});
// 即使 element 被移除，事件监听器仍然存在，可能导致内存泄漏
```

---

## 3. 垃圾回收算法

JavaScript 引擎使用多种算法来实现垃圾回收，以下是最常见的两种：

### 标记清除（Mark-and-Sweep）

- **原理**：
  1. *遍历*所有对象，从根对象开始*标记所有可达的对象*。
  2. 清除未被标记的对象，释放其占用的内存。
- **优点**：解决了早期算法（如引用计数）中的循环引用问题。
- **示例**：

```javascript
let obj1 = { name: "Alice" };
let obj2 = { age: 25 };

obj1.friend = obj2;
obj2.friend = obj1;

obj1 = null;
obj2 = null; // 即使存在循环引用，obj1 和 obj2 仍会被回收
```

---

### 引用计数（Reference Counting）

- **原理**：*每个对象都有一个引用计数器，记录有多少个引用指向它*。当引用计数为 0 时，对象被销毁。
- **缺点**：无法处理循环引用问题。
- **示例**：

```javascript
function createCycle() {
  let obj1 = {};
  let obj2 = {};

  obj1.ref = obj2;
  obj2.ref = obj1;

  return [obj1, obj2];
}

createCycle(); // obj1 和 obj2 形成循环引用，在引用计数算法中无法回收
```

现代 JavaScript 引擎已*不再单独使用引用计数算法，而是结合标记清除等更先进的技术*。

---

## 4. 垃圾回收的触发时机

JavaScript 引擎会根据内存使用情况动态决定何时触发垃圾回收。开发者*无法直接控制垃圾回收的具体时间，但可以间接影响它*。

### 自动触发

- 当内存使用量达到一定阈值时，垃圾回收器会自动运行。
- 不同引擎（如 V8、SpiderMonkey）有不同的策略和优化。

### 手动提示

虽然*无法直接触发*垃圾回收，但可以*通过一些操作间接提示引擎：*

- 将变量设置为 `null` 或重新赋值。
- 使用 `delete` 删除对象属性。
- 清理定时器或事件监听器。

**示例：手动清理**

```javascript
let data = { value: 42 };
data = null; // 提示垃圾回收器可以回收原对象
```

---

## 5. 如何避免内存泄漏？


### 清理闭包中的引用

```javascript
function createClosure() {
 let largeObj = new Array(10000);
 return function () {
   console.log(largeObj.length);
 };
}
let closure = createClosure();
```
这里的 `largeObj` 被闭包引用，即使 `createClosure` 函数执行完毕，`largeObj` 也不会被回收。避免的方法是*在不再需要时将 `largeObj` 置为 `null`。*

### 清理定时器和事件监听器

- 在不需要时及时清除定时器和事件监听器。
- **示例**：

```javascript
let timer = setInterval(() => {
  console.log("Tick");
}, 1000);

clearInterval(timer); // 清除定时器

let button = document.querySelector("button");
button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick); // 移除事件监听器
```

---

### 避免全局变量

- 全局变量会一直存在，可能导致对象无法被回收。
- 减少全局变量的使用，防止它们长期占用内存。
- **示例**：

```javascript
function createGlobalVariable() {
  window.myData = { value: 42 }; // 不推荐
}
```

---

### 使用弱引用（Weak References）

- 使用 `WeakMap` 和 `WeakSet` 来存储对象引用，这些结构*不会阻止垃圾回收*。
- **示例**：

```javascript
let weakMap = new WeakMap();
let obj = { name: "Alice" };

weakMap.set(obj, "Some data");
obj = null; // obj 被回收后，weakMap 中的键也会被自动清除
```

---

### 分离 DOM 和 JavaScript

- 避免将 DOM 元素直接存储在 JavaScript 中，尤其是在频繁更新的场景中。
- **示例**：

```javascript
let elements = [];
document.querySelectorAll(".item").forEach((item) => {
  elements.push(item); // 不推荐，可能导致内存泄漏
});
```

---

## 6. 性能考虑

   - 虽然垃圾回收是自动的，但不合理的代码可能影响性能：
     - **频繁创建大对象**：频繁创建和销毁大对象可能导致频繁触发垃圾回收，影响性能。例如，在一个循环中创建大数组：
       ```javascript
       for (let i = 0; i < 10000; i++) {
         let largeArray = new Array(10000);
       }
       ```
     - **长时间运行的代码**：长时间运行的代码可能延迟垃圾回收，导致内存占用过高。可以使用 `setTimeout` 或 `setInterval` 来分割代码，给垃圾回收提供时间。
     - **优化建议**：
       - 合理使用数据结构，避免创建过大的数据对象。
       - 及时清除不再需要的引用，将对象置为 `null` 或使用 `delete` 删除属性。


## 7. 总结

- **垃圾回收的作用**：自动释放不再使用的内存，避免内存泄漏。
- **核心算法**：标记清除是最常用的算法，解决了循环引用问题。
- **避免内存泄漏的方法**：
  - 清理定时器和事件监听器。
  - 避免全局变量。
  - 使用 `WeakMap` 和 `WeakSet`。
  - 分离 DOM 和 JavaScript。

理解垃圾回收机制可以帮助开发者编写更高效的代码，并避免常见的内存问题。

### 相关资料
- [[13.垃圾回收：垃圾数据是如何自动回收的？]]
- [[9-q9-garbage-collection]]
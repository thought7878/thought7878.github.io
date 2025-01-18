## 基本概念

   - JavaScript 的垃圾回收（Garbage Collection）是一种**自动内存管理机制**，*用于回收不再使用的内存，防止内存泄漏，确保程序运行时的内存使用效率*。由于 JavaScript 是一种高级语言，**开发者不需要手动分配和释放内存，垃圾回收器会自动处理这一过程**。

## 主要的垃圾回收算法

   - **标记清除（Mark and Sweep）**：
     - **标记阶段**：
       - 垃圾回收器从根对象（如全局对象、当前执行函数的局部变量等）开始，递归遍历所有可达对象，并将其标记为活动对象。可达对象是指从根对象开始可以通过引用链访问到的对象。例如，在以下代码中：
       ```javascript
       let obj1 = { prop1: "value1" };
       let obj2 = { prop2: "value2" };
       obj1.child = obj2;
       ```
       - `obj1` 和 `obj2` 都是可达的，因为可以从全局对象通过 `obj1` 找到 `obj2`。
     - **清除阶段**：
       - 遍历整个堆内存，清除未标记的对象，回收它们占用的内存。这些未标记的对象就是不再被引用的对象，是需要回收的垃圾。例如：
       ```javascript
       let tempObj = { prop: "value" };
       tempObj = null;
       ```
       - 当 `tempObj` 被设置为 `null` 时，原来的对象就变成了不可达对象，会在清除阶段被回收。

   - **引用计数（Reference Counting）**：
     - 为每个对象添加一个引用计数。当对象被引用时，计数加 1；当引用解除时，计数减 1。当计数为 0 时，该对象可被回收。例如：
     ```javascript
     let objA = { prop: "value" }; // 引用计数为 1
     let objB = objA; // 引用计数为 2
     objB = null; // 引用计数为 1
     objA = null; // 引用计数为 0，可回收
     ```
     - 但是，引用计数算法存在一个严重问题，即循环引用：
     ```javascript
     function createCycle() {
       let obj1 = {};
       let obj2 = {};
       obj1.ref = obj2;
       obj2.ref = obj1;
     }
     createCycle();
     ```
     - 在这里，`obj1` 和 `obj2` 相互引用，即使它们在外部没有引用，它们的引用计数也不会为 0，无法被回收。而标记清除算法可以解决这个问题。

## 触发垃圾回收的时机

   - 不同的 JavaScript 引擎（如 V8 引擎）有不同的垃圾回收策略，但通常在以下情况可能触发：
     - **内存分配时**：当尝试分配新内存但堆内存不足时，可能触发垃圾回收，以腾出空间。
     - **周期性**：一些引擎会定期进行垃圾回收，确保内存的有效管理。
     - **程序空闲时**：在程序空闲时间，引擎可能会执行垃圾回收，减少对程序性能的影响。

## 内存泄漏和避免方法

### 内存泄漏的概念
**内存泄漏**是指程序中不再使用的内存没有被正确回收，导致内存占用不断增加。

### 常见的内存泄漏情况

#### 闭包中的引用

```javascript
function createClosure() {
 let largeObj = new Array(10000);
 return function () {
   console.log(largeObj.length);
 };
}
let closure = createClosure();
```
这里的 `largeObj` 被闭包引用，即使 `createClosure` 函数执行完毕，`largeObj` 也不会被回收。避免的方法是在不再需要时将 `largeObj` 置为 `null`。
    
#### 未清理的定时器和事件监听器
   ```javascript
   let element = document.getElementById("some-element");
   element.addEventListener("click", function () {
	 // 事件处理函数
   });
       ```
若不使用 `element.removeEventListener` 移除事件监听器，`element` 可能不会被正确回收。

#### 全局变量

```javascript
let globalObj = new Object();
```
全局变量会一直存在，可能导致对象无法被回收。应尽量减少全局变量的使用，将对象存储在局部变量中。

## 性能考虑

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

总之，了解 JavaScript 的垃圾回收机制对于编写高效、稳定的代码非常重要。通过避免内存泄漏，合理安排代码结构，可以提高程序的性能和可维护性。


在 ECMAScript 2017（ES8）中，async 和 await 被正式引入。它们是*建立在 Promise 和 Generator 基础之上的**语法糖***，旨在**让异步代码的编写和阅读体验无限接近于同步代码，彻底解决了“回调地狱”和 Promise 链式调用（.then 链）在复杂逻辑下依然不够直观的问题**。

以下是对 async/await 的详细、系统性总结：

---

### 一、 核心概念与本质

* ***本质***：async/await 是 Generator 函数 + Promise 的语法糖。它内置了执行器（类似于 co 模块），使得 Generator 函数可以自动执行。
* ***目的***：用**同步的编程风格**来编写**异步逻辑**，提高代码的可读性和可维护性。

---

### 二、 async 关键字详解

async 用于*声明一个异步函数，可以放在普通函数、箭头函数、对象方法或类方法前面*。

#### 1. 核心特性：**必定返回一个 Promise**
*无论 async 函数内部返回什么，它**对外总是返回一个 Promise 对象***。

* **返回普通值**：自动包装成 Promise.resolve(普通值)。
* **返回 Promise**：直接返回该 Promise，状态和值保持不变。
* **抛出异常**：自动包装成 Promise.reject(错误对象)。

```javascript
// 1. 返回普通值
async function foo() { return 1; }
foo().then(v => console.log(v)); // 1

// 2. 返回 Promise
async function bar() { return Promise.resolve(2); }
bar().then(v => console.log(v)); // 2

// 3. 抛出异常
async function baz() { throw new Error('fail'); }
baz().catch(e => console.log(e.message)); // 'fail'
```

#### 2. async 函数内部的执行
*async 函数内部的代码是**同步执行**的，直到遇到第一个 await 关键字，函数才会暂停并让出主线程的控制权*。

---

### 三、 await 关键字详解

await 意为 **“等待”**，它**只能在 async 函数内部使用**（ES2022 引入了 Top-level await，允许在 ES 模块顶层使用）。

#### 1. await 等待什么？
* **等待 Promise**：*暂停 async 函数的执行，直到该 Promise 状态改变（fulfilled 或 rejected）*。
  * 如果 Promise 成功（fulfilled），***await 表达式的返回值就是 Promise 的 value***。
  * 如果 Promise 失败（rejected），**await 会抛出异常**（可以通过 try...catch 捕获）。
* **等待非 Promise（普通值）**：*等同于 await Promise.resolve(普通值)*，直接返回该值，但**依然会触发微任务的切换**（让出执行权）。

```javascript
async function test() {
  const p1 = await Promise.resolve('hello'); // 等待 Promise
  console.log(p1); // 'hello'

  const p2 = await 'world'; // 等待普通值，等同于 await Promise.resolve('world')
  console.log(p2); // 'world'
}
```

#### 2. **右侧表达式的执行时机**
*await 右侧的表达式会**立即同步执行**，执行完毕后，async 函数才会暂停等待其结果*。

```javascript
async function test() {
  // 'start' 会立即同步打印，然后才等待 setTimeout 的结果
  const res = await new Promise(resolve => {
    console.log('start'); 
    setTimeout(() => resolve('end'), 1000);
  });
  console.log(res);
}
test(); 
// 输出顺序：'start' -> (等待1秒) -> 'end'
```

---

### 四、 **运行机制**（结合 Event Loop）

理解 await 的底层机制是掌握它的关键。当代码执行到 await 时，发生了什么？

1. **同步执行**：*同步执行 await 右侧的表达式*。
2. **让出控制权**：*如果右侧返回的是 Promise，async 函数会**暂停执行**，将主线程控制权交还*。
3. **注册微任务**：*将 await 之后的代码包装成一个微任务（类似于 .then() 的回调），放入微任务队列*。
4. **恢复执行**：*当 Promise 状态改变，且主线程同步代码和之前的微任务执行完毕后，从微任务队列中取出该任务，**恢复** async 函数的执行*。

**经典面试题解析：**
```javascript
async function async1() {
  console.log('async1 start');
  await async2(); // async2 同步执行，返回 Promise，await 后面的代码进入微任务
  console.log('async1 end'); 
}

async function async2() {
  console.log('async2');
}

console.log('script start');
setTimeout(() => console.log('setTimeout'), 0);
async1();
new Promise(resolve => {
  console.log('promise1');
  resolve();
}).then(() => console.log('promise2'));
console.log('script end');

/* 输出顺序：
1. script start (同步)
2. async1 start (同步)
3. async2 (同步)
4. promise1 (同步)
5. script end (同步)
--- 同步代码执行完毕，清空微任务队列 ---
6. async1 end (微任务：await 后续代码)
7. promise2 (微任务：.then 回调)
--- 微任务清空，执行宏任务 ---
8. setTimeout (宏任务)
*/
```

---

### 五、 核心优势 (对比 Promise .then)

1. **代码可读性极高**：消灭了 .then 链，逻辑像同步代码一样自上而下。
2. **错误处理更统一**：可以使用传统的 try...catch 捕获同步和异步错误，而 Promise 需要用 .catch()。
3. **条件分支和循环更自然**：在 .then 中写 if/else 或 for 循环非常别扭，而在 async/await 中则顺理成章。
4. **调试更友好**：在 await 语句上打断点，调试器会暂停并等待；而在 .then 回调中打断点，调试器无法跨步跟踪。

---

### 六、 **常见陷阱与最佳实践**

#### 1. 串行 vs 并发（性能陷阱）
如果多个异步操作**没有依赖关系**，千万不要连续 await，这会导致**串行执行**，极大地浪费时间。

❌ **错误写法（串行，耗时 3 秒）：**
```javascript
async function fetchAll() {
  const user = await fetchUser();    // 耗时 1s
  const posts = await fetchPosts();  // 耗时 1s
  const comments = await fetchComments(); // 耗时 1s
}
```

✅ **正确写法（并发，耗时 1 秒）：**
```javascript
async function fetchAll() {
  // 同时发起请求
  const userPromise = fetchUser();
  const postsPromise = fetchPosts();
  const commentsPromise = fetchComments();
  
  // 等待所有结果
  const [user, posts, comments] = await Promise.all([
    userPromise, postsPromise, commentsPromise
  ]);
}
```

#### 2. 循环中的 await（大坑）
在 forEach 或 map 中使用 await **不会**按预期阻塞循环。

❌ **错误写法（不会等待）：**
```javascript
async function processItems(items) {
  // forEach 的回调是同步执行的，它不关心回调返回的 Promise
  items.forEach(async (item) => {
    await processItem(item); 
  });
  console.log('Done'); // 会立刻打印，不会等 processItem 完成
}
```

✅ **正确写法 1（串行执行，使用 for...of）：**
```javascript
async function processItems(items) {
  for (const item of items) {
    await processItem(item); // 会逐个等待
  }
  console.log('Done'); 
}
```

✅ **正确写法 2（并发执行，使用 Promise.all + map）：**
```javascript
async function processItems(items) {
  const promises = items.map(item => processItem(item));
  await Promise.all(promises); // 并发等待所有完成
  console.log('Done');
}
```

#### 3. 优雅的错误处理 (Go 风格)
为了避免满屏的 try...catch，可以封装一个工具函数，将错误和数据以数组形式返回：

```javascript
// 封装 to 函数
function to(promise) {
  return promise
    .then(data => [null, data])
    .catch(err => [err, null]);
}

// 使用
async function getUser() {
  const [err, user] = await to(fetchUser());
  if (err) {
    console.error('获取用户失败:', err);
    return;
  }
  console.log('用户数据:', user);
}
```

---

### 七、 补充：Top-level await (ES2022)

在 ES2022 中，引入了**顶层 await**（Top-level await）。允许在 ES 模块（\<script type="module"\> 或 Node.js 的 ESM）的顶层作用域直接使用 await，而无需包裹在 async 函数中。

**应用场景**：
1. 模块初始化时动态加载资源。
2. 动态 import() 模块。

```javascript
// module.js (ES 模块顶层)
const response = await fetch('https://api.example.com/data');
const data = await response.json();
export { data };
```
*注意：如果模块 A 顶层 await 阻塞，依赖模块 A 的模块 B 也会等待，直到 A 执行完毕。*

---

### 八、 总结

* async/await 并没有改变 JavaScript 单线程和非阻塞的本质，它只是让**代码的书写方式**变成了同步风格。
* **async** 负责将函数返回值包装为 Promise。
* **await** 负责暂停 async 函数的执行，等待 Promise 决议，并解包出结果。
* 掌握 async/await 的关键在于理解其背后的 **Promise 机制**和**微任务执行顺序**，并在实战中注意**串行/并发**的性能差异以及**循环中的正确用法**。

---

# 概念简介

   - async和await是 JavaScript 中用于*处理异步操作的关键字*。它们*建立在 Promise 之上*，**使得异步代码的编写更加简洁和易于理解，就像是把异步操作写成了同步的形式**。
   - async函数是一个返回Promise对象的函数。这意味着你可以在async函数中使用await关键字来暂停函数的执行，直到一个Promise被解决（resolved）或者被拒绝（rejected）。

# async 函数

   - **定义**：通过在函数声明前面添加async关键字来定义一个async函数。例如：
   ```javascript
   async function getData() {
     // 函数体
   }
   ```
   - **返回值**：*async函数总是返回一个Promise对象*。如果在函数内部没有显式地返回一个Promise，它会自动将返回值包装在一个已解决的Promise中。例如：
   ```javascript
   async function returnValue() {
     return 42;
   }
   // 等同于
   async function returnValue() {
     return Promise.resolve(42);
   }
   ```

# await 表达式

## 使用位置
*await只能在async函数内部使用，它后面的表达式只能是promise对象*。它**用于暂停async函数的执行，直到等待的Promise被解决（resolved）或者被拒绝（rejected）**。

## 作用机制
- **当遇到await时，JavaScript 运行时会暂停async函数的执行，将控制权交出去，直到Promise完成**。
- **如果Promise被解决，await表达式会返回Promise的解决值；如果Promise被拒绝，await会抛出一个异常，这个异常可以在async函数内部使用try/catch块来捕获**。

例如：
   ```javascript
   async function getData() {
     try {
       const response = await fetch("https://example.com/api/data");
       const data = await response.json();
       return data;
     } catch (error) {
       console.error("出错了:", error);
     }
   }
   ```
   - 在这个例子中，首先await暂停函数执行，等待fetch操作返回的Promise被解决。fetch操作成功后，得到response对象，然后又使用await等待response.json()返回的Promise被解决，最后返回解析后的数据。如果在fetch或者response.json()过程中出现错误，catch块会捕获异常并进行处理。

# 与传统 Promise 的比较

   - **传统 Promise 写法**：在没有async/await时，处理异步操作的Promise链*可能会变得复杂和难以阅读*。例如：
   ```javascript
   function getData() {
     return fetch("https://example.com/api/data")
       .then((response) => response.json())
       .then((data) => {
         // 对数据进行处理
         return data;
       })
       .catch((error) => {
         console.error("出错了:", error);
       });
   }
   ```

   - **async/await 写法的优势**：async/await让代码看起来*更像是同步代码，逻辑更加清晰*。它*把异步操作的流程扁平化，减少了嵌套，提高了代码的可读性和可维护性*。尤其是在处理多个异步操作有先后顺序的情况时，async/await的优势更加明显。例如，在前面的getData函数中，使用async/await的版本更容易理解数据获取和处理的步骤。

# 错误处理

   - 在async函数中，可以使用try/catch块来捕获await表达式抛出的异常。这使得错误处理更加方便和直观，就像在同步代码中处理错误一样。例如：
   ```javascript
   async function doSomething() {
     try {
       const result = await someAsyncFunction();
       console.log("异步操作成功:", result);
     } catch (error) {
       console.error("异步操作失败:", error);
     }
   }
   ```
   - 如果someAsyncFunction返回的Promise被拒绝，catch块会捕获这个异常，并执行相应的错误处理代码。

# 参考资料
[[20.async、await-使用同步的方式去写异步代码]]

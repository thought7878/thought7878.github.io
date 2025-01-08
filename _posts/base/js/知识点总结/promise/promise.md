# [JavaScript 可视化 - Promise 执行（双语字幕）](https://www.youtube.com/watch?v=Xs1EMmBLpn4&ab_channel=LydiaHallie)

[b 站视频](https://www.bilibili.com/video/BV1bD421V756/?spm_id_from=333.999.0.0&vd_source=22af953ea4c09540ad1966711a2d53f0)

- [00:00](https://www.bilibili.com/video/BV1bD421V756/?t=0.621977#t=0.62) 视频简介
- [00:17](https://www.bilibili.com/video/BV1bD421V756/?t=17.850659#t=17.85) Promise 对象、resolve/reject 函数、执行过程、Promise Constructor - Promise 对象属性： - PromiseState：pending、fulfilled、rejected - PromiseResult - PromiseFulfillReactions - PromiseRejectReactions - PromiseIsHandled
  ![[_posts/base/js/知识点总结/media/d98f4eb9c4868a14f459a84b7d6582a1_MD5.jpeg]]

- [00:46](https://www.bilibili.com/video/BV1bD421V756/?t=46.84439#t=46.84) Promise 的执行流程
- [01:14](https://www.bilibili.com/video/BV1bD421V756/?t=74.072898#t=01:14.07) PromiseFulfillReactions、PromiseRejectReactions、PromiseReaction
- [02:36](https://www.bilibili.com/video/BV1bD421V756/?t=156.385124#t=02:36.39) Promise 结合异步任务（异步请求、计时器）和执行流程
- [04:39](https://www.bilibili.com/video/BV1bD421V756/?t=279.852953#t=04:39.85) 链式调用，Chaining thens
  - then()返回新的 Promise 对象
- [06:21](https://www.bilibili.com/video/BV1bD421V756/?t=381.752097#t=06:21.75) Challenge
- [08:03](https://www.bilibili.com/video/BV1bD421V756/?t=483.39819#t=08:03.40)

# Promise 是什么？

## 定义

- Promise 是异步编程的一种解决方案；
- 它是一个对象，代表了一个尚未完成或已成功或已失败的异步操作；
- 并且提供了统一的 API，用于处理异步操作的最终完成（或失败）及其结果值。

## 状态

Promise 有三种状态，分别是`pending`（进行中）、`fulfilled`（已成功）和`rejected`（已失败）。Promise 对象的状态改变只能是从`pending`到`fulfilled`或者从`pending`到`rejected`，并且*状态一旦改变就不能再改变*。

**示例说明状态改变**：
例如，当你发起一个网络请求（如使用`fetch` API 获取数据），在请求还在进行的时候，这个 Promise 就处于`pending`状态。当服务器成功返回数据，Promise 的状态就变为`fulfilled`，并且会携带返回的数据；如果请求过程中出现错误，比如网络故障或者服务器返回错误码，Promise 的状态就变为`rejected`，并且会携带错误信息。

# 为什么使用 Promise？

- **解决回调地狱问题**：在没有 Promise 之前，异步操作主要依靠回调函数来处理。当有多个异步操作相互依赖时，就会出现回调嵌套回调的情况，这就是所谓的“回调地狱”。它会使代码难以阅读、维护和调试。例如：
  ```javascript
  // 不使用Promise的回调地狱示例
  setTimeout(() => {
    console.log("第一步完成");
    setTimeout(() => {
      console.log("第二步完成");
      setTimeout(() => {
        console.log("第三步完成");
      }, 1000);
    }, 1000);
  }, 1000);
  ```
  上面的代码中有三层嵌套的`setTimeout`，如果异步操作更加复杂，嵌套会更多。而使用 Promise 可以将这种嵌套的结构扁平化，使代码更加清晰。

- **更好的错误处理**：Promise 提供了统一的错误处理机制。在回调函数中，错误处理可能会分散在各个回调中，容易遗漏。而 Promise 可以通过`.catch`方法来捕获异步操作中的错误，例如：
  ```javascript
  function asyncFunction() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        // 模拟一个错误
        reject(new Error("出错啦"));
      }, 1000);
    });
  }
  asyncFunction().catch((error) => {
    console.log(error);
  });
  ```

# 怎么用？

## 创建 Promise

  - 基本语法是使用`new Promise`构造函数，它接受一个函数作为参数，这个函数有两个参数`resolve`和`reject`。例如：
  ```javascript
  const myPromise = new Promise((resolve, reject) => {
    // 假设这里是一个异步操作，比如读取文件
    setTimeout(() => {
      const data = "读取到的数据";
      resolve(data);
    }, 1000);
  });
  ```

## 使用 Promise

### then 方法
  
  **用于处理 Promise 成功（`fulfilled`）的情况**。它接受一个函数作为参数，这个函数会在 Promise 状态变为`fulfilled`时被调用，并且会传入 Promise 成功的结果值。例如：
  
  ```javascript
  myPromise.then((result) => {
    console.log(result);
  });
  ```
  
### catch 方法
  
  **用于处理 Promise 失败（`rejected`）的情况**。它接受一个函数作为参数，这个函数会在 Promise 状态变为`rejected`时被调用，并且会传入 Promise 失败的原因（通常是一个错误对象）。例如：
  ```javascript
  const anotherPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
      reject(new Error("出现错误"));
    }, 1000);
  });
  anotherPromise.catch((error) => {
    console.log(error);
  });
  ```

### finally 方法

**这个方法会在 Promise 完成（无论是`fulfilled`还是`rejected`）后执行**。例如：
  ```javascript
  const thirdPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("操作完成");
    }, 1000);
  });
  thirdPromise.finally(() => {
    console.log("无论成功还是失败，我都会执行");
  });
  ```

Promise 在现代 JavaScript 异步编程中是非常重要的工具，它也被广泛应用于各种框架和库中，如`axios`（用于网络请求）等，这些库在内部也大量使用 Promise 来处理异步操作。

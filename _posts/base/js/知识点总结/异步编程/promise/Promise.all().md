1. **基本概念**

   - `Promise.all`是一个*静态方法*，**用于将多个 Promise 实例包装成一个新的 Promise**。它接受一个可迭代对象（通常是数组）作为参数，数组中的每个元素都应该是一个 Promise 对象。

   - **这个新的 Promise 的状态变化规则如下：**
     - 当所有的 Promise 都变为`fulfilled`状态时，`Promise.all`返回的 Promise 也变为`fulfilled`状态，并且它的结果是一个数组，这个数组按照参数中 Promise 的顺序包含了每个 Promise 的`fulfilled`结果。
     - *只要其中有一个 Promise 变为`rejected`状态，`Promise.all`返回的 Promise 就会立即变为`rejected`状态，并且它的结果是第一个被`rejected`的 Promise 的错误原因*。

2. **使用示例**

   - **多个成功的 Promise**
     - 假设我们有三个返回 Promise 的函数，分别模拟不同的异步操作。
     ```javascript
     function asyncTask1() {
       return new Promise((resolve) => {
         setTimeout(() => {
           resolve("任务1完成");
         }, 1000);
       });
     }
     function asyncTask2() {
       return new Promise((resolve) => {
         setTimeout(() => {
           resolve("任务2完成");
         }, 1500);
       });
     }
     function asyncTask3() {
       return new Promise((resolve) => {
         setTimeout(() => {
           resolve("任务3完成");
         }, 2000);
       });
     }
     const promises = [asyncTask1(), asyncTask2(), asyncTask3()];
     Promise.all(promises).then((results) => {
       console.log(results);
       // 输出 ["任务1完成", "任务2完成", "任务3完成"]，顺序与promises数组中的顺序一致
     });
     ```
   - **包含失败的 Promise**
     - 假设在上述的三个任务中，有一个任务失败。
     ```javascript
     function asyncTask4() {
       return new Promise((resolve, reject) => {
         setTimeout(() => {
           reject(new Error("任务4失败"));
         }, 1000);
       });
     }
     const newPromises = [asyncTask1(), asyncTask2(), asyncTask4()];
     Promise.all(newPromises).catch((error) => {
       console.log(error);
       // 输出 Error: 任务4失败，只要有一个任务失败，Promise.all就会进入catch状态
     });
     ```

3. **应用场景和注意事项**
   - **应用场景**
     - 用于同时发起多个相互独立的异步请求，并且需要等待所有请求都成功完成后再进行下一步操作。例如，一个网页需要同时获取用户信息、文章内容和评论列表，就可以使用`Promise.all`来同时发起这三个请求，等全部完成后再一起渲染页面。
   - **注意事项**
     - 由于`Promise.all`只要有一个 Promise 失败就会立即进入`rejected`状态，所以在某些场景下，如果希望即使有部分任务失败，其他任务的结果仍然能够被获取，可以考虑使用`Promise.allSettled`（在一些 JavaScript 环境中可用）。
     - 传递给`Promise.all`的参数必须是一个可迭代对象，并且其中的元素应该是 Promise 对象。如果其中有非 Promise 对象，它会被自动转换为一个立即`fulfilled`的 Promise（例如，传递一个数字，会被视为一个`fulfilled`状态的 Promise，其结果就是这个数字）。

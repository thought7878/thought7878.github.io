1. **基本概念**

   - `Promise.allSettled`是一个静态方法，和`Promise.all`类似，它也用于处理多个 Promise 对象。但与`Promise.all`不同的是，`Promise.allSettled`会等待所有传入的 Promise 都已经确定状态（无论是`fulfilled`还是`rejected`），然后返回一个新的 Promise。
   - 这个新 Promise 的状态总是`fulfilled`，它的结果是一个数组，数组中的每个元素是一个对象，该对象描述了对应的原始 Promise 的状态和结果。每个对象有两个属性：
     - `status`：其值为`"fulfilled"`或`"rejected"`，表示原始 Promise 的最终状态。
     - `value`（当`status`为`"fulfilled"`时）或`reason`（当`status`为`"rejected"`时）：表示原始 Promise 的成功值或失败原因。

2. **使用示例**

   - 假设我们有三个返回 Promise 的函数，其中一个会失败。

   ```javascript
   function asyncFunction1() {
     return new Promise((resolve) => {
       setTimeout(() => {
         resolve("第一个任务完成");
       }, 1000);
     });
   }
   function asyncFunction2() {
     return new Promise((resolve) => {
       setTimeout(() => {
         resolve("第二个任务完成");
       }, 1500);
     });
   }
   function asyncFunction3() {
     return new Promise((resolve, reject) => {
       setTimeout(() => {
         reject(new Error("第三个任务失败"));
       }, 2000);
     });
   }
   const promises = [asyncFunction1(), asyncFunction2(), asyncFunction3()];
   Promise.allSettled(promises).then((results) => {
     console.log(results);
     /*
     输出：
     [
       { status: "fulfilled", value: "第一个任务完成" },
       { status: "fulfilled", value: "第二个任务完成" },
       { status: "rejected", reason: Error: 第三个任务失败 }
     ]
     */
   });
   ```

   - 在这个例子中，`Promise.allSettled`等待所有的 Promise 都确定状态后，返回一个状态为`fulfilled`的新 Promise。这个新 Promise 的结果是一个数组，其中包含了每个原始 Promise 的状态和结果的描述对象。

3. **应用场景和优势**
   - **应用场景**
     - 当需要同时发起多个异步操作，并且希望获取所有操作的最终状态（无论成功还是失败）时，`Promise.allSettled`非常有用。例如，在批量处理多个文件上传任务时，可能想要知道每个文件上传的最终结果，包括成功的文件和失败的文件。
   - **优势**
     - 相比于`Promise.all`，它不会因为某个 Promise 的失败而提前终止。这使得在处理多个可能会失败的异步任务时，可以更全面地收集和处理信息，而不是只关注第一个失败的任务。例如，在一个数据同步场景中，可能有多个数据源需要同步，使用`Promise.allSettled`可以获取所有数据源同步的最终情况，以便进行后续的记录或修复操作。

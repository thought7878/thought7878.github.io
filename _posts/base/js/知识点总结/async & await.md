# 概念简介

   - `async`和`await`是 JavaScript 中用于*处理异步操作的关键字*。它们*建立在 Promise 之上*，**使得异步代码的编写更加简洁和易于理解，就像是把异步操作写成了同步的形式**。
   - `async`函数是一个返回`Promise`对象的函数。这意味着你可以在`async`函数中使用`await`关键字来暂停函数的执行，直到一个`Promise`被解决（resolved）或者被拒绝（rejected）。

# async 函数

   - **定义**：通过在函数声明前面添加`async`关键字来定义一个`async`函数。例如：
   ```javascript
   async function getData() {
     // 函数体
   }
   ```
   - **返回值**：*`async`函数总是返回一个`Promise`对象*。如果在函数内部没有显式地返回一个`Promise`，它会自动将返回值包装在一个已解决的`Promise`中。例如：
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
*`await`只能在`async`函数内部使用，它后面的表达式只能是promise对象*。它**用于暂停`async`函数的执行，直到等待的`Promise`被解决（resolved）或者被拒绝（rejected）**。

## 作用机制
- **当遇到`await`时，JavaScript 运行时会暂停`async`函数的执行，将控制权交出去，直到`Promise`完成**。
- **如果`Promise`被解决，`await`表达式会返回`Promise`的解决值；如果`Promise`被拒绝，`await`会抛出一个异常，这个异常可以在`async`函数内部使用`try/catch`块来捕获**。

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
   - 在这个例子中，首先`await`暂停函数执行，等待`fetch`操作返回的`Promise`被解决。`fetch`操作成功后，得到`response`对象，然后又使用`await`等待`response.json()`返回的`Promise`被解决，最后返回解析后的数据。如果在`fetch`或者`response.json()`过程中出现错误，`catch`块会捕获异常并进行处理。

# 与传统 Promise 的比较

   - **传统 Promise 写法**：在没有`async/await`时，处理异步操作的`Promise`链*可能会变得复杂和难以阅读*。例如：
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

   - **async/await 写法的优势**：`async/await`让代码看起来*更像是同步代码，逻辑更加清晰*。它*把异步操作的流程扁平化，减少了嵌套，提高了代码的可读性和可维护性*。尤其是在处理多个异步操作有先后顺序的情况时，`async/await`的优势更加明显。例如，在前面的`getData`函数中，使用`async/await`的版本更容易理解数据获取和处理的步骤。

# 错误处理

   - 在`async`函数中，可以使用`try/catch`块来捕获`await`表达式抛出的异常。这使得错误处理更加方便和直观，就像在同步代码中处理错误一样。例如：
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
   - 如果`someAsyncFunction`返回的`Promise`被拒绝，`catch`块会捕获这个异常，并执行相应的错误处理代码。

# 参考资料
[[20.async、await-使用同步的方式去写异步代码]]

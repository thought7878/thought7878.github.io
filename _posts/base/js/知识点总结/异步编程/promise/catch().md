# 基本概念

- `catch`方法是用于**处理 Promise 被拒绝（`rejected`）的情况**。*它实际上是`.then(null, rejectionHandler)`的语法糖，不过使用`catch`会让代码的错误处理意图更加清晰*。
- 当一个 Promise 的状态变为`rejected`时，`catch`方法中注册的回调函数就会被调用，并且会传入导致 Promise 被拒绝的原因（通常是一个`Error`对象或其他有意义的错误值）。

# 使用示例

## 基本的错误捕获

- 考虑一个返回 Promise 的函数，在其中模拟一个会出错的异步操作。
  ```javascript
  function asyncError() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject(new Error("发生了错误"));
      }, 1000);
    });
  }
  asyncError().catch((error) => {
    console.log(error.message); // 输出 "发生了错误"
  });
  ```

## 链式调用中的错误捕获

- 在 Promise 链式调用中，`catch`方法*可以捕获链中前面任何一个`then`方法里抛出的错误*。
  ```javascript
  function firstAsync() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("第一步数据");
      }, 1000);
    });
  }
  firstAsync()
    .then((result) => {
      throw new Error("在第一个then中出错");
    })
    .catch((error) => {
      console.log(error.message); // 输出 "在第一个then中出错"
    });
  ```

## 在多个`catch`和`then`组合中捕获错误

- 可以在链式调用中有多个`catch`和`then`方法。*一旦某个`catch`捕获到错误，后续的`then`方法会在错误被处理后继续执行*。
  ```javascript
  function complexAsync() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject(new Error("复杂的错误"));
      }, 1000);
    });
  }
  complexAsync()
    .catch((error) => {
      console.log("第一次捕获错误:", error.message);
      return "从错误中恢复的数据";
    })
    .then((result) => {
      console.log("继续处理:", result);
    });
  ```
在这个例子中，`catch`方法捕获了错误，输出错误信息后，返回了一个值。这个值会被传递给下一个`then`方法，使得后续的处理可以继续进行。

# 注意事项

- 如果没有`catch`方法来处理被拒绝的 Promise，并且这个错误没有在其他地方被处理，那么这个错误可能会导致 JavaScript 运行时抛出未捕获的异常，这在某些情况下可能会导致程序崩溃或者出现不可预期的行为。
- 一个`catch`方法可以捕获前面多个`then`方法产生的错误，但它只会处理第一个进入它的错误。如果在`catch`方法中又产生了新的错误，这个新错误可以通过后续的`catch`方法来捕获（catch是then的语法糖）。

# 基本概念

- `finally`方法是 Promise 对象提供的一个方法，用于指定*不管 Promise 对象最终是`fulfilled`（成功）还是`rejected`（失败）都会执行的操作*。
- 它接收一个函数作为参数，*这个函数在 Promise 状态确定后执行，并且这个函数没有参数传递进来*。**这使得它在执行一些清理操作（如关闭资源、清除定时器等）时非常有用**，无论异步操作的结果如何，这些清理操作都需要进行。

# 使用示例

## 基本的`finally`调用

- 假设我们有一个返回 Promise 的函数，它模拟一个异步操作，可能成功也可能失败。

  ```javascript
  function asyncOperation() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        const randomValue = Math.random();
        if (randomValue > 0.5) {
          resolve("操作成功");
        } else {
          reject(new Error("操作失败"));
        }
      }, 1000);
    });
  }
  asyncOperation()
    .then((result) => {
      console.log(result);
    })
    .catch((error) => {
      console.log(error);
    })
    .finally(() => {
      console.log("异步操作已经结束");
    });
  ```

在这个例子中，`finally`方法中的回调函数会在`then`或者`catch`执行之后执行，用于表明异步操作已经完成，无论它是成功还是失败。

## 在链式调用中使用`finally`

- 可以在 Promise 的链式调用中多次使用`finally`。例如，我们有一个更复杂的操作，包括多个异步步骤。
  ```javascript
  function step1() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        resolve("第一步完成");
      }, 1000);
    });
  }
  function step2() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        const randomValue = Math.random();
        if (randomValue > 0.5) {
          resolve("第二步完成");
        } else {
          reject(new Error("第二步失败"));
        }
      }, 1000);
    });
  }
  step1()
    .then((result) => {
      console.log(result);
      return step2();
    })
    .then((result) => {
      console.log(result);
    })
    .catch((error) => {
      console.log(error);
    })
    .finally(() => {
      console.log("整个操作流程结束");
    });
  ```

*在这里，`finally`方法在整个 Promise 链式调用的最后执行，用于清理或记录整个异步操作流程已经结束的状态*。

# 注意事项

- `finally`方法中的回调函数*不接收 Promise 的结果*（成功的值或失败的原因）作为参数。如果需要在`finally`之后继续处理 Promise 的结果，需要在`finally`之前的`then`或`catch`方法中对结果进行适当的处理并传递下去。
- 虽然`finally`方法中的回调函数会在 Promise 状态确定后执行，但*它内部抛出的异常*依然会影响后续代码的执行。如果`finally`方法中抛出了一个错误，这个错误会导致后续的代码无法正常执行，并且需要通过`catch`方法来处理这个新的错误。

# then()

## 基本概念

`then`方法是 Promise 对象的一个重要方法，它用于**处理 Promise 成功（`fulfilled`）的情况**。一个 Promise 对象可以通过链式*调用多个`then`方法来按顺序处理异步操作的结果*。

### 参数

- 它接受一个或两个函数作为参数。*第一个函数*是当 Promise 状态变为`fulfilled`时要执行的回调函数，这个函数会接收到 Promise 成功后的结果值作为参数。_第二个函数_（可选）是当 Promise 状态变为`rejected`时要执行的回调函数，不过通常更*推荐使用`catch`方法来处理失败情况*。

### 返回值

#### 返回普通值

当`then`方法中的回调函数返回一个普通值（如字符串、数字、布尔值、对象、数组等）时，**这个普通值会被自动包装成一个新的`Promise`对象，并且这个新`Promise`的状态为`fulfilled`**。后续的`then`方法（如果存在链式调用）会**将这个普通值作为参数接收**。

例如：

```javascript
const myPromise = new Promise((resolve) => {
  resolve("初始数据");
});
myPromise
  .then((result) => {
    console.log(result); // 输出 "初始数据"
    return result + "经过处理";
  })
  .then((newResult) => {
    console.log(newResult); // 输出 "初始数据经过处理"
  });
```

- 在第一个`then`中，返回了一个经过处理后的字符串，_这个字符串被包装成新的`Promise`，然后第二个`then`接收了这个字符串并进行处理_。

#### 返回 Promise 对象

如果`then`方法中的回调函数*返回一个新的`Promise`对象，那么后续的`then`方法会暂停执行，等待这个新返回的`Promise`状态改变*。

例如：

```javascript
const firstPromise = new Promise((resolve) => {
  resolve("第一步数据");
});
firstPromise
  .then((result) => {
    console.log(result);
    return new Promise((innerResolve) => {
      setTimeout(() => {
        innerResolve("第二步数据");
      }, 1000);
    });
  })
  .then((newResult) => {
    console.log(newResult);
  });
```

- 在这个例子中，第一个`then`返回了一个新的`Promise`，这个`Promise`在 1 秒后`resolve`。第二个`then`会等待这个新的`Promise`状态改变，当新`Promise`变为`fulfilled`时，第二个`then`中的回调函数才会被执行，并且接收新`Promise`的`resolve`值（这里是`第二步数据`）。

#### 没有返回值（返回`undefined`）

如果`then`方法中的回调函数没有返回值（或者显式返回`undefined`），那么后续的`then`方法（如果存在链式调用）会接收到`undefined`作为参数。

例如：

```javascript
const anotherPromise = new Promise((resolve) => {
  resolve("原始数据");
});
anotherPromise
  .then((result) => {
    console.log(result);
  })
  .then((newResult) => {
    console.log(newResult); // 输出undefined
  });
```

- 第一个`then`没有返回值，所以第二个`then`接收到的参数是`undefined`。

## 使用示例

### 简单的`then`调用

- 假设我们有一个返回 Promise 的函数，它模拟一个异步操作，比如延迟一段时间后返回一个数字。
  ```javascript
  function asyncAdd() {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve(2 + 3);
      }, 1000);
    });
  }
  asyncAdd().then((result) => {
    console.log(result); // 输出5
  });
  ```

### 链式`then`调用

- 可以通过链式`then`方法来对异步操作的结果进行多次处理。例如，先获取一个数字，然后将这个数字乘以 2，再输出。
  ```javascript
  function asyncNumber() {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve(4);
      }, 1000);
    });
  }
  asyncNumber()
    .then((result) => {
      return result * 2;
    })
    .then((newResult) => {
      console.log(newResult); // 输出8
    });
  ```

### `then`中返回 Promise

- `then`方法中返回的内容也会被 Promise 机制处理。**如果返回一个普通值**，那么这个值会被作为下一个`then`方法的参数。**如果返回一个新的 Promise**，那么下一个`then`方法会等待这个新的 Promise 状态改变后再执行。
  ```javascript
  function asyncOuter() {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve("外层数据");
      }, 1000);
    });
  }
  function asyncInner() {
    return new Promise((resolve) => {
      setTimeout(() => {
        resolve("内层数据");
      }, 1000);
    });
  }
  asyncOuter()
    .then((outerResult) => {
      console.log(outerResult);
      return asyncInner();
    })
    .then((innerResult) => {
      console.log(innerResult);
    });
  ```

在这个例子中，第一个`then`方法先输出`外层数据`，然后*返回一个新的 Promise*（`asyncInner`）。第二个`then`方法会等待这个新的 Promise 状态改变后再执行，当`asyncInner`的 Promise 状态变为`fulfilled`时，输出`内层数据`。

### 注意事项

- 每个`then`方法的回调函数都是异步执行的。即使`Promise`立即`resolve`，`then`方法中的回调函数也会被放入微任务队列中，等待当前执行栈中的同步代码执行完毕后再执行。
- 在链式`then`调用中，如果前一个`then`方法没有返回值（或者返回`undefined`），那么下一个`then`方法的参数将是`undefined`。

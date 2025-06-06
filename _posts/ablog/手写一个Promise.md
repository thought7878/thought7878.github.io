---
title: '手写一个Promise'

date: '2021-07-05'

lastmod: '2021-07-05'

tags: ['JavaScript']

draft: false

summary: 'Promise是JavaScript中处理异步编程的一种方式，本文将详细介绍Promise的基本特点、方法以及如何手写一个简易的Promise。'
---

## Promise是什么？

> 想象一下，你是一位顶尖歌手，粉丝没日没夜地询问你下首歌什么时候发。
>
> 为了从中解放，你承诺（promise）会在单曲发布的第一时间发给他们。你给了粉丝们一个列表。他们可以在上面填写他们的电子邮件地址，以便当歌曲发布后，让所有订阅了的人能够立即收到。即便遇到不测，例如录音室发生了火灾，以致你无法发布新歌，他们也能及时收到相关通知。
>
> 每个人都很开心：你不会被任何人催促，粉丝们也不用担心错过歌曲发行。
>
> 这是我们在编程中经常遇到的事儿与真实生活的类比：
>
> - “生产者代码（producing code）”会做一些事儿，并且会需要一些时间。例如，通过网络加载数据的代码。它就像一位“歌手”。
> - “消费者代码（consuming code）”想要在“生产者代码”完成工作的第一时间就能获得其工作成果。许多函数可能都需要这个结果。这些就是“粉丝”。
> - Promise 是将“生产者代码”和“消费者代码”连接在一起的*一个特殊的 JavaScript 对象*。用我们的类比来说：这就是就像是“订阅列表”。“生产者代码”花费它所需的任意长度时间来产出所承诺的结果，而 “promise” 将在它（译注：指的是“生产者代码”，也就是下文所说的 executor）准备好时，将结果向所有订阅了的代码开放。

这段话很形象地解释了`Promise`的概念，它能够很好地支持异步编程，避免了回调地狱的问题。

接着来看下面这样一个例子：

```javascript
const fetchValue = fetch('/api/get-data')

console.log(fetchValue)
```

当我们调用`fetch`时，它会启动网络请求，这是一个异步操作，JavaScript线程不会停止并等待，代码继续运行。

但是`fetch()`函数实际返回了什么？不可能是请求的结果，因为请求还没完成。实际上它返回一个`Promise`对象，你可以把它理解为一张借条，上面写着“*我承诺会在未来的某个时间点给你一个结果*”。

## Promise的基本特点

### executor

`Promise`是一个构造函数，传递给`new Promise`的函数被称为`executor`，*当`Promise`对象被创建时，`executor`会立即执行*。

当`executor`获得了结果，就会调用以下回调之一：- `resolve(value)`：如果操作成功完成，带有结果`value`。- `reject(error)`：如果出现错误，带有错误`error`。

### `Promise`对象的三种状态

`Promise`始终处于以下三种状态之一：

1. `pending`：初始状态，既不是成功，也不是失败状态。
2. `fulfilled`：意味着操作成功完成。
3. `rejected`：意味着操作失败。

状态改变只能是`pending`->`fulfilled`或者`pending`->`rejected`，状态一旦改变就不能再变。

简单总结一下，由`new Promise`构造器返回的`promise`对象具有以下内部属性：

- state: 最开始是`pending`，然后变为`fulfilled`或`rejected`。
- result: 最开始是`undefined`，然后在`resolve(value)`被调用时变为`value`，在`reject(error)`被调用时变为`error`。

### then()和catch()方法

我们希望在`Promise`对象状态改变时执行一些操作，这时我们可以使用`then()`方法，它接收两个参数：`onFulfilled`和`onRejected`，分别对应`Promise`对象状态变为`fulfilled`和`rejected`时的回调函数。
我们也可以只传入一个参数，这个参数既是`onFulfilled`也是`onRejected`。

如果`Promise`对象状态变为`rejected`，我们也可以使用`catch()`方法来处理错误。

```javascript
fetch('/api/get-data')
  .then((response) => {
    console.log(response)
    // Response { type: 'basic', status: 200, ...}
  })
  .catch((error) => {
    console.log(error)
  })
```

> [!Tip]
> 如果您以前使用过 Fetch API，您可能已经注意到需要第二步，才能以 JSON 格式实际获取我们需要的数据：
>
> ```javascript
> fetch('/api/get-data')
>   .then((response) => {
>     return response.json()
>   })
>   .then((data) => {
>     console.log(data)
>   })
> ```
>
> *这是因为`response.json()`也返回一个`Promise`对象，所以我们需要再次使用`then()`方法*。
>
> `fetch()`产生的`promise`在浏览器从服务器接收到第一个字节时就会`resolve`，然后返回`response.json`的`promise`，这个`promise`在浏览器接收到整个响应并解析完JSON时才会`resolve`。

## 手写简易的Promise

根据目前的理解，我们可以尝试手写一个简易的`Promise`。

```javascript
// 定义三种状态
const state = {
  PENDING: 'pending',
  FULFILLED: 'fulfilled',
  REJECTED: 'rejected',
}

class MyPromise {
  // 构造函数，在new Promise的时候立刻执行，通常需要花费一些时间
  constructor(executor) {
    this.state = state.PENDING // 初始化状态为pending
    this.value = undefined // 初始化value为undefined
    this.onFulfilledCbs = [] // 存放onFulfilled回调函数,因为可能有多个then
    this.onRejectedCbs = [] // 存放onRejected回调函数

    try {
      executor(this.resolve, this.reject) //立刻执行executor
    } catch (error) {
      this.reject(error) // 如果执行executor时出错，直接reject
    }
  }

  runAllCallbacks = () => {
    // 如果状态是fulfilled，依次执行存入的onFulfilled函数们
    if (this.state === state.FULFILLED) {
      this.onFulfilledCbs.forEach((cb) => cb(this.value))
      this.onFulfilledCbs = [] // 执行完后清空
    }
    // 如果状态是rejected，依次执行存入的onRejected函数们
    if (this.state === state.REJECTED) {
      this.onRejectedCbs.forEach((cb) => cb(this.value))
      this.onRejectedCbs = []
    }
  }

  // resolve函数，因为使用箭头函数，所以这里的this指向MyPromise实例；如果使用普通函数，需要在new Promise时绑定this
  resolve = (value) => {
    if (this.state !== state.PENDING) return

    if (value instanceof MyPromise) {
      value.then(this.resolve, this.reject)
      return
    }
    this.state = state.FULFILLED
    this.value = value
    this.runAllCallbacks()
  }

  // reject函数
  reject = (error) => {
    if (this.state !== state.PENDING) return

    if (error instanceof MyPromise) {
      error.then(this.resolve, this.reject)
      return
    }
    this.state = state.REJECTED
    this.value = error
    this.runAllCallbacks()
  }

  // then方法，接受两个参数，onFulfilled和onRejected
  then(onFulfilled, onRejected) {
    if (onFulfilled == null && onRejected == null) return

    if (this.state == state.FULFILLED) {
      onFulfilled(this.value)
    }
    if (this.state == state.REJECTED) {
      onRejected(this.value)
    }

    if (this.state == state.PENDING) {
      this.onFulfilledCbs.push(onFulfilled)
      this.onRejectedCbs.push(onRejected)
    }

    this.runAllCallbacks()
  }

  catch(onRejected) {
    this.then(null, onRejected)
  }
}

// 测试
const myPromise = new MyPromise((resolve, reject) => {
  console.log(this, 'this')
  setTimeout(() => {
    resolve('Hello, Promise!') // 1秒后resolve
  }, 1000)
})

// 多个then，每个then都接收了一个回调函数，这些回调函数会被存入对应的数组，等待resolve或reject后依次执行
myPromise.then((value) => {
  console.log('First then:', value) // 1秒后输出Hello, Promise!
})

myPromise.then((value) => {
  console.log('Second then:', value) // 1秒后输出Hello, Promise!
})

myPromise.then((value) => {
  console.log('Third then:', value) // 1秒后输出Hello, Promise!
})
```

## 链式调用

promise还有一个重要的特性，就是可以链式调用。这是因为在官方的`Promise`实现中，每次调用`then`方法都会返回一个新的`Promise`对象。
正如我们前面看过的`fetch`的例子，`response.json()`也返回一个`Promise`对象，所以我们可以继续使用`then`方法。

```javascript
fetch('/api/get-data')
  .then((response) => {
    return response.json()
  })
  .then((data) => {
    console.log(data)
  })
```

那么我们怎么修改上面自己实现的简易`Promise`，使其支持链式调用呢？

首先在`then`方法这个地方要做个改装，让它总是返回一个`promise`

```javascript
// then方法，接受两个参数，onFulfilled和onRejected，告诉Promise状态变为fulfilled或rejected时该如何处理
  then(onFulfilled, onRejected) {
    // 无论如何，then方法都会返回一个新的Promise
    return new MyPromise((resolve, reject) => {
     const fulfilledCb = (value) => {
        try {
          const result = onFulfilled ? onFulfilled(value) : value // 如果onFulfilled存在，执行onFulfilled，否则直接返回value
          if (result instanceof MyPromise) {
            // 如果onFulfilled返回的是Promise实例，继续then，等待resolve或reject
            result.then(resolve, reject)
          } else {
            // 如果不是Promise实例，直接resolve
            resolve(result)
          }
        } catch (error) {
          reject(error)
        }
      }

      const rejectedCb = (error) => {
        try {
          const result = onRejected ? onRejected(error) : error // 如果没有传入onRejected，直接返回error
          if (result instanceof MyPromise) {
            result.then(resolve, reject)
          } else {
            resolve(result)
          }
        } catch (error) {
          reject(error)
        }
      }

      if(this.status == STATE.PENDING) {
        this.onFulfilledCbs.push(fulfilledCb)
        this.onRejectedCbs.push(rejectedCb)
      }
      if(this.status == STATE.FULFILLED) {
        queueMicrotask(()=> fulfilledCb(this.value))
      }
      if (this.status == STATE.REJECTED) {
        queueMicrotask(()=> rejectedCb(this.value))
      }

    })
  }
```
> 参考 [[queueMicrotask()]]

测试：

```javascript
const myPromise = new MyPromise((resolve, reject) => {
  console.log(this, 'this')
  setTimeout(() => {
    resolve('Hello, Promise!') // 1秒后resolve
  }, 1000)
})

myPromise
  .then((value) => {
    console.log('First then:', value) // 1秒后输出Hello, Promise!
    return 'First then'
  })
  .then((value) => {
    console.log('Second then:', value) // 1秒后输出First then
    return 'Second then'
  })
  .then((value) => {
    console.log('Third then:', value) // 1秒后输出Second then
    return 'Third then'
  })
```

还有一个可以改善的地方，就是给`resolve`和`reject`加上`setTimeout`或`queueMicrotask`，这样可以保证`then`方法总是在`resolve`或`reject`之后执行。

`setTimeout`用的是宏任务队列，`queueMicrotask`用的是微任务队列，微任务队列的优先级高于宏任务队列，所以`queueMicrotask`会更好点。

## finally

finally的作用是在**上一个Promise**结束后，无论结果是fulfilled还是rejected，都会执行一段代码。它接收一个回调函数，不接收参数，也不关心Promise的状态。

```javascript
// 例子：
const myPromise = new MyPromise((resolve, reject) => {
  console.log(this, 'this')
  setTimeout(() => {
    resolve('Hello, Promise!') // 1秒后resolve
  }, 1000)
})

myPromise
  .then((value) => {
    console.log('First then:', value) // 1秒后输出Hello, Promise!
    return 'First then'
  })
  .finally(() => {
    console.log('Finally!') // 1秒后输出Finally!
  })
  .then((value) => {
    console.log('Second then:', value) // 1秒后输出First then
    return 'Second then'
  })
```

在我们的`MyPromise`添加`finally`方法：

```javascript
  finally(onFinally) {
    return this.then(
      (value) => {
        onFinally()
        return value
      },
      (error) => {
        onFinally()
        throw error
      }
    )
  }
```

## resolve and reject

`Promise` 里还有一个静态方法`Promise.resolve`和`Promise.reject`，它们分别返回一个`Promise`对象，`Promise.resolve`返回一个`fulfilled`状态的`Promise`对象，`Promise.reject`返回一个`rejected`状态的`Promise`对象。

```javascript
// 例子：
const resolvedPromise = MyPromise.resolve('Hello, Promise!')
resolvedPromise.then((value) => {
  console.log(value) // Hello, Promise!
})

const rejectedPromise = MyPromise.reject('Error!')
rejectedPromise.catch((error) => {
  console.log(error) // Error!
})
```

在`MyPromise`中添加`resolve`和`reject`方法：

```javascript
  static resolve(value) {
    return new MyPromise((resolve) => {
      resolve(value)
    })
  }

  static reject(error) {
    return new MyPromise((_, reject) => {
      reject(error)
    })
  }
```

## all

`Promise.all`接收一个`Promise`对象数组，当所有`Promise`对象都变为`fulfilled`状态时，返回一个`fulfilled`状态的`Promise`对象，结果是一个数组，数组的顺序和传入的`Promise`对象数组的顺序一致。

```javascript
// 例子：
const promise1 = new MyPromise((resolve) => {
  setTimeout(() => {
    resolve('Promise 1')
  }, 1000)
})

const promise2 = new MyPromise((resolve) => {
  setTimeout(() => {
    resolve('Promise 2')
  }, 2000)
})

MyPromise.all([promise1, promise2]).then((values) => {
  console.log(values) // ['Promise 1', 'Promise 2']
})
```

在`MyPromise`中添加`all`方法：

```javascript
  static all(promises) {
    return new MyPromise((resolve, reject) => {
      const results = []
      let count = 0

      promises.forEach((promise, index) => {
        promise.then((value) => {
          results[index] = value
          count++
          if (count === promises.length) {
            resolve(results)
          }
        }).catch(reject)
      })
    })
    }
```

## allSettled

`Promise.allSettled`接收一个`Promise`对象数组，当所有`Promise`对象都变为`fulfilled`或`rejected`状态时，返回一个`fulfilled`状态的`Promise`对象，结果是一个数组，数组的顺序和传入的`Promise`对象数组的顺序一致。

```javascript
// 例子：
const promise1 = new MyPromise((resolve) => {
  setTimeout(() => {
    resolve('Promise 1')
  }, 1000)
})

const promise2 = new MyPromise((_, reject) => {
  setTimeout(() => {
    reject('Promise 2')
  }, 2000)
})

MyPromise.allSettled([promise1, promise2]).then((values) => {
  console.log(values) // [{ status: 'fulfilled', value: 'Promise 1' }, { status: 'rejected', reason: 'Promise 2' }]
})
```

在`MyPromise`中添加`allSettled`方法：

```javascript
  static allSettled(promises) {
    return new MyPromise((resolve) => {
      const results = []
      let count = 0

      promises.forEach((promise, index) => {
        promise.then(
          (value) => {
            results[index] = { status: STATE.FULFILLED, value }
          },
          (reason) => {
            results[index] = { status: STATE.REJECTED, reason }
          }
        ).finally(() => {
          count++
          if (count === promises.length) {
            resolve(results)
          }
        })
      })
    })
    }

```

## race

`Promise.race`接收一个`Promise`对象数组，当其中任意一个`Promise`对象变为**`fulfilled`或`rejected`**状态时，返回一个`fulfilled`状态的`Promise`对象，结果是第一个变为`fulfilled`或`rejected`状态的`Promise`对象的结果。

```javascript
// 例子：
const promise1 = new MyPromise((resolve) => {
  setTimeout(() => {
    resolve('Promise 1')
  }, 1000)
})

const promise2 = new MyPromise((_, reject) => {
  setTimeout(() => {
    reject('Promise 2')
  }, 2000)
})

MyPromise.race([promise1, promise2]).then((value) => {
  console.log(value) // Promise 1
})
```

在`MyPromise`中添加`race`方法：

```javascript
static race(promises) {
    return new MyPromise((resolve, reject) => {
      promises.forEach((promise) => {
        promise.then(resolve).catch(reject)
      })
    })
  }
```

## any

`Promise.any`接收一个`Promise`对象数组，当其中任意一个`Promise`对象变为`fulfilled`状态时，返回一个`fulfilled`状态的`Promise`对象，结果是第一个变为`fulfilled`状态的`Promise`对象的结果。如果所有`Promise`对象都变为`rejected`状态，返回一个`rejected`状态的`Promise`对象，结果是所有`Promise`对象的错误。

```javascript
// 例子：
const promise1 = new MyPromise((_, reject) => {
  setTimeout(() => {
    reject('Promise 1')
  }, 1000)
})

const promise2 = new MyPromise((resolve) => {
  setTimeout(() => {
    resolve('Promise 2')
  }, 2000)
})

MyPromise.any([promise1, promise2]).then((value) => {
  console.log(value) // Promise 2
})
```

在`MyPromise`中添加`any`方法：

```javascript
  static any(promises) {
    return new MyPromise((resolve, reject) => {
      let count = 0

      promises.forEach((promise) => {
        promise.then(resolve).catch((error) => {
          count++
          if (count === promises.length) {
            reject(error)
          }
        })
      })
    })
  }
```

## 使用promise如何避免回调地狱？

### Promise如何避免回调地狱？

什么是回调地狱？回调地狱是指多个回调函数嵌套调用，导致代码难以阅读和维护。例如：

```javascript
console.log(3)
setTimeout(() => {
  console.log(2)
  setTimeout(() => {
    console.log(1)
    setTimeout(() => {
      console.log('Happy New Year!')
    }, 1000)
  }, 1000)
}, 1000)
```

上述代码实现了一个新年倒计时，这是一个典型的回调地狱。使用`Promise`可以很好地解决这个问题，代码变得更加清晰易读：

```javascript
const countdown = (time, message) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      console.log(message)
      resolve()
    }, time)
  })
}

countdown(1000, 3)
  .then(() => countdown(1000, 2))
  .then(() => countdown(1000, 1))
  .then(() => console.log('Happy New Year!'))
```

如果再使用`async`和`await`，代码会更加简洁：

```javascript
const countdown = (time, message) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      console.log(message)
      resolve()
    }, time)
  })
}

const newYear = async () => {
  await countdown(1000, 3)
  await countdown(1000, 2)
  await countdown(1000, 1)
  console.log('Happy New Year!')
}
```

## 参考资料

[1] https://zh.javascript.info/promise-basics
[2] https://www.joshwcomeau.com/javascript/promises/
[3] https://www.youtube.com/watch?v=1l4wHWQCCIc&t=448s
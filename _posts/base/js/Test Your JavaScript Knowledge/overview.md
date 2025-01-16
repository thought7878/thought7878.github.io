[原视频](https://frontendmasters.com/courses/javascript-quiz/)

![[_posts/base/js/Test Your JavaScript Knowledge/media/cd3d59d272f0a30292f4405bb7446e47_MD5.jpeg]]

## Table of Contents

Section Duration: 2 hours, 11 minutes

### [Introduction](https://frontendmasters.com/courses/javascript-quiz/introduction/)
[00:00:00 - 00:00:52](https://frontendmasters.com/courses/javascript-quiz/introduction/)

Lydia Hallie introduces a Q&A style workshop where they present 50 questions on various topics in JavaScript, including event loop, closures, classes, prototypes, and miscellaneous content like numbers and weak maps. Students will be given time to answer each question, and Lydia provides detailed explanations for each answer. The course aims to enhance participants' understanding of JavaScript concepts.

### [Event Loop & Task Queue](https://frontendmasters.com/courses/javascript-quiz/event-loop-task-queue/)
  [00:00:53 - 00:18:08](https://frontendmasters.com/courses/javascript-quiz/event-loop-task-queue/)
  Lydia explains the concept of the event loop in JavaScript. They discuss the different components of the event loop, such as the call stack, task queue, and microtask queue. They also provide examples and explanations of how different tasks and callbacks are scheduled and executed in the event loop.
  
### [Scope & Closure](https://frontendmasters.com/courses/javascript-quiz/scope-closure/)
  [00:18:09 - 00:39:45](https://frontendmasters.com/courses/javascript-quiz/scope-closure/)
  Lydia explains the concept of scope and closures in JavaScript. They discuss how closures work by referencing lexical environments and demonstrate examples of closures in code. The instructor also covers topics such as hoisting, variable declaration, and block scope.
  
### [this keyword](https://frontendmasters.com/courses/javascript-quiz/this-keyword/)
  [00:39:46 - 00:57:04](https://frontendmasters.com/courses/javascript-quiz/this-keyword/)
  Lydia explains the behavior of the "this" keyword in JavaScript. They cover various scenarios, including the global context, regular functions, arrow functions, classes, strict mode, event handlers, and the use of call, bind, and apply methods.
  
### [Classes & Prototypes](https://frontendmasters.com/courses/javascript-quiz/classes-prototypes/)
  [00:57:05 - 01:09:59](https://frontendmasters.com/courses/javascript-quiz/classes-prototypes/)
  Lydia explains the concept of classes and prototypes in JavaScript. They demonstrate how classes can be extended and how properties and methods can be added to the prototype. They also discuss the difference between properties defined in the constructor and those added to the prototype. The lesson includes examples and explanations of how prototypes work and how they affect memory usage. The instructor also covers topics such as static methods and the use of object.create to create objects with specific prototypes.
  
### [Generators & Iterators](https://frontendmasters.com/courses/javascript-quiz/generators-iterators/)
  [01:10:00 - 01:20:03](https://frontendmasters.com/courses/javascript-quiz/generators-iterators/)
  Lydia demonstrates how to create a generator function using the asterisk symbol and explains that a generator function returns an iterator object. The instructor also shows how to use the iterator object to call the next method and retrieve the yielded values. They discuss the difference between regular functions and generator functions, highlighting the ability to pause and resume execution with generator functions. Additionally, the instructor explains how to create custom iterable objects in JavaScript by implementing the symbol.iterator method.
  
### [Garbage Collection](https://frontendmasters.com/courses/javascript-quiz/garbage-collection/)
  [01:20:04 - 01:36:23](https://frontendmasters.com/courses/javascript-quiz/garbage-collection/)
  Lydia discusses various concepts related to garbage collection in JavaScript. They explain how objects are garbage collected based on their availability in the global lexical environment and how references to objects affect their garbage collection. The instructor also explains the concept of weak maps and how they differ from regular maps in terms of garbage collection. They also discuss closures and when inner functions become eligible for garbage collection. The lesson concludes with a discussion on the event loop and how it interacts with garbage collection.
  
### [Modules](https://frontendmasters.com/courses/javascript-quiz/modules/)
  [01:36:24 - 01:45:14](https://frontendmasters.com/courses/javascript-quiz/modules/)
  Lydia discusses different module loading mechanisms such as CommonJS, AMD, UMD, and ESM. They explain the characteristics and use cases of each mechanism, including their support for server-side or client-side usage, synchronous or asynchronous loading, and static analysis and tree shaking capabilities. The instructor also demonstrates the order of logs when using a combination of require and import statements in a script. Finally, they compare the differences between require and import, highlighting their conditional usage, hoisting behavior, synchronous or asynchronous loading, and support for static analysis and tree shaking.
  
### [Numbers](https://frontendmasters.com/courses/javascript-quiz/numbers/)
  [01:45:15 - 01:50:33](https://frontendmasters.com/courses/javascript-quiz/numbers/)
  Lydia explains the behavior of the post-increment operator and pre-increment operator, as well as the difference between using the "==" and "===" operators. They also discuss the "isNaN" function and the "toString" method for numbers. The lesson concludes with a discussion on the "parseInt" and "parseFloat" functions and how to use the "toString" method with different radix values.
  
### [Miscellaneous](https://frontendmasters.com/courses/javascript-quiz/miscellaneous/)
  [01:50:34 - 02:11:12](https://frontendmasters.com/courses/javascript-quiz/miscellaneous/)
  Lydia covers various topics including working with maps and sets, freezing objects, using proxies, manipulating arrays, working with default parameters, comparing symbols, and using tagged template literals. The instructor explains the concepts and provides examples to illustrate how they work.
  
### [Wrapping Up](https://frontendmasters.com/courses/javascript-quiz/wrapping-up/)
  [02:11:13 - 02:11:40](https://frontendmasters.com/courses/javascript-quiz/wrapping-up/)
  Lydia wraps up the course by emphasizing that the goal was to remind students of certain concepts and provide resources for further learning. The instructor also encourages participants to reach out with any additional questions or concerns.

# Event Loop

## Question 1

## Question 2

![[_posts/base/js/Test Your JavaScript Knowledge/media/e52c5fb299dc374f7df949d12afccf97_MD5.jpeg]]

## Question 3

疑问：这里的 async await 的执行？参考 [[async & await]]、[[20.async、await-使用同步的方式去写异步代码]]

async 函数也是同步的，直到它命中 await 关键字，剩下的部分是异步的部分

![[_posts/base/js/Test Your JavaScript Knowledge/media/924d3c5b20dd24b95f8e7482df9a0739_MD5.jpeg]]

4 5 1 2 8 7 6 3

```js
async function asyncFunction() {
  console.log(1);
  new Promise(() => console.log(2));
  await new Promise((res) => {
    setTimeout(() => console.log(3), 0);
    res();
  });
}

new Promise((res) => {
  console.log(4);
  (async () => {
    console.log(5);
    await asyncFunction();
    console.log(6);
  })();
  res();
}).then(() => console.log(7));

console.log(8);
```

以下是对这段代码执行流程的详细解释：

**一、全局代码开始执行**：

1. 首先，定义了一个名为 `asyncFunction` 的异步函数。
2. 然后，创建一个新的 `Promise` 对象（从第 10 行开始）。
   - 当这个 `Promise` 对象被创建时，它的构造函数被调用，立即执行 `console.log(4)`，将 `4` 输出到控制台。
   - 接着，一个立即执行的异步函数表达式（从第 12 行开始）被调用。
     - 在这个函数中，`console.log(5)` 被执行，将 `5` 输出到控制台。

**二、调用 `asyncFunction` 函数**：

1. 调用 `asyncFunction` 函数。
   - 首先执行 `console.log(1)`，将 `1` 输出到控制台。
   - 然后创建一个新的 `Promise` 对象（第 3 行）。
     - 这里代码存在错误，正确的写法应该是 `new Promise(() => { console.log(2); });`。按照修正后的代码，会执行 `console.log(2)`，将 `2` 输出到控制台。
   - 接着，遇到 `await new Promise((res) => {...})`（第 4 行）。
     - 这里创建了一个新的 `Promise` 对象，其中包含一个 `setTimeout` 函数，它会将 `console.log(3)` 的执行安排在 0 毫秒后，将其放入宏任务队列。
     - 同时，调用 `res()` 方法，将这个 `Promise` 对象的状态设置为已解决。
     - `await` 会暂停 `asyncFunction` 函数的执行，直到内部的 `Promise` 被解决。

**三、继续执行外层 `Promise` 的代码**：

1. 回到外层的 `Promise` 对象。
   - 由于 `asyncFunction` 函数内部的 `await` 暂停了它的执行，外层 `Promise` 对象的构造函数继续执行。
   - 调用 `res()` 方法，将外层 `Promise` 的状态设置为已解决，将 `.then(() => console.log(7))` 的回调函数添加到微任务队列。
   - 然后执行 `console.log(8)`，将 `8` 输出到控制台。

**四、处理微任务队列**：

1. 当调用栈为空时，事件循环开始处理微任务队列。
   - 首先执行 `.then(() => console.log(7))` 的回调函数，将 `7` 输出到控制台。

**五、处理宏任务队列**：

1. 微任务队列处理完毕后，事件循环开始处理宏任务队列。
   - 执行之前放入宏任务队列的 `setTimeout` 回调函数，将 `3` 输出到控制台。

**六、完成 `asyncFunction` 函数的执行**：

1. `asyncFunction` 函数继续执行 `await` 之后的代码。
   - 执行 `console.log(6)`，将 `6` 输出到控制台。

**修正后的代码**：

```javascript
async function asyncFunction() {
  console.log(1);
  new Promise(() => console.log(2));
  await new Promise((res) => {
    setTimeout(() => console.log(3), 0);
    res();
  });
}

new Promise((res) => {
  console.log(4);
  (async () => {
    console.log(5);
    await asyncFunction();
    console.log(6);
  })();
  res();
}).then(() => console.log(7));

console.log(8);
```

**代码解释**：

- `asyncFunction` 是一个异步函数，使用 `async` 关键字定义。
  - 它首先输出 `1`，然后创建一个 `Promise` 对象，输出 `2`。
  - 遇到 `await` 时，暂停函数的执行，等待内部 `Promise` 解决。
  - 内部 `Promise` 中使用 `setTimeout` 安排 `console.log(3)` 稍后执行，并调用 `res()` 解决 `Promise`。
- 外层 `Promise` 对象在创建时输出 `4`，并执行一个立即执行的异步函数。
  - 这个异步函数输出 `5`，调用 `asyncFunction`，输出 `1`、`2` 等，在 `asyncFunction` 中暂停，等待内部 `Promise`。
  - 调用 `res()` 解决外层 `Promise`，将 `.then` 回调添加到微任务队列。
  - 然后输出 `8`。
- 事件循环先处理微任务，输出 `7`，再处理宏任务，输出 `3`，最后完成 `asyncFunction` 执行，输出 `6`。

总结来说，代码的输出顺序将是 `4`、`5`、`1`、`2`、`8`、`7`、`3`、`6`。

请注意，代码中第 3 行存在错误，已在上述解释中指出正确的写法。通过 `async` 和 `await` 以及 `Promise` 对象，结合事件循环的微任务和宏任务队列，实现了复杂的异步执行顺序控制。

## Question 4

![[_posts/base/js/Test Your JavaScript Knowledge/media/231c7007eb5d0baa5222b1012c690f69_MD5.jpeg]]

# Scope & Closure

![[_posts/base/js/知识点总结/media/fdf63eeb9e479fa88d722db0d5e06396_MD5.jpeg]]

![[_posts/base/js/知识点总结/media/326c90c1cb44b6a8b98e9e70e2fc5130_MD5.jpeg]]

## Question 5

![[_posts/base/js/Test Your JavaScript Knowledge/media/461f876a39155470785197f6fd7c156f_MD5.jpeg]]

## Question 6

_调用函数就会执行`Call`属性，创建新的 Execution Context（包含新的 Lexical Environment）_。注意下面的第三个 log 的 createCounter 的重新调用。

![[_posts/base/js/Test Your JavaScript Knowledge/media/011147cfac2cbdb93d96f5fde7f097c8_MD5.jpeg]]

## Question 7

![[_posts/base/js/Test Your JavaScript Knowledge/media/f01dce938ae444e3214e1cf40eb1225f_MD5.jpeg]]

## Question 8

![[_posts/base/js/Test Your JavaScript Knowledge/media/d51d9e685ed43437e26ed921ff7e4dc5_MD5.jpeg]]

## Question 9

视频中有详细的画图解释：

- let、const 与 var 的区别
- 函数声明与函数表达式的区别
- 函数的 creation 和 execute 阶段

![[_posts/base/js/Test Your JavaScript Knowledge/media/7cecef93d7dc92b954e07afe09615bf9_MD5.jpeg]]

![[_posts/base/js/Test Your JavaScript Knowledge/media/2e1173703ca4ae85a53ce3513ecadc0b_MD5.jpeg]]

![[_posts/base/js/Test Your JavaScript Knowledge/media/0f04e76f9f1589589f28619098dc4130_MD5.jpeg]]

## Question 10

[[_posts/base/js/Test Your JavaScript Knowledge/media/c49c825b981e018ad69728808c3e4f57_MD5.jpeg|Open: Pasted image 20250116131643.png]]
![[_posts/base/js/Test Your JavaScript Knowledge/media/c49c825b981e018ad69728808c3e4f57_MD5.jpeg]]

# this keyword
深入探讨了JavaScript的 “this” 关键字的复杂性，**探索它在各种上下文中的行为，包括全局范围、对象、常规函数、箭头函数、类和事件处理程序**。 它强调了 “this” 值的确定，突出了传统函数和箭头函数在 “this” 方面的显著差异。 严格模式对 “this” 、事件处理程序中的 “this” 以及类的方法的影响。 

**全局作用域**

![[_posts/base/js/Test Your JavaScript Knowledge/media/25d2c41b766550da8b3fe59659cf4fc4_MD5.jpeg]]

**常规函数**

![[_posts/base/js/Test Your JavaScript Knowledge/media/ad75131b58c27ea9553090f51a968097_MD5.jpeg]]

**箭头函数**

![[_posts/base/js/Test Your JavaScript Knowledge/media/034181e60bc0ed14ea6356ddd9100b59_MD5.jpeg]]




## Question 11


## Question 12

## Question 13

## Question 14

# Classes & Prototypes
介绍了原型和继承，阐明了它们如何促进*对象实例之间的共享属性和方法*。 



# Generators & Iterators

对话最后涉及了生成器和迭代器，预示着即将到来的关于这些主题的讨论，同时解决了关于 “这” 的常见误解，并加强了JavaScript中的面向对象编程原则。

## Question 15

## Question 16

## Question 17

## Question 18

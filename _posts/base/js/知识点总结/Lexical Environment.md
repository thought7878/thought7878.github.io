在 JavaScript 中，词法环境（Lexical Environment）是一个至关重要的概念，它是理解变量作用域、闭包等特性的基础。下面将从多个方面对其进行详细介绍。

### 定义

**词法环境**是一种规范类型，用于定义标识符（变量和函数名）与具体值之间的关联。简单来说，它就像是一个容器，用来*存储变量和函数的定义，并且能够追踪这些变量和函数的作用域*。

### 结构

词法环境主要由两部分组成：

1. **环境记录（Environment Record）**：这是一个存储变量和函数定义的地方。根据词法环境的类型不同，环境记录也分为不同的类型：
   - **声明式环境记录（Declarative Environment Record）**：用于函数作用域和块级作用域，它可以存储变量（如使用 `let`、`const` 声明的变量）、函数声明和类声明等。
   - **对象式环境记录（Object Environment Record）**：主要用于全局作用域，在浏览器环境中，它通常与全局对象（`window`）相关联，存储全局变量和全局函数。
2. **对外部词法环境的引用（Outer Lexical Environment Reference）**：这是一个指向外部词法环境的引用，通过这个引用，词法环境可以形成一个链表结构，也就是作用域链。当在当前词法环境中找不到某个变量时，JavaScript 引擎会通过这个引用到外部词法环境中继续查找。

### 类型

1. **全局词法环境（Global Lexical Environment）**：这是最外层的词法环境，在程序开始执行时就会被创建。它包含全局变量和全局函数的定义，并且其外部词法环境引用为 `null`。在浏览器环境中，全局词法环境的环境记录与全局对象 `window` 紧密相关。
2. **函数词法环境（Function Lexical Environment）**：每当调用一个函数时，就会创建一个新的函数词法环境。它包含函数内部的变量、参数以及内部函数的定义，其外部词法环境引用指向函数定义时所在的词法环境。这就是闭包能够访问外部函数变量的原理。
3. **块级词法环境（Block Lexical Environment）**：使用 `let` 和 `const` 声明变量时会创建块级词法环境，块级作用域可以是 `if` 语句块、`for` 循环块等。块级词法环境可以限制变量的作用范围，避免变量提升带来的问题。

### 示例代码及分析

```javascript
// 全局词法环境
let globalVar = "I am a global variable";

function outerFunction() {
  // 函数词法环境
  let outerVar = "I am from outer function";

  function innerFunction() {
    // 函数词法环境
    let innerVar = "I am from inner function";
    console.log(globalVar);
    console.log(outerVar);
    console.log(innerVar);
  }

  return innerFunction;
}

let closure = outerFunction();
closure();
```

- **全局词法环境**：包含 `globalVar` 变量和 `outerFunction` 函数的定义，其外部词法环境引用为 `null`。
- **`outerFunction` 函数词法环境**：当调用 `outerFunction` 时创建，包含 `outerVar` 变量和 `innerFunction` 函数的定义，其外部词法环境引用指向全局词法环境。
- **`innerFunction` 函数词法环境**：当调用 `innerFunction` 时创建，包含 `innerVar` 变量的定义，其外部词法环境引用指向 `outerFunction` 的词法环境。
- **闭包**：当 `outerFunction` 执行完毕返回 `innerFunction` 时，`innerFunction` 作为闭包保留了对 `outerFunction` 词法环境的引用。因此，当调用 `closure()` 时，`innerFunction` 仍然可以访问 `outerFunction` 中的 `outerVar` 变量。

### 与执行上下文的关系

执行上下文是一个更高级的概念，它包含词法环境、变量对象（在 ES6 中词法环境替代了部分变量对象的功能）和 `this` 指针。可以说，词法环境是执行上下文的一个重要组成部分，它负责管理变量和函数的作用域。在代码执行过程中，JavaScript 引擎会根据执行上下文栈来切换不同的执行上下文，而每个执行上下文都有自己的词法环境来处理变量的查找和作用域问题。

---
layout: post
title: JavaScript Questions
subtitle:
categories: nextJS
tags: [JavaScript, Questions]
# top: 2
---

- Explain event delegation.解释事件委托

- Explain how `this` works in JavaScript.解释  `this`  在 JavaScript 中如何工作。
  
  - Can you give an example of one of the ways that working with `this` has changed in ES6?您能举例说明 ES6 中使用  `this`  的方式发生了变化吗？

- Explain how prototypal inheritance works.解释原型继承如何工作。

- What's the difference between a variable that is: `null`, `undefined` or undeclared?`null` 、 `undefined`  或未声明的变量之间有什么区别？
  
  - How would you go about checking for any of these states?您将如何检查这些状态？

- What is a closure, and how/why would you use one?什么是闭包，以及如何/为什么使用闭包？

- What language constructions do you use for iterating over object properties and array items?您使用什么语言结构来迭代对象属性和数组项？

- Can you describe the main difference between the `Array.forEach()` loop and `Array.map()` methods and why you would pick one versus the other?您能否描述一下  `Array.forEach()`  循环和  `Array.map()`  方法之间的主要区别以及为什么您会选择其中一种方法？

- What's a typical use case for anonymous functions?What's a typical use case for anonymous functions?

- What's the difference between host objects and native objects?宿主对象和本机对象有什么区别？

- Explain the difference between: `function Person(){}`, `var person = Person()`, and `var person = new Person()`?解释一下： `function Person(){}` 、 `var person = Person()`  和  `var person = new Person()`  之间的区别？

- Explain the differences on the usage of `foo` between `function foo() {}` and `var foo = function() {}`解释一下  `function foo() {}`  和  `var foo = function() {}`  之间  `foo`  用法的差异

- Can you explain what `Function.call` and `Function.apply` do? What's the notable difference between the two?你能解释一下  `Function.call`  和  `Function.apply`  的作用吗？两者之间的显着区别是什么？

- Explain `Function.prototype.bind`.解释  `Function.prototype.bind`

- What's the difference between feature detection, feature inference, and using the UA string?特征检测、特征推断和使用 UA 字符串之间有什么区别？

- Explain "hoisting".解释一下“吊装”。

- What is type coercion? What are common pitfalls of relying on type coercion in JavaScript code?什么是类型强制？ JavaScript 代码中依赖类型强制有哪些常见陷阱？

- Describe event bubbling.描述事件冒泡。

- Describe event capturing.描述事件捕获。

- What's the difference between an "attribute" and a "property"?“属性”和“属性”有什么区别？

- What are the pros and cons of extending built-in JavaScript objects?扩展内置 JavaScript 对象的优点和缺点是什么？

- What is the difference between `==` and `===`?`==`  和  `===`  之间有什么区别？

- Explain the same-origin policy with regards to JavaScript.解释有关 JavaScript 的同源策略。

- Why is it called a Ternary operator, what does the word "Ternary" indicate?为什么叫三元运算符，“三元”这个词表示什么？

- What is strict mode? What are some of the advantages/disadvantages of using it?什么是严格模式？使用它有哪些优点/缺点？

- What are some of the advantages/disadvantages of writing JavaScript code in a language that compiles to JavaScript?使用可编译为 JavaScript 的语言编写 JavaScript 代码有哪些优点/缺点？

- What tools and techniques do you use debugging JavaScript code?您使用哪些工具和技术来调试 JavaScript 代码？

- Explain the difference between mutable and immutable objects.解释可变对象和不可变对象之间的区别。
  
  - What is an example of an immutable object in JavaScript?JavaScript 中不可变对象的示例是什么？
  - What are the pros and cons of immutability?不变性的优点和缺点是什么？
  - How can you achieve immutability in your own code?如何在自己的代码中实现不变性？

- Explain the difference between synchronous and asynchronous functions.解释同步函数和异步函数之间的区别。

- What is event loop?什么是事件循环？
  
  - What is the difference between call stack and task queue?调用堆栈和任务队列有什么区别？
  
  

- What are the differences between variables created using `let`, `var` or `const`?使用  `let` 、 `var`  或  `const`  创建的变量之间有什么区别？

let/var 声明的变量的值是可变的，const 是不可变的。

作用域不同。

execution context 中的位置不同，let/const 在 lexical environment，var 在 variable environment。

- What are the differences between ES6 class and ES5 function constructors?ES6 类和 ES5 函数构造函数有什么区别？

- Can you offer a use case for the new arrow `=>` function syntax? How does this new syntax differ from other functions?您能否提供新箭头  `=>`  函数语法的用例？这个新语法与其他函数有何不同？

- What advantage is there for using the arrow syntax for a method in a constructor?在构造函数中的方法中使用箭头语法有什么优点？

- What is the definition of a higher-order function?高阶函数的定义是什么？

- Can you give an example for destructuring an object or an array?你能举一个解构对象或数组的例子吗？

- Can you give an example of generating a string with ES6 Template Literals?你能举一个使用 ES6 模板文字生成字符串的例子吗？

- Can you give an example of a curry function and why this syntax offers an advantage?您能否举一个柯里化函数的示例以及为什么这种语法具有优势？

- What are the benefits of using `spread syntax` and how is it different from `rest syntax`?使用  `spread syntax`  有什么好处？它与  `rest syntax`  有什么不同？

- How can you share code between files?如何在文件之间共享代码？

- Why you might want to create static class members?为什么您可能想要创建静态类成员？

- What is the difference between `while` and `do-while` loops in JavaScript?JavaScript 中的  `while`  和  `do-while`  循环有什么区别？



- What is a promise? Where and how would you use promise?什么是承诺？你会在哪里以及如何使用 Promise？

定义 micro task 的语法。io/等待任务/

- Discuss how you might use Object Oriented Programming principles when coding with JavaScript.讨论在使用 JavaScript 编码时如何使用面向对象编程原则。

## Coding questions

- Make this work:

```javascript
duplicate([1, 2, 3, 4, 5]); // [1,2,3,4,5,1,2,3,4,5]
```

- Create a for loop that iterates up to `100` while outputting **"fizz"** at multiples of `3`, **"buzz"** at multiples of `5` and **"fizzbuzz"** at multiples of `3` and `5`创建一个 for 循环，迭代到  `100` ，同时以  `3`  的倍数输出“fizz”、以  `5`  的倍数输出“buzz”以及以倍数输出“fizzbuzz” `3`  和  `5`

- What will be returned by each of these?

```javascript
console.log("hello" || "world");
console.log("foo" && "bar");
```

- Write an immediately invoked function expression (IIFE) 编写立即调用函数表达式 (IIFE)

```javascript
(function () {
  var a;
  //code
})();
```

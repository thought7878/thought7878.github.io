---
title: Function.prototype.bind
date: 2024-09-17
lastmod: 2024-09-17
tags:
  - JavaScript
  - 手撕
draft: false
summary: 这篇文章将一步步实现一个bind方法，让我们更好地理解bind的原理。
---

### 引入

开门见山，我们先来看MDN上对`Function.prototype.bind`的定义：

> `bind()`方法创建一个新的函数，当这个新函数被调用时，`bind()`的第一个参数将作为它运行时的`this`，之后的一序列参数将会在传递的实参前传入作为它的参数。

从这句话可以得出`bind`方法的几个特点：

1. `bind`方法会返回一个新的函数
2. 新函数的`this`指向`bind`的第一个参数
3. 新函数的参数是`bind`的第二个参数开始的参数 + 新函数调用时的参数

比如这个例子

```javascript
function foo(a, b) {
  console.log(this, a, b)
}

const bar = foo.bind({ name: 'bar' }, 1)
bar(2) // { name: 'bar' } 1 2
```

`bar`是`foo`的一个新函数，`bar`的`this`指向`{ name: 'bar' }`，参数`1`是`bind`的第二个参数，参数`2`是`bar`调用时的参数。

## 基础版bind

根据上面的特点，我们可以实现一个简单的`bind`方法：

```javascript
Function.prototype.myBind = function (context, ...args) {
  // context是bind的第一个参数，为this指向
  const fn = this // fn是调用myBind的函数
  return function (...newArgs) {
    return fn.apply(context, args.concat(newArgs)) // 调用这个函数，this指向context，参数是args和newArgs的合并
  }
}
```

## 进阶版bind

上面的实现是一个简单的`bind`，但是它没考虑到以下几个问题：

1. bind函数可以通过调用进一步绑定参数，此时新绑定的`this`会被忽略。

```javascript
function log(...args) {
  console.log(this, ...args)
}
const boundLog = log.bind('this value', 1, 2)
const boundLog2 = boundLog.bind('new this value', 3, 4)
boundLog2(5, 6) // "this value", 1, 2, 3, 4, 5, 6
```

比如这个例子中，`boundLog2`的`this`指向`"this value"`，而不是`"new this value"`。

2. `bind`函数可以通过`new`关键字调用，此时`this`指向新创建的对象。

```javascript
function Person(name) {
  this.name = name
}
Person.prototype.sayName = function () {
  console.log(this.name)
}
const PersonBind = Person.bind({ name: 'bind' })
const person = new PersonBind('new')
person.sayName() // "new"
```

根据这些特点，我们可以进一步完善`bind`方法：

```javascript
Function.prototype.myBind = function (context) {
  // 1. 判断调用bind的是否是函数
  if (typeof this !== 'function') {
    throw new Error('Function.prototype.bind - what is trying to be bound is not callable')
  }

  const fn = this
  const args = Array.prototype.slice.call(arguments, 1)
  const bound = function (...newArgs) {
    // 2. 如果通过new关键字调用，this指向新创建的对象，否则指向context
    return fn.apply(this instanceof bound ? this : context, args.concat(newArgs))
  }

  // 3. 继承原型链，确保通过 new 调用时，实例能访问原函数的 prototype 方法
  // 使用Object.create()而不是直接赋值，是为了避免修改bound的prototype属性影响原函数的prototype属性
  bound.prototype = Object.create(fn.prototype)
  return bound
}
```
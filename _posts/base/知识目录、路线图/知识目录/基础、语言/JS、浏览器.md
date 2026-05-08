参考：[[JS_roadmap-未整理]]、[[_posts/base/知识目录、路线图/知识目录/浏览器原理与性能优化/浏览器|浏览器]]、[[JS_AI总结]]


参考：[[面试题目汇总]]

对前端开发中的JavaScript核心概念及浏览器工作原理进行系统性知识总结。内容应涵盖但不限于以下方面：JavaScript基础语法、数据类型、函数与作用域、原型与继承、异步编程模型（回调函数、Promise、async/await）、DOM操作与事件处理机制；浏览器的渲染原理、关键渲染路径、回流与重绘、事件循环机制、存储机制（Cookie、LocalStorage、SessionStorage）、同源策略与跨域解决方案、安全机制（XSS、CSRF防护）等。要求知识点结构清晰、层次分明，每个概念需包含定义、工作原理、实际应用场景及常见问题与解决方案，形成全面且实用的前端技术知识体系。

对前端开发领域的核心知识点进行系统性、结构化的详细总结。内容应涵盖但不限于以下关键模块：HTML基础与语义化标签、CSS核心概念（选择器、盒模型、布局技术、响应式设计）、JavaScript核心语法与ES6+特性、DOM操作与事件处理机制、前端工程化（构建工具、模块化方案、包管理）、主流前端框架（React、Vue、Angular）的核心原理与应用、浏览器工作原理与性能优化策略、前端安全（XSS、CSRF防护等）、网络请求与AJAX技术、前端测试（单元测试、集成测试）、跨端开发方案（移动端适配、小程序、Electron等）以及前端性能监控与错误追踪。每个知识点需包含基础概念、关键技术点、实际应用场景及常见问题解决方案，形成层次分明、内容全面的知识体系总结。


以下是 JavaScript 知识点的系统性总结，涵盖基础到高级内容，方便你按模块复习：

---

# JavaScript简介
## JavaScript是什么?
参考：[[What is JavaScript?]]

## JavaScript的历史
参考：[[History of JavaScript]]
## Javascript 的版本
参考：[[Javascript Versions]]
## 如何运行JS

# 变量
## 声明变量
- var
- let
- const
要在JavaScript中使用变量，我们首先需要创建它，即声明一个变量。要声明变量，我们使用 `var` ， `let` 或 `const` 关键字之一。
## 变量提升（Hoisting）
## 命名规则
## 作用域（Scopes）
- Global
- Function
- Block


# 数据类型（Data types）
参考：[[基本数据类型、引用数据类型]]、[[12.栈空间和堆空间：数据是如何存储的？]]

## 原始类型、基本类型（Primitive Types）
7种原始类型、基本类型
参考：[[基本数据类型、引用数据类型]]

## 引用类型、对象类型（Object）
对象类型、引用类型：对象、数组、函数

参考：[[基本数据类型、引用数据类型]]
### 原型（Prototype）
参考：[[Prototype & Inheritance]]

### 原型继承（Prototypal Inheritance）
参考：[[Prototype & Inheritance]]

## 类型判断
参考：[[类型判断]]

## 类型转换
Type Conversion

参考：[[类型转换]]

### 类型转换/强制
Type Conversion/Coercion  

类型强制是将值从一个数据类型转换为另一种数据类型的自动或隐式转换（例如字符串到数字）。类型转换类似于类型的胁迫，因为它们具有一个关键差异将值从一个数据类型转换为另一种数据类型 - 类型强制是隐式的。相比之下，类型转换可以是隐式的或显式的。

### 隐式类型转换
Implicit Type Casting  

当编译器或运行时自动转换数据类型时，就会发生隐式类型转换。 JavaScript 是松散类型语言，大多数时候*运算符会自动将值转换为正确的类型*。

### 显式类型转换
Explicit Type Casting  

类型转换意味着通过显式指定要将给定数据转换为的类型，将数据从一种数据类型传输到另一种数据类型。通常进行显式类型转换以使数据与其他变量兼容。类型转换方法的示例有 `parseInt()` 、 `parseFloat()` 、 `toString()` 。

# 数据结构（Data Structures）
## 键集合（Keyed Collections）
### Map
参考：[[Map]]、[[Map-2]]
### Weak map
参考：[[WeakMap]]
### Set
参考：[[Set]]
### WeakSet
参考：[[WeakSet]]

## 索引集合（Indexed Collections）
### Typed Array
In Javascript, a typed array is an array-like buffer of binary data. There is no JavaScript property or object named TypedArray, but properties and methods can be used with typed array objects.  
在 Javascript 中，类型化数组是类似数组的二进制数据缓冲区。没有名为 TypedArray 的 JavaScript 属性或对象，但属性和方法可以与类型化数组对象一起使用。
### Array
参考：[[_posts/base/js/知识点总结/数据类型/Array]]

Arrays are objects that store a collection of items and can be assigned to a variable. They have their methods that can perform operations on the array.  
数组是存储项目集合并可以分配给变量的对象。他们的方法可以在数组上执行操作。

## Structured data
### JSON
参考：[[JSON]]


# Equality Comparisons
相等比较
## ==
## ===
## Object.is

## isLooselyEqual

## isStrictlyEqual
## Same value zero
## Same value

# Loops and Iterations
循环和迭代
[[循环]]

## 循环总结
[[循环 总结]]

## 迭代协议（Symbol.iterator）
[[迭代协议（Symbol.iterator）]]

## for

## do…while


## while


## for…of


## for…in


## break / continue



# 一、JavaScript 基础语法

## 变量与数据类型

   - 变量声明：`var`、`let`、`const` 的区别（作用域、提升、重复声明）
   - 数据类型：7 种原始类型（`number`, `string`, `boolean`, `null`, `undefined`, `symbol`, `bigint`） + `object`
   - 类型检测：`typeof`、`instanceof`、`Object.prototype.toString.call()`
   - 类型转换：显式转换（`Number()`, `String()`）与隐式转换（\=\= vs \=\=\=）

## 运算符与表达式

   - 算术运算符：`+`、`-`、`*`、`/`、`%`、`**`
   - 逻辑运算符：`&&`、`||`、`!`、`??`（空值合并）
   - 比较运算符：\=\=、\=\=\=、`>`、`<`、`>=`、`<=`
   - 位运算符：`&`、`|`、`~`、`^`、`<<`、`>>`、`>>>`

## 流程控制
   - 条件语句：`if...else`、`switch`
   - 循环语句：`for`、`while`、`do...while`、`for...of`、`for...in`
   - 控制关键字：`break`、`continue`、`return`、`throw`

---

# 二、核心概念

## 作用域与闭包

   - 作用域类型：全局作用域、函数作用域、块级作用域（`let`/`const`）
   - 作用域链与变量查找规则
   - 闭包原理与应用场景（模块化、私有变量）。[[_闭包、作用域链]]、[[闭包]]

## `this` 绑定规则

   - 默认绑定（全局对象）
   - 隐式绑定（对象方法调用）
   - 显式绑定（`call`、`apply`、`bind`）
   - `new` 绑定（构造函数）
   - 箭头函数的 `this` 继承

## 原型与继承

   - 原型链：`__proto__`、`prototype`。[[原型]]
   - 构造函数与 `new` 的原理。[[构造函数与 new 的原理]]
   - 继承方式：原型链继承、组合继承、寄生组合继承、Class 继承。[[继承方式：原型链继承、组合继承、寄生组合继承、Class 继承]]
   - `Object.create()`、`Object.assign()`。[[Object.create()]]、[[Object.assign()]]

## 异步编程
   - 回调函数（Callback）
   - `Promise` 链式调用与静态方法（`all`、`race`、`allSettled`）。[[_Promise|Promise 原理]]、
   - `async/await` 语法糖
   - 事件循环（Event Loop）：宏任务（`setTimeout`）与微任务（`Promise.then`）。[[_Event Loop, Web APIs, (Micro)task Queue]]
   - `Generator` 与 `yield`

---

# 三、高级特性

## ES6+ 新特性

   - 箭头函数、解构赋值、模板字符串。[[箭头函数]]、[[_posts/base/js/知识点总结/ES6+ 新特性/解构赋值|解构赋值]]、[[模板字符串]]
   - 默认参数、剩余参数（`...`）。[[默认参数]]、[[剩余参数（...）]]
   - 类（`class`）、继承（`extends`）、静态方法（`static`）
   - 模块化（`import`/`export`）。[[_posts/base/js/知识点总结/模块/模块化|模块化]]
   - `Map`、`Set`、`WeakMap`、`WeakSet`。
   - `Proxy` 与 `Reflect`。[[_posts/base/js/知识点总结/ES6+ 新特性/Proxy]]、[[_posts/base/js/知识点总结/ES6+ 新特性/Reflect]]
   - 可选链（`?.`）、空值合并（`??`）。[[可选链（?.）]]、[[空值合并（??）]]

## 函数进阶

   - 函数式编程（纯函数、不可变性）。[[_posts/base/js/知识点总结/函数/函数式编程]]
   - 高阶函数（函数作为参数或返回值）。[[高阶函数]]
   - 柯里化（Currying）与偏函数。[[柯里化（Currying）与偏函数]]
   - 防抖（Debounce）与节流（Throttle）。[[防抖（Debounce）与节流（Throttle）]]

## 错误处理
[[错误处理]]
   - `try...catch...finally`
   - 错误类型：`Error`、`SyntaxError`、`TypeError` 等
   - 自定义错误与 `throw`

## 浏览器相关
   - DOM 操作：节点增删改查、事件监听（`addEventListener`）
   - BOM 对象：`window`、`location`、`history`、`navigator`、`screen`
   - 事件模型：冒泡、捕获、委托 [[事件机制]]
   - AJAX 与 `fetch` API
   - Web Storage：`localStorage`、`sessionStorage`

### 事件
[[事件机制]]

#### 事件的触发过程

#### 事件绑定

#### 事件委托/代理

#### 事件类型

#### 事件对象

### 网络




---

# 四、运行机制与底层原理
参考：[[JS运行原理1_JS是如何被运行的？]]、[[JS运行原理2_V8引擎是如何运行JS的?]]、[[JS运行原理3_JS调用栈]]


1. **执行上下文与调用栈**
[[执行上下文（Execution Context）]]
   - 执行上下文类型：全局、函数、eval
   - 变量环境（`var`）与词法环境（`let`/`const`）
   - 作用域链与闭包的内存管理。

2. **内存管理**
[[内存管理：栈空间、堆空间、代码空间]]、
   - 垃圾回收机制：标记清除、引用计数
   - 内存泄漏场景（全局变量、未清除的定时器、闭包滥用）

3. **事件循环（Event Loop）**
   - 调用栈、任务队列（宏任务、微任务）
   - `setTimeout`、`Promise`、`requestAnimationFrame` 的执行顺序
   - Node.js 与浏览器事件循环差异

---

# 五、工具与生态

1. **开发工具**

   - npm/yarn 包管理
   - Webpack/Vite 构建工具
   - Babel 转译器
   - ESLint/Prettier 代码规范

2. **测试框架**

   - Jest/Mocha 单元测试
   - Cypress/Puppeteer E2E 测试

3. **现代框架**
   - React/Vue/Angular 核心概念
   - 状态管理（Redux、Vuex）
   - 路由（React Router、Vue Router）

---

# 六、最佳实践与常见问题

1. **代码优化**

   - 避免全局变量污染
   - 使用 \=\=\= 而非 \=\=
   - 减少 DOM 操作次数
   - 防抖与节流优化高频事件

2. **安全**

   - XSS（跨站脚本攻击）防范
   - CSRF（跨站请求伪造）防范
   - 使用 `Content Security Policy (CSP)`

3. **常见问题**
   - `0.1 + 0.2 !\=\= 0.3`（浮点数精度）
   - `typeof null \=\== 'object'`（历史遗留）
   - `var` 的变量提升与闭包陷阱

---

# 七、面试重点总结

1. **高频考点**

   - 闭包与作用域链
   - 原型链与继承
   - 事件循环与异步编程
   - `this` 绑定规则
   - `Promise` 手写实现

2. **手写代码题**
   - 实现 `call`/`apply`/`bind`
   - 深拷贝（Deep Clone）
   - 数组去重、扁平化
   - 防抖与节流函数
   - `Promise` 封装 AJAX

---

# 学习资源推荐

- **书籍**：《JavaScript 高级程序设计》《你不知道的 JavaScript》
- **文档**：[MDN Web Docs](https://developer.mozilla.org/zh-CN/)
- **练习**：[LeetCode](https://leetcode.com/)、[Codewars](https://www.codewars.com/)
- **视频**：慕课网、B 站技术博主的系统课程

---

按此框架逐一攻克，*重点关注核心概念与高频面试题*，辅以代码实践，即可系统掌握 JavaScript！

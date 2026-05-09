参考：[[JS_roadmap-未整理]]、[[_posts/base/知识目录、路线图/知识目录/浏览器原理与性能优化/浏览器|浏览器]]

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

`var`（存在变量提升，函数作用域）、`let`（块级作用域）、`const`（块级作用域，声明常量）。

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

`Number`（数值类型，包含整数和浮点数）、`String`（字符串类型）、`Boolean`（布尔类型，值为 `true` 或 `false`）、`Null`（只有一个值 `null`，表示空对象引用）、`Undefined`（变量声明但未赋值时的默认值）、`Symbol`（ES6 新增，创建独一无二的值）。

## 引用类型、对象类型（Object）
对象类型、引用类型：对象、数组、函数。`Object`（对象类型，*无序的*数据集合）、`Array`（数组类型，*有序的*数据集合）、`Function`（函数类型，*可执行的代码块*）。

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


# 循环和迭代
Loops and Iterations

[[循环]]

## 循环总结
[[循环 总结]]

## 迭代协议（Symbol.iterator）
[[迭代协议（Symbol.iterator）]]

## 循环语句
`for`（普通 for 循环）、`while`（当条件为真时循环）、`do...while`（至少执行一次循环体）、`for...in`（遍历对象的可枚举属性）、`for...of`（ES6 新增，遍历可迭代对象）。

### for

### do…while


### while


### for…of


### for…in


## 跳转语句
`break`（跳出循环）、`continue`（跳过本次循环）、`return`（从函数中返回值并终止函数执行）。

### break
### continue
### return


# 表达式、运算符
Expressions and Operators

## 条件运算符
Conditional operators

condition ? val_for_true : val_for_false

## 一元运算符
Unary Operators
参考：[[一元运算符]]

## 赋值运算符
Assignment Operators

- 赋值：`\=`
- 加等于：`+=`
- 减等于：`-=`

参考：[[赋值运算符]]


## 比较运算符
Comparison Operators

- 相等：`\==`
- 严格相等：`\===`（值和类型都相等）
- 不相等：`!=`
- 严格不相等：`!==`
- 大于：`>`
- 小于：`<`
- 大于等于：`>=`
- 小于等于：`<=`

参考：[[比较运算符]]

## 算术运算符
Arithmetic operators

- 加：`+`
- 减：`-`
- 乘：`*`
- 除：`/`
- 取余：`%`
- 自增：`++`
- 自减：`--`

参考：[[算术运算符]]

## 位运算符
Bitwise operators

- 按位与：`&`
- 按位或：`|`
- 按位异或：`^`
- 按位取反：`~`
- 左移：`<<`
- 右移：`>>`
- 无符号右移：`>>>`

参考：[[位运算符]]


## 逻辑运算符
Logical Operators

- 逻辑与：`&&`
- 逻辑或：`||`
- 逻辑非：`!`

参考：[[逻辑运算符]]

## BigInt运算符
BigInt Operators

参考：[[BigInt运算符]]

## 字符串运算符
String Operators

参考：[[字符串运算符]]


## 逗号运算符
Comma operators
可忽略，极少这么用


# Function
Functions exist so we can reuse code. They are blocks of code that execute whenever they are invoked. Each function is typically written to perform a particular task, like an addition function used to find the sum of two or more numbers. When numbers need to be added anywhere within your code, the addition function can be invoked as many times as necessary.  

**函数的存在是为了让我们可以重用代码**。它们是只要被调用就会执行的**代码块**。每个函数通常都是**为了执行特定任务而编写的**，例如用于求两个或多个数字之和的加法函数。当需要在代码中的任何位置添加数字时，可以根据需要多次调用加法函数。


## 函数定义
### 函数声明
使用 `function` 关键字定义函数，如 `function add(a, b) { return a + b; }`。

### 函数表达式
将函数赋值给一个变量，如 `const add = function(a, b) { return a + b; }`。

### 箭头函数
ES6 新增的简洁语法，如 `const add = (a, b) => a + b;`。

## 函数参数
Function Parameters

### 形参和实参
函数定义时的参数为形参，调用时传递的参数为实参。

### 默认参数
Default Parameters

ES6 允许为函数参数设置默认值，如 `function add(a, b = 0) { return a + b; }`。

### 剩余参数
Rest Parameters

使用 `...` 语法收集多余的参数，如 `function sum(...numbers) { return numbers.reduce((acc, num) => acc + num, 0); }`。

## 箭头函数
Arrow Functions

## IIFE

## Arguments object

## 作用域
Scope

函数内部可以访问外部作用域的变量，但外部作用域无法访问函数内部的变量。

可以访问或使用特定变量或功能的空间或环境。此变量或函数的可访问性取决于定义的位置。

JavaScript具有以下类型的范围：
- **Global scope**: The default scope for all code running in script mode.
- **Module scope**: The scope for code running in module mode.
- **Function scope**: The scope created with a function.
- **Block scope**: The scope created with a pair of curly braces (a block).

## 调用栈
Function Stack (Call stack)

The function stack is how the interpreter keeps track of its place in a script that calls multiple functions, like which function is currently executing and which functions within that function are being called.  
该功能堆栈是解释器如何在调用多个函数的脚本中跟踪其位置，例如当前正在执行哪个函数以及该功能中的哪些函数正在调用。

## 递归
Recursion

## 词法作用域
Lexical scoping

## 闭包
Closures

函数内部返回一个函数，该内部函数可以访问外部函数的变量，即使外部函数已经执行完毕。

## 内置函数
Built-in functions


# 对象、类
参考：[[Class]]

## 对象创建
### 对象字面量
使用 `{}` 创建对象，如 `const person = { name: 'John', age: 30 };`。

### 构造函数
使用 `function` 定义构造函数，通过 `new` 关键字创建对象

### ES6 类
使用 `class` 关键字定义类，通过 `new` 关键字创建对象

## 对象的继承
### 原型链继承
通过原型对象实现继承，每个对象都有一个原型对象，原型对象又有自己的原型对象，以此类推。

### 构造函数继承
在子类构造函数中调用父类构造函数，如 `function Child(name, age) { Parent.call(this, name, age); }`。

### 组合继承
结合原型链继承和构造函数继承的优点。

### ES6 类继承
使用 `extends` 关键字实现类的继承，如 `class Child extends Parent { constructor(name, age) { super(name, age); } }`。


# 数组

- **数组创建**：使用 `[]` 或 `new Array()` 创建数组，如 `const numbers = [1, 2, 3];` 或 `const numbers = new Array(1, 2, 3);`。
- **数组方法**
  - **遍历方法**：`forEach`（遍历数组）、`map`（返回一个新数组，新数组中的元素是原数组元素经过某种处理后的结果）、`filter`（返回一个新数组，新数组中的元素是原数组中满足某种条件的元素）、`reduce`（将数组元素累积为一个值）等。
  - **添加和删除元素方法**：`push`（在数组末尾添加元素）、`pop`（删除数组末尾的元素）、`unshift`（在数组开头添加元素）、`shift`（删除数组开头的元素）、`splice`（删除、插入或替换数组元素）等。
  - **其他方法**：`concat`（合并数组）、`join`（将数组元素转换为字符串）、`reverse`（反转数组元素的顺序）、`sort`（对数组元素进行排序）等。



# 异步
Asynchronous

## 回调函数
将一个函数作为参数传递给另一个函数，在另一个函数执行完毕后调用该函数。

## Promise
ES6 引入的一种异步编程解决方案，用于处理异步操作的结果。`Promise` 有三种状态：`pending`（进行中）、`fulfilled`（已成功）、`rejected`（已失败）。可以使用 `then` 方法处理成功的结果，使用 `catch` 方法处理失败的结果。

参考：[[_posts/base/js/知识点总结/异步编程/promise/overview|overview]]

## async / await
ES8 引入的异步编程语法糖，基于 `Promise` 实现，使异步代码看起来更像同步代码。使用 `async` 关键字定义异步函数，使用 `await` 关键字等待 `Promise` 的结果。

参考：[[async & await]]

## Web APIs
参考：[[_Event Loop, Web APIs, (Micro)task Queue]]、[[Web APIs]]

## Event Loop/Call Stack/(Micro)Task Queue
参考：[[_Event Loop, Web APIs, (Micro)task Queue]]、[[Web APIs]]

# remote APIs
## XMLHttpRequest
参考：[[XMLHttpRequest]]
## Fetch
参考：[[_posts/base/js/知识点总结/Web APIs/Fetch/Fetch]]


# 迭代器、生成器
Iterators and Generators

参考：[[Generator-2]]、[[Iterator]]


# Module
## ES Module
参考：[[_posts/base/js/知识点总结/模块/ES Module]]
## CommonJS
参考：[[CommonJS]]


- **ES6 模块**：使用 `import` 和 `export` 关键字实现模块的导入和导出。`export` 可以导出变量、函数、类等，`import` 可以导入其他模块导出的内容。
- **CommonJS**：服务器端 JavaScript（如 Node.js）使用的模块化规范，使用 `module.exports` 导出模块内容，使用 `require` 导入模块。
- **AMD 和 CMD**：浏览器端的模块化规范，AMD（异步模块定义）以 RequireJS 为代表，CMD（通用模块定义）以 Sea.js 为代表。



# Memory Management
参考：[[内存管理：栈空间、堆空间、代码空间]]

## Memory lifecycle

## Garbage Collection
参考：[[垃圾回收（Garbage Collection）]]、[[13.垃圾回收：垃圾数据是如何自动回收的？]]


# DOM APIs
With HTML DOM, JavaScript can access and change all the elements of an HTML document such as its attributes, CSS styles, remove elements, add and create new elements on the page. Web API means application programming interface for the web. All browsers have a set of built-in Web APIs to support complex operations, and to help accessing data. Like Geo-location API, Web Storage, Web History and others.  
通过 HTML DOM，JavaScript 可以访问和更改 HTML 文档的所有元素（例如其属性、CSS 样式）、删除元素、在页面上添加和创建新元素。 Web API 是指网络应用程序编程接口。所有浏览器都有一组内置的 Web API 来支持复杂的操作并帮助访问数据。例如地理位置 API、网络存储、网络历史记录等。

# 严格模式
Strict Mode

参考：[[Strict Mode]]

# This 关键字
This Keyword


# Chrome Dev Tools
## Debugging issues

## Debugging Memory Leaks

## Debugging performance

---

# 浏览器对象模型（BOM）

- **window 对象**：浏览器窗口的全局对象，包含了许多属性和方法，如 `alert`（弹出警告框）、`confirm`（弹出确认框）、`prompt`（弹出提示框）、`setTimeout`（设置定时器）、`setInterval`（设置间隔定时器）等。
- **location 对象**：用于获取和设置当前页面的 URL 信息，如 `location.href`（获取或设置当前页面的 URL）、`location.reload`（重新加载当前页面）等。
- **history 对象**：用于操作浏览器的历史记录，如 `history.back`（返回上一页）、`history.forward`（前进到下一页）、`history.go`（跳转到指定的历史记录位置）等。
- **navigator 对象**：用于获取浏览器的相关信息，如 `navigator.userAgent`（获取浏览器的用户代理字符串）等。

# 文档对象模型（DOM）

- **DOM 节点**：HTML 文档中的每个元素、文本、属性等都是一个 DOM 节点。常见的节点类型有元素节点、文本节点、属性节点等。
- **DOM 操作**
  - **节点选择**：使用 `document.getElementById`（通过 ID 选择元素）、`document.getElementsByTagName`（通过标签名选择元素）、`document.getElementsByClassName`（通过类名选择元素）、`document.querySelector`（通过 CSS 选择器选择第一个匹配的元素）、`document.querySelectorAll`（通过 CSS 选择器选择所有匹配的元素）等方法选择节点。
  - **节点创建和插入**：使用 `document.createElement`（创建元素节点）、`document.createTextNode`（创建文本节点）、`appendChild`（在父节点末尾插入子节点）、`insertBefore`（在指定节点之前插入新节点）等方法创建和插入节点。
  - **节点删除和替换**：使用 `removeChild`（删除子节点）、`replaceChild`（替换子节点）等方法删除和替换节点。
  - **节点属性和样式操作**：可以通过节点的属性（如 `id`、`className` 等）和 `style` 属性操作节点的属性和样式。

# 其他高级特性

- **正则表达式**：用于匹配和处理字符串的模式，使用 `/pattern/flags` 的语法创建正则表达式对象，如 `/abc/i` 表示不区分大小写匹配字符串中的 `abc`。
- **JSON**：JavaScript 对象表示法，是一种轻量级的数据交换格式。使用 `JSON.stringify` 将 JavaScript 对象转换为 JSON 字符串，使用 `JSON.parse` 将 JSON 字符串转换为 JavaScript 对象。
- **元编程**：ES6 引入了 `Proxy` 和 `Reflect` 对象，用于实现元编程。`Proxy` 可以拦截对象的基本操作，如属性访问、属性赋值等；`Reflect` 提供了一系列静态方法，用于操作对象。


---

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

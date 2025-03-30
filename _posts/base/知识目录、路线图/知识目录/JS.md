# JavaScript简介
## What is JavaScript?
参考 [[What is JavaScript?]]
## History of JavaScript
参考 [[History of JavaScript]]
## Javascript Versions
参考 [[Javascript Versions]]
## How to Run Javascript

# Variables
## Variable Declarations
- var
- let
- const
To use variables in JavaScript, we first need to create it i.e. declare a variable. To declare variables, we use one of the `var`, `let`, or `const` keywords.  
要在JavaScript中使用变量，我们首先需要创建它，即声明一个变量。要声明变量，我们使用 `var` ， `let` 或 `const` 关键字之一。
## Hoisting
## Naming Rules
## Scopes
- Global
- Function
- Block

# Data types
参考 [[基本数据类型、引用数据类型]]、[[12.栈空间和堆空间：数据是如何存储的？]]

## Primitive Types
7种原始类型、基本类型
参考 [[基本数据类型、引用数据类型]]
## Object
对象类型、引用类型：对象、数组、函数
参考 [[基本数据类型、引用数据类型]]
### Prototype
参考 [[Prototype & Inheritance]]

### Prototypal Inheritance
参考 [[Prototype & Inheritance]]

## 类型判断
参考 [[类型判断]]

## Type Conversion
参考 [[类型转换]]

### Type Conversion/Coercion  
类型转换/强制

Type coercion is the automatic or implicit conversion of values from one data type to another (such as strings to numbers). Type conversion is similar to type coercion because they convert values from one data type to another with one key difference — type coercion is implicit. In contrast, type conversion can be either implicit or explicit.  
类型强制是将值从一个数据类型转换为另一种数据类型的自动或隐式转换（例如字符串到数字）。类型转换类似于类型的胁迫，因为它们具有一个关键差异将值从一个数据类型转换为另一种数据类型 - 类型强制是隐式的。相比之下，类型转换可以是隐式的或显式的。

### Implicit Type Casting  
隐式类型转换

Implicit type conversion happens when the compiler or runtime automatically converts data types. JavaScript is loosely typed language and most of the time operators automatically convert a value to the right type.  
当编译器或运行时自动转换数据类型时，就会发生隐式类型转换。 JavaScript 是松散类型语言，大多数时候运算符会自动将值转换为正确的类型。

### Explicit Type Casting  
显式类型转换

Type casting means transferring data from one data type to another by explicitly specifying the type to convert the given data to. Explicit type casting is normally done to make data compatible with other variables. Examples of typecasting methods are `parseInt()`, `parseFloat()`, `toString()`.  
类型转换意味着通过显式指定要将给定数据转换为的类型，将数据从一种数据类型传输到另一种数据类型。通常进行显式类型转换以使数据与其他变量兼容。类型转换方法的示例有 `parseInt()` 、 `parseFloat()` 、 `toString()` 。

# Data Structures
## Keyed Collections
### Map
参考 [[Map]]
### Weak map
参考 [[WeakMap]]
### Set
参考 [[Set]]
### WeakSet
参考 [[WeakSet]]

## Indexed Collections
### Typed Array
In Javascript, a typed array is an array-like buffer of binary data. There is no JavaScript property or object named TypedArray, but properties and methods can be used with typed array objects.  
在 Javascript 中，类型化数组是类似数组的二进制数据缓冲区。没有名为 TypedArray 的 JavaScript 属性或对象，但属性和方法可以与类型化数组对象一起使用。
### Array
参考 [[_posts/base/js/知识点总结/数据类型/Array]]

Arrays are objects that store a collection of items and can be assigned to a variable. They have their methods that can perform operations on the array.  
数组是存储项目集合并可以分配给变量的对象。他们的方法可以在数组上执行操作。

## Structured data
### JSON
参考 [[JSON]]

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

## for

## do…while


## while


## for…of


## for…in


## break / continue


# Control Flow
## Conditional statements

### If Else

### Switch Case

## Exception Handling

### Throw Statement

### Try, Catch, Finally

### Error Object


# Expressions and Operators
## Conditional operators
condition ? val_for_true : val_for_false

## Comma operators
可忽略，极少这么用

## Unary Operators
参考 [[一元运算符]]

## Assignment Operators
参考 [[赋值运算符]]

## Comparison Operators
参考 [[比较运算符]]

## Arithmetic operators
参考 [[算术运算符]]

## Bitwise operators
参考 [[位运算符]]

## Logical Operators
参考 [[逻辑运算符]]

## BigInt Operators
[[BigInt运算符]]

## String Operators
参考 [[字符串运算符]]


# Function
Functions exist so we can reuse code. They are blocks of code that execute whenever they are invoked. Each function is typically written to perform a particular task, like an addition function used to find the sum of two or more numbers. When numbers need to be added anywhere within your code, the addition function can be invoked as many times as necessary.  

**函数的存在是为了让我们可以重用代码**。它们是只要被调用就会执行的**代码块**。每个函数通常都是**为了执行特定任务而编写的**，例如用于求两个或多个数字之和的加法函数。当需要在代码中的任何位置添加数字时，可以根据需要多次调用加法函数。

## Function Parameters

### Default Parameters

### Rest Parameters

## Arrow Functions

## IIFE

## Arguments object

## Scope and function stack(Call stack)
### Scope

A space or environment in which a particular variable or function can be accessed or used. Accessibility of this variable or function depends on where it is defined.  
可以访问或使用特定变量或功能的空间或环境。此变量或函数的可访问性取决于定义的位置。

JavaScript has the following kinds of scopes:  
JavaScript具有以下类型的范围：

- **Global scope**: The default scope for all code running in script mode.
- **Module scope**: The scope for code running in module mode.
- **Function scope**: The scope created with a function.
- **Block scope**: The scope created with a pair of curly braces (a block).

### Function Stack (Call stack)

The function stack is how the interpreter keeps track of its place in a script that calls multiple functions, like which function is currently executing and which functions within that function are being called.  
该功能堆栈是解释器如何在调用多个函数的脚本中跟踪其位置，例如当前正在执行哪个函数以及该功能中的哪些函数正在调用。

### Recursion

### Lexical scoping

### Closures

## Built-in functions

## 函数声明、函数表达式

# DOM APIs
With HTML DOM, JavaScript can access and change all the elements of an HTML document such as its attributes, CSS styles, remove elements, add and create new elements on the page. Web API means application programming interface for the web. All browsers have a set of built-in Web APIs to support complex operations, and to help accessing data. Like Geo-location API, Web Storage, Web History and others.  
通过 HTML DOM，JavaScript 可以访问和更改 HTML 文档的所有元素（例如其属性、CSS 样式）、删除元素、在页面上添加和创建新元素。 Web API 是指网络应用程序编程接口。所有浏览器都有一组内置的 Web API 来支持复杂的操作并帮助访问数据。例如地理位置 API、网络存储、网络历史记录等。

# Strict Mode
参考 [[Strict Mode]]

# This Keyword



# Asynchronous
## Promise
参考 [[_posts/base/js/知识点总结/异步编程/promise/overview|overview]]

## async / await
参考 [[async & await]]

## Web APIs
参考 [[Event Loop, Web APIs, (Micro)task Queue]]、[[Web APIs]]
## Event Loop/Call Stack/(Micro)Task Queue
参考 [[Event Loop, Web APIs, (Micro)task Queue]]、[[Web APIs]]

# remote APIs
## XMLHttpRequest
参考 [[XMLHttpRequest]]
## Fetch
参考 [[_posts/base/js/知识点总结/Web APIs/Fetch/Fetch]]

# Class
参考 [[Class]]

# Iterators and Generators
参考 [[Generator]]、[[Iterator]]


# Module
## ES Module
参考 [[_posts/base/js/知识点总结/模块/ES Module]]
## CommonJS
参考 [[CommonJS]]

# Memory Management
参考：[[内存管理：栈空间、堆空间、代码空间]]

## Memory lifecycle

## Garbage Collection
参考：[[垃圾回收（Garbage Collection）]]、[[13.垃圾回收：垃圾数据是如何自动回收的？]]

# Chrome Dev Tools
## Debugging issues

## Debugging Memory Leaks

## Debugging performance


**环境记录（Environment Record）** 是 JavaScript 中用于*存储变量和函数声明的机制*，它是*执行上下文（Execution Context）的重要组成部分*。环境记录在 JavaScript 的*作用域（Scope）、闭包（Closure）以及变量查找中扮演了核心角色*。

---

## 1. 什么是环境记录？

`环境记录`是一个*抽象的概念*，表示当前执行上下文中存储*变量、函数声明以及 `this` 绑定的地方*。它定义了一个“作用域”的内部实现方式，是 JavaScript 引擎用来*管理变量和作用域*的核心机制。

每个执行上下文（全局执行上下文或函数执行上下文）都有一个与之关联的环境记录。环境记录可以分为以下*两种主要类型：*

1. **声明式环境记录（Declarative Environment Record）**：
   - 用于存储局部变量、函数参数以及函数声明。
   - 常见于函数执行上下文中。
   
2. **对象式环境记录（Object Environment Record）**：
   - 用于存储全局变量和绑定到全局对象（如 `window` 或 `global`）的属性。
   - 常见于全局执行上下文中。

---

## 2. 环境记录的作用

环境记录的主要作用包括：
1. **存储变量和函数声明**：
   - 在代码执行前，变量和函数声明会被提升（Hoisting），并存储在环境记录中。
   
2. **支持作用域链（Scope Chain）**：
   - 每个环境记录会链接到外部环境记录，形成作用域链，用于查找变量。

3. **实现闭包（Closure）**：
   - 当函数返回时，其内部的环境记录可能会被保留，从而实现闭包功能。

4. **绑定 `this`**：
   - 环境记录还负责确定当前执行上下文中的 `this` 值。

---

## **3. 环境记录的生命周期**

环境记录的生命周期与执行上下文紧密相关，主要包括以下几个阶段：

### **(1) 创建阶段**
- 在执行上下文创建时，环境记录也会被初始化。
- 变量和函数声明会被添加到环境记录中，但变量的初始值为 `undefined`。

#### 示例：
```javascript
function foo() {
  var a = 10;
  let b = 20;
  const c = 30;
}

foo();
```
在这个例子中：
- 函数 `foo` 的执行上下文创建时，环境记录会包含 `a`、`b` 和 `c` 的声明。
- `a` 的初始值为 `undefined`，而 `b` 和 `c` 尚未初始化（暂时性死区，Temporal Dead Zone, TDZ）。

---

### **(2) 执行阶段**
- 在代码执行过程中，变量会被赋值，并存储到环境记录中。
- 如果访问的变量不存在于当前环境记录中，则沿作用域链向上查找。

#### 示例：
```javascript
function outer() {
  let x = 10;

  function inner() {
    console.log(x); // 访问外部环境记录中的 x
  }

  inner();
}

outer();
```
在此例中：
- `inner` 函数的环境记录不包含 `x`，因此会沿作用域链找到 `outer` 函数的环境记录。

---

### **(3) 销毁阶段**
- 当执行上下文销毁时，环境记录通常也会被销毁。
- 如果存在闭包，则闭包相关的环境记录会被保留。

#### 示例：
```javascript
function outer() {
  let x = 10;

  function inner() {
    console.log(x);
  }

  return inner;
}

const closureFunc = outer();
closureFunc(); // 输出 10
```
在此例中：
- `outer` 函数执行完毕后，其执行上下文被销毁，但 `inner` 函数形成了闭包，因此 `x` 的环境记录被保留。

---

## **4. 环境记录的类型**

JavaScript 中有两种主要的环境记录类型，每种类型适用于不同的场景。

### **(1) 声明式环境记录（Declarative Environment Record）**
- 存储局部变量、函数参数以及函数声明。
- 不直接暴露给开发者，而是由 JavaScript 引擎内部使用。
- 特点：
  - 支持块级作用域（`let` 和 `const`）。
  - 变量在声明之前不可访问（暂时性死区，Temporal Dead Zone, TDZ）。

#### 示例：
```javascript
function foo() {
  let a = 10; // 存储在声明式环境记录中
  const b = 20;
  var c = 30;

  console.log(a, b, c);
}
```

---

### **(2) 对象式环境记录（Object Environment Record）**
- 用于存储全局变量和绑定到全局对象（如 `window` 或 `global`）的属性。
- 全局变量可以通过全局对象访问（例如 `window.variableName`）。
- 特点：
  - 全局变量是全局对象的属性。
  - 变量提升后可以直接访问。

#### 示例：
```javascript
var globalVar = "I am global";

console.log(window.globalVar); // 输出: I am global（浏览器环境）
```

---

## **5. 环境记录与作用域链**

环境记录通过作用域链连接在一起，形成变量查找的路径。作用域链的工作原理如下：
1. 当访问一个变量时，JavaScript 引擎首先在当前环境记录中查找。
2. 如果找不到，则沿作用域链向上查找，直到找到该变量或到达全局环境记录。

#### 示例：
```javascript
function outer() {
  let x = 10;

  function inner() {
    let y = 20;
    console.log(x + y); // x 来自外部环境记录，y 来自当前环境记录
  }

  inner();
}

outer();
```
在此例中：
- `inner` 的环境记录包含 `y`。
- `x` 不存在于 `inner` 的环境记录中，因此沿作用域链找到 `outer` 的环境记录。

---

## **6. 环境记录与闭包**

闭包的核心机制依赖于环境记录的保留。当一个函数返回时，如果其内部函数引用了外部变量，则外部函数的环境记录会被保留。

#### 示例：
```javascript
function createCounter() {
  let count = 0;

  return function() {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 输出: 1
console.log(counter()); // 输出: 2
```
在此例中：
- `createCounter` 执行完毕后，其环境记录（包含 `count`）被保留。
- 返回的匿名函数形成了闭包，能够继续访问 `count`。

---

## **7. 总结**

环境记录是 JavaScript 中管理变量和作用域的核心机制，具有以下特点：
1. **存储变量和函数声明**：包括局部变量、全局变量和函数参数。
2. **支持作用域链**：通过作用域链实现变量查找。
3. **实现闭包**：保留环境记录，支持函数访问外部变量。
4. **绑定 `this`**：在全局环境中绑定 `this` 到全局对象。

理解环境记录的工作原理有助于掌握 JavaScript 的作用域、闭包以及变量提升等核心概念，从而编写更高效、可靠的代码。
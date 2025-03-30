JavaScript 的 **变量环境（Variable Environment）** 是执行上下文（Execution Context）的*核心组成部分*，专门用于*管理 `var` 声明的变量和函数声明*。它是 _ES6 之前 JavaScript 作用域机制的基石_，与后续引入的 **词法环境（Lexical Environment）** 共同支撑现代作用域规则。

---

### 一、变量环境的核心特性

#### 1. 变量提升（Hoisting）

- `var` 变量在代码解析阶段被提升到作用域顶部，并初始化为 `undefined`。
- **函数声明** 整体提升（优先级高于变量提升）。

```javascript
console.log(a); // undefined（变量提升）
var a = 10;

func(); // 正常执行（函数提升）
function func() {
  console.log("Hello");
}
```

#### 2. 函数作用域（Function Scope）

- `var` 变量的作用域是*函数级*，而*非块级*（如 `if`、`for` 等块无法限制 `var`）。

```javascript
function example() {
  if (true) {
    var x = 10; // 变量 x 属于整个函数作用域
  }
  console.log(x); // 10（穿透块作用域）
}
```

#### 3. 重复声明不报错

- 同一作用域内 `var` 变量可重复声明，后者覆盖前者。

```javascript
var a = 1;
var a = 2; // 合法，a 最终为 2
```

---

### 二、变量环境的结构

变量环境是一个**环境记录（Environment Record）**，存储以下内容：

1. _`var` 声明的变量_（初始值为 `undefined`）。
2. _函数声明_（整体提升，可直接调用）。
3. _函数参数_（形参赋值）。

**示例：**

```javascript
function test(b) {
  // 形参 b 存储在变量环境
  var c = 20; // var 变量 c
  function inner() {} // 函数声明 inner
}
test(10);
```

- 变量环境初始化：
  ```javascript
  // 创建阶段：
  VariableEnvironment = {
    b: 10,               // 形参传入值
    c: undefined,        // var 变量提升
    inner: <function>    // 函数声明提升
  }
  ```

---

### 三、变量环境 vs. 词法环境

**ES6 引入 `let`/`const` 后，执行上下文拆分为两个环境**：

| **特性**       | 变量环境（Variable Environment） | 词法环境（Lexical Environment） |
| -------------- | -------------------------------- | ------------------------------- |
| **存储内容**   | `var` 变量、函数声明、形参       | `let`、`const` 变量、块级作用域 |
| **作用域规则** | 函数作用域                       | 块级作用域                      |
| **变量提升**   | 提升并初始化为 `undefined`       | 提升但处于暂时性死区（TDZ）     |
| **重复声明**   | 允许                             | 不允许                          |
| **闭包依赖**   | 直接参与闭包                     | 通过作用域链引用                |

---

### 四、变量环境与作用域链

变量环境是作用域链的起点，变量查找遵循：

1. **当前变量环境** → 2. **外层词法环境** → ... → **全局变量环境**。

**闭包示例：**

```javascript
function outer() {
  var x = 10; // 存储在 outer 的变量环境
  return function inner() {
    console.log(x); // 通过作用域链找到 outer 的变量环境
  };
}
const closure = outer();
closure(); // 输出 10（闭包保留对变量环境的引用）
```

---

### 五、变量环境的潜在问题

#### 1. 变量污染

- `var` 穿透块作用域导致变量意外覆盖。

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i)); // 输出 3, 3, 3
}
```

#### 2. 提升引发的逻辑错误

- 变量未声明先使用，导致 `undefined`。

```javascript
console.log(a); // undefined（而非报错）
var a = 5;
```

#### 3. 全局变量挂载

- 非严格模式下，未声明的变量赋值会泄露到全局。

```javascript
function leak() {
  accidentalGlobal = 100; // 自动成为 window.accidentalGlobal
}
leak();
console.log(accidentalGlobal); // 100
```

---

### 六、最佳实践

1. **优先使用 `let`/`const`**  
   避免变量提升和块作用域穿透问题。

   ```javascript
   for (let i = 0; i < 3; i++) {
     setTimeout(() => console.log(i)); // 输出 0, 1, 2
   }
   ```

2. **避免依赖函数提升**  
   函数表达式更安全，防止意外覆盖。

   ```javascript
   const func = () => {
     /* ... */
   }; // 推荐使用函数表达式
   ```

3. **严格模式（`'use strict'`）**  
   禁止隐式全局变量，提前发现错误。
   ```javascript
   "use strict";
   undeclaredVar = 10; // 抛出 ReferenceError
   ```

---

### 七、总结

- **变量环境** 是 JavaScript 早期作用域机制的体现，处理 `var` 和函数声明。
- **函数作用域** 和 **变量提升** 是其主要特点，但也易引发逻辑问题。
- 现代开发中，应优先使用 `let`/`const` 配合词法环境，以更精确地控制作用域。

理解变量环境有助于诊断遗留代码的问题，并编写更健壮、可维护的 JavaScript 程序。

JavaScript 的 `词法环境（Lexical Environment）` 是 **ES6 引入**的核心概念，用于*管理块级作用域和 `let`/`const` 变量*的行为。它与 **变量环境（Variable Environment）** *共同构成执行上下文（Execution Context）*，支撑现代作用域规则。

---

### 一、词法环境的核心特性
#### 1. 块级作用域（Block Scope）
   - `let` 和 `const` 声明的变量仅在**代码块**（如 `{}`、`if`、`for`）内有效。
   ```javascript
   if (true) {
     let a = 10;
     const b = 20;
     console.log(a); // 10
   }
   console.log(a); // ReferenceError: a is not defined
   ```

#### 2. 暂时性死区（Temporal Dead Zone, TDZ）
   - 变量在声明前不可访问，否则抛出 `ReferenceError`。
   ```javascript
   console.log(x); // ReferenceError: x is not defined
   let x = 5;
   ```

#### 3. 不可重复声明
   - 同一作用域内 `let`/`const` 变量不可重复声明。
   ```javascript
   let a = 1;
   let a = 2; // SyntaxError: Identifier 'a' has already been declared
   ```

---

### 二、词法环境的结构
词法环境由两部分组成：
1. **环境记录（Environment Record）**  
   - 存储当前作用域内的变量绑定（`let`/`const`）。
   - 类型：
     - **声明式环境记录**：用于函数、块级作用域（存储 `let`/`const`）。
     - **对象式环境记录**：用于 `with` 语句或全局对象（存储 `var` 变量）。
2. **对外部词法环境的引用（Outer Reference）**  
   - 指向父级词法环境，形成作用域链。

```javascript
function outer() {
  let x = 10; // outer 的词法环境
  if (true) {
    let y = 20; // 新块级词法环境
    console.log(x + y); // 通过外部引用访问 x
  }
}
```

---

### 三、词法环境与作用域链
作用域链通过 **外部引用（Outer）** 串联，逐层向上查找变量：
1. 当前词法环境 → 2. 外部词法环境 → ... → 全局词法环境。

#### 闭包的本质
闭包是函数与其定义时的词法环境的组合。内部函数保留对外部环境的引用，即使外部函数已执行完毕。
```javascript
function outer() {
  let x = 10;
  return function inner() {
    console.log(x); // 通过作用域链访问 outer 的词法环境
  };
}
const closure = outer();
closure(); // 输出 10（闭包生效）
```

---

### 四、词法环境 vs. 变量环境
| **特性**               | 词法环境（Lexical Environment）       | 变量环境（Variable Environment）       |
|------------------------|---------------------------------------|----------------------------------------|
| **存储内容**           | `let`、`const`、块级变量              | `var` 变量、函数声明、形参             |
| **作用域规则**         | 块级作用域                            | 函数作用域                             |
| **变量提升**           | 提升但处于 TDZ（不可访问）            | 提升并初始化为 `undefined`             |
| **重复声明**           | 不允许                                | 允许                                   |
| **环境记录类型**       | 声明式环境记录                        | 对象式环境记录（全局）或声明式（函数） |

---

### 五、词法环境的执行流程示例
#### 1. 块级作用域与循环
```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i)); // 输出 0, 1, 2
}
```
- 每次循环迭代创建一个 **新的块级词法环境**，`i` 的值独立保存。

#### 2. 暂时性死区（TDZ）
```javascript
{
  console.log(a); // ReferenceError: a is not defined
  let a = 10;
}
```

---

### 六、词法环境与异步代码
#### 事件循环中的闭包
异步回调通过作用域链访问定义时的词法环境。
```javascript
function asyncTask() {
  let data = "Loaded";
  setTimeout(() => {
    console.log(data); // 闭包保留对 data 的引用
  }, 1000);
}
asyncTask(); // 1秒后输出 "Loaded"
```

---

### 七、常见问题与解决方案
#### 1. 循环中的闭包陷阱（历史问题）
- **问题**：`var` 导致循环变量共享。
  ```javascript
  for (var i = 0; i < 3; i++) {
    setTimeout(() => console.log(i)); // 输出 3, 3, 3
  }
  ```
- **解决**：使用 `let` 创建块级作用域。
  ```javascript
  for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i)); // 输出 0, 1, 2
  }
  ```

#### 2. 意外的全局变量
- **问题**：未声明直接赋值，污染全局（严格模式下报错）。
  ```javascript
  function leak() {
    x = 100; // 严格模式下抛出 ReferenceError
  }
  leak();
  console.log(x); // 非严格模式下输出 100
  ```
- **解决**：使用 `let`/`const` 并启用严格模式。

---

### 八、总结
- **词法环境** 是 JavaScript 实现块级作用域和 `let`/`const` 的底层机制。
- **块级作用域** 和 **暂时性死区** 解决了 `var` 的历史问题，提升代码健壮性。
- **闭包** 依赖词法环境的作用域链，实现对外部变量的持久引用。
- 现代开发中，优先使用 `let`/`const` 替代 `var`，以利用词法环境的优势。

理解词法环境有助于编写可预测、低风险的代码，并深入掌握闭包、异步编程等高级特性。
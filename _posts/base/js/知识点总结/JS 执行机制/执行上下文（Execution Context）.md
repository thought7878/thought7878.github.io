JavaScript 的 `执行上下文（Execution Context）` 是**代码运行的核心环境，执行一段代码时的运行环境**，它定义了*变量、函数和 `this`* 的访问规则。理解执行上下文是掌握作用域、闭包、`this` 绑定的关键。

---

### 一、执行上下文的本质
执行上下文是一个*抽象容器*，包含以下三部分：
1. **变量环境（Variable Environment）**：*存储 `var` 声明的变量和函数声明*。参考：[[变量环境（Variable Environment）]]
2. **词法环境（Lexical Environment）**：*存储 `let`、`const` 声明的变量和块级作用域*。参考：[[词法环境（Lexical Environment）]]
3. **`this` 绑定**：指向当前执行环境的上下文对象。

```javascript
function example(c) {// 存储在变量环境
  var a = 1;       // 存储在变量环境
  let b = 2;       // 存储在词法环境
  console.log(this); // this 绑定
}
```

---

### 二、执行上下文的生命周期
#### 1. 创建阶段
- **初始化变量环境**：
	- `var` *变量提升*，初始化为 `undefined`。
	- *函数声明整体提升*。
- **初始化词法环境**：
	- `let`/`const` 变量进入暂时性死区（TDZ），不可访问。
- **建立作用域链（Scope Chain）**：用于查找变量。
- **确定 `this`**：
	- 根据调用方式绑定（全局、对象方法、构造函数等）。


#### 2. 执行阶段
- 逐行执行代码，修改变量值。
- 处理函数调用（创建新的执行上下文）。

#### 3. 销毁阶段
- 执行完毕，上下文*从调用栈弹出*。
- ***闭包**可能保留对外部环境的引用，阻止销毁*。

---

### 三、执行上下文的类型
#### 1. 全局执行上下文
- 在*代码首次运行时*创建，*唯一且全局存在*。
- `this` 指向全局对象（浏览器中为 `window`，Node.js 中为 `global`）。
- 变量环境和词法环境为全局作用域。

#### 2. 函数执行上下文
- *每次函数调用时创建*，独立于其他函数。
- `this` 由调用方式决定（见下表）。
- `作用域链`基于函数定义时的词法环境。

#### 3. Eval 执行上下文
- 在 `eval()` 函数内部代码执行时创建（极少使用，*存在安全风险*）。

---

### 四、执行上下文栈（Call Stack）
JavaScript 通过 **调用栈（后进先出）** 管理执行上下文：
1. 全局上下文首先入栈。
2. 函数调用时，其上下文入栈并成为“当前上下文”。
3. 函数执行完毕，上下文出栈，控制权交回上一上下文。

```javascript
function first() {
  console.log("First");
  second(); // 调用 second()
}
function second() {
  console.log("Second");
}
first(); // 调用栈顺序：global → first → second
// 输出顺序：First → Second
```

---

### 五、`this` 绑定的规则
执行上下文中 `this` 的值由调用方式决定：

| **调用方式**           | **`this` 指向**               | 示例                          |
|------------------------|------------------------------|-------------------------------|
| 全局调用               | 全局对象（`window`/`global`） | `func()`                      |
| 对象方法调用           | 调用该方法的对象              | `obj.method()`                |
| `new` 构造函数调用     | 新创建的对象                  | `new Person()`                |
| `call`/`apply`/`bind`  | 手动指定的对象                | `func.call(obj)`              |
| 箭头函数               | 继承外层词法环境的 `this`     | `() => { console.log(this); }` |

---

### 六、作用域链与闭包
#### 1. 作用域链的形成
- **每个词法环境都有一个对外部词法环境的引用（`outer`）**。
- 变量查找沿作用域链逐级向上，直到全局环境。

#### 2. 闭包的本质
`闭包`是**函数与其定义时的词法环境的组合**。即使外层函数执行完毕，闭包仍能访问其变量环境。

```javascript
function outer() {
  let x = 10;
  return function inner() {
    console.log(x); // 通过作用域链访问 outer 的 x
  };
}
const closure = outer();
closure(); // 输出 10（闭包保留对 x 的引用）
```

---

### 七、执行上下文与异步代码
#### 1. 同步与异步的执行流程
- 同步代码直接进入调用栈执行。
- 异步任务（如 `setTimeout`、`Promise`）通过事件循环处理：
  - 异步任务完成后，回调函数被推入任务队列。
  - 调用栈为空时，事件循环将队列中的回调推入栈执行。

#### 2. 示例：宏任务与微任务
```javascript
console.log("Start"); // 同步

setTimeout(() => console.log("Timeout"), 0); // 宏任务

Promise.resolve().then(() => console.log("Promise")); // 微任务

console.log("End"); // 同步

// 输出顺序：Start → End → Promise → Timeout
```

---

### 八、常见问题与解决方案
#### 1. 变量污染
- **问题**：`var` 变量穿透块级作用域。
- **解决**：使用 `let`/`const` 替代 `var`。

#### 2. `this` 丢失
- **问题**：回调函数中 `this` 指向错误。
- **解决**：使用箭头函数或 `bind` 绑定 `this`。

#### 3. 闭包内存泄漏
- **问题**：闭包长期引用外部变量，导致内存无法释放。
- **解决**：手动解除引用（如 `closure = null`）。

---

### 九、总结
- **执行上下文** 是 JavaScript 代码执行的基石，管理变量、作用域和 `this`。
- **变量环境** 和 **词法环境** 分别处理 `var` 和 `let`/`const` 的作用域规则。
- **调用栈** 和 **事件循环** 协同工作，实现单线程下的异步非阻塞执行。

理解执行上下文能帮助开发者写出更可靠、高效的代码，并深入掌握闭包、`this`、异步等高级特性。

### 相关资料
- [[_Execution Context，Environment Record]]
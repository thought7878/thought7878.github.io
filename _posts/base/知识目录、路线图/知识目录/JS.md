以下是 JavaScript 知识点的系统性总结，涵盖基础到高级内容，方便你按模块复习：

---

### 一、JavaScript 基础语法

1. **变量与数据类型**

   - 变量声明：`var`、`let`、`const` 的区别（作用域、提升、重复声明）
   - 数据类型：7 种原始类型（`number`, `string`, `boolean`, `null`, `undefined`, `symbol`, `bigint`） + `object`
   - 类型检测：`typeof`、`instanceof`、`Object.prototype.toString.call()`
   - 类型转换：显式转换（`Number()`, `String()`）与隐式转换（\=\= vs \=\=\=）

2. **运算符与表达式**

   - 算术运算符：`+`、`-`、`*`、`/`、`%`、`**`
   - 逻辑运算符：`&&`、`||`、`!`、`??`（空值合并）
   - 比较运算符：\=\=、\=\=\=、`>`、`<`、`>=`、`<=`
   - 位运算符：`&`、`|`、`~`、`^`、`<<`、`>>`、`>>>`

3. **流程控制**
   - 条件语句：`if...else`、`switch`
   - 循环语句：`for`、`while`、`do...while`、`for...of`、`for...in`
   - 控制关键字：`break`、`continue`、`return`、`throw`

---

### 二、核心概念

1. **作用域与闭包**

   - 作用域类型：全局作用域、函数作用域、块级作用域（`let`/`const`）
   - 作用域链与变量查找规则
   - 闭包原理与应用场景（模块化、私有变量）

2. **`this` 绑定规则**

   - 默认绑定（全局对象）
   - 隐式绑定（对象方法调用）
   - 显式绑定（`call`、`apply`、`bind`）
   - `new` 绑定（构造函数）
   - 箭头函数的 `this` 继承

3. **原型与继承**

   - 原型链：`__proto__`、`prototype`
   - 构造函数与 `new` 的原理
   - 继承方式：原型链继承、组合继承、寄生组合继承、Class 继承
   - `Object.create()`、`Object.assign()`

4. **异步编程**
   - 回调函数（Callback）
   - `Promise` 链式调用与静态方法（`all`、`race`、`allSettled`）
   - `async/await` 语法糖
   - 事件循环（Event Loop）：宏任务（`setTimeout`）与微任务（`Promise.then`）
   - `Generator` 与 `yield`

---

### 三、高级特性

1. **ES6+ 新特性**

   - 箭头函数、解构赋值、模板字符串
   - 默认参数、剩余参数（`...`）
   - 类（`class`）、继承（`extends`）、静态方法（`static`）
   - 模块化（`import`/`export`）
   - `Map`、`Set`、`WeakMap`、`WeakSet`
   - `Proxy` 与 `Reflect`。[[Proxy]]、[[Reflect]]
   - 可选链（`?.`）、空值合并（`??`）

2. **函数进阶**

   - 函数式编程（纯函数、不可变性）。[[函数式编程]]
   - 高阶函数（函数作为参数或返回值）。[[高阶函数]]
   - 柯里化（Currying）与偏函数。[[柯里化（Currying）与偏函数]]
   - 防抖（Debounce）与节流（Throttle）。[[防抖（Debounce）与节流（Throttle）]]

3. **错误处理**
[[错误处理]]
   - `try...catch...finally`
   - 错误类型：`Error`、`SyntaxError`、`TypeError` 等
   - 自定义错误与 `throw`

4. **浏览器相关**
   - DOM 操作：节点增删改查、事件监听（`addEventListener`）
   - BOM 对象：`window`、`location`、`history`、`navigator`
   - 事件模型：冒泡、捕获、委托
   - AJAX 与 `fetch` API
   - Web Storage：`localStorage`、`sessionStorage`

---

### 四、运行机制与底层原理

1. **执行上下文与调用栈**
[[执行上下文（Execution Context）]]
   - 执行上下文类型：全局、函数、eval
   - 变量环境（`var`）与词法环境（`let`/`const`）
   - 作用域链与闭包的内存管理

2. **内存管理**
[[内存管理：栈空间、堆空间、代码空间]]、
   - 垃圾回收机制：标记清除、引用计数
   - 内存泄漏场景（全局变量、未清除的定时器、闭包滥用）

3. **事件循环（Event Loop）**
   - 调用栈、任务队列（宏任务、微任务）
   - `setTimeout`、`Promise`、`requestAnimationFrame` 的执行顺序
   - Node.js 与浏览器事件循环差异

---

### 五、工具与生态

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

### 六、最佳实践与常见问题

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

### 七、面试重点总结

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

### 学习资源推荐

- **书籍**：《JavaScript 高级程序设计》《你不知道的 JavaScript》
- **文档**：[MDN Web Docs](https://developer.mozilla.org/zh-CN/)
- **练习**：[LeetCode](https://leetcode.com/)、[Codewars](https://www.codewars.com/)
- **视频**：慕课网、B 站技术博主的系统课程

---

按此框架逐一攻克，*重点关注核心概念与高频面试题*，辅以代码实践，即可系统掌握 JavaScript！

CommonJS 是一种为 JavaScript 设计的模块规范，它最初是为服务器端 JavaScript 环境（如 Node.js）所创建的，用于在不同的文件中组织和管理代码，实现代码的模块化和复用。

### 基本概念

在 CommonJS 规范下，每个文件都被视为一个独立的模块，模块内部的变量和函数默认是私有的，不会污染全局作用域。模块可以通过特定的语法导出其内部的变量、函数或对象，其他模块则可以通过导入语法使用这些导出的内容。

### 语法

#### 导出模块内容

在 CommonJS 中，使用 `module.exports` 或 `exports` 来导出模块中的变量、函数或对象。

**使用 `module.exports` 导出单个值**

```javascript
// math.js
// 定义一个函数
function add(a, b) {
  return a + b;
}

// 使用 module.exports 导出该函数
module.exports = add;
```

**使用 `module.exports` 导出多个值**

```javascript
// math.js
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

// 使用 module.exports 导出多个函数
module.exports = {
  add: add,
  subtract: subtract,
};

// 也可以使用对象字面量的简写形式
module.exports = {
  add,
  subtract,
};
```

**使用 `exports` 导出多个值**
`exports` 是 `module.exports` 的一个引用，通过 `exports` 可以方便地导出多个属性。

```javascript
// math.js
function multiply(a, b) {
  return a * b;
}

function divide(a, b) {
  return a / b;
}

// 使用 exports 导出多个函数
exports.multiply = multiply;
exports.divide = divide;
```

#### 导入模块内容

使用 `require()` 函数来导入其他模块导出的内容。

```javascript
// main.js
// 导入单个值
const add = require("./math.js");
console.log(add(2, 3)); // 5

// 导入多个值
const math = require("./math.js");
console.log(math.add(2, 3)); // 5
console.log(math.subtract(5, 2)); // 3
```

### 特点

#### 同步加载

CommonJS 模块是同步加载的，即当使用 `require()` 导入一个模块时，Node.js 会暂停当前模块的执行，直到被导入的模块加载完成并执行完毕。这对于服务器端环境来说通常是可行的，因为服务器端的文件系统操作速度相对较快。

#### 动态加载

CommonJS 支持动态加载模块，你可以在代码中根据条件动态地决定是否加载某个模块。例如：

```javascript
if (someCondition) {
  const module = require("./someModule.js");
  // 使用 module
}
```

#### 缓存机制

Node.js 对 CommonJS 模块采用了缓存机制。当第一次使用 `require()` 导入某个模块时，Node.js 会执行该模块的代码，并将导出的内容缓存起来。后续再次导入同一个模块时，直接从缓存中获取导出的内容，而不会再次执行模块代码。

### 与 ES Module 的对比

#### 语法差异

- **CommonJS**：使用 `module.exports` 或 `exports` 导出，`require()` 导入。
- **ES Module**：使用 `export` 导出（包括命名导出和默认导出），`import` 导入。

#### 加载方式

- **CommonJS**：同步加载，适合服务器端环境。
- **ES Module**：异步加载，在浏览器中不会阻塞页面渲染。

#### 静态与动态

- **CommonJS**：支持动态加载，根据条件决定是否加载模块。
- **ES Module**：静态结构，在编译时就确定导入和导出关系，便于 Tree Shaking 等优化。

### 总结

CommonJS 是一种广泛应用于 Node.js 环境的模块规范，它通过 `module.exports` 或 `exports` 导出模块内容，使用 `require()` 导入模块内容。其特点包括同步加载、动态加载和缓存机制。虽然 ES Module 逐渐成为 JavaScript 模块系统的标准，但 CommonJS 仍然在 Node.js 社区中被大量使用。

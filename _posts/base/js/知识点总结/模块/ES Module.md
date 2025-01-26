ES Module（ES6 模块）是 JavaScript 在 ES6（ES2015）版本中引入的官方模块系统，它为 JavaScript 提供了一种标准的、静态的模块定义和导入导出机制，用于更好地组织和管理代码。以下从基本概念、导入导出语法、模块加载、特点等方面详细介绍 ES Module。

### 基本概念

在 ES Module 之前，JavaScript 没有官方的模块系统，开发者主要使用 CommonJS（用于 Node.js）和 AMD（如 RequireJS）等第三方模块系统。ES Module 为浏览器和 Node.js 环境都提供了统一的模块解决方案，使得代码可以更好地复用和维护。

### 导出语法

ES Module 提供了两种导出方式：命名导出（Named Exports）和默认导出（Default Exports）。

#### 命名导出

命名导出允许你从一个模块中导出多个变量、函数或类，导出时需要指定名称。可以在定义时直接导出，也可以在最后统一导出。

```javascript
// math.js
// 直接导出
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

// 或者统一导出
const multiply = (a, b) => a * b;
const divide = (a, b) => a / b;
export { multiply, divide };
```

#### 默认导出

每个模块只能有一个默认导出，默认导出可以是一个变量、函数或类，导出时不需要指定名称。

```javascript
// greet.js
const greet = (name) => `Hello, ${name}!`;
export default greet;
```

### 导入语法

根据不同的导出方式，有不同的导入方式。

#### 导入命名导出

```javascript
// main.js
// 导入指定的命名导出
import { add, subtract } from "./math.js";
console.log(add(2, 3)); // 5
console.log(subtract(5, 2)); // 3

// 导入所有命名导出，使用别名
import * as math from "./math.js";
console.log(math.multiply(2, 3)); // 6
```

#### 导入默认导出

```javascript
// main.js
import greet from "./greet.js";
console.log(greet("John")); // Hello, John!
```

#### 同时导入默认导出和命名导出

```javascript
// 假设 someModule.js 同时有默认导出和命名导出
// someModule.js
export default function defaultFunc() {
  console.log("This is a default function.");
}
export const namedValue = 10;

// main.js
import defaultFunc, { namedValue } from "./someModule.js";
defaultFunc(); // This is a default function.
console.log(namedValue); // 10
```

### 模块加载

#### 在浏览器中使用 ES Module

在浏览器中使用 ES Module，需要在 `<script>` 标签中添加 `type="module"` 属性。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
  </head>
  <body>
    <script type="module">
      import { add } from "./math.js";
      console.log(add(1, 2));
    </script>
  </body>
</html>
```

注意：在浏览器中，模块路径必须是相对路径或绝对路径，不能省略 `.js` 扩展名。

#### 在 Node.js 中使用 ES Module

从 Node.js 13.2.0 版本开始，Node.js 正式支持 ES Module。要在 Node.js 中使用 ES Module，有两种方式：

- **使用 `.mjs` 扩展名**：将模块文件的扩展名改为 `.mjs`，然后使用 `node` 命令运行。

```javascript
// math.mjs
export const add = (a, b) => a + b;

// main.mjs
import { add } from "./math.mjs";
console.log(add(1, 2));
```

在终端中运行：

```sh
node main.mjs
```

- **在 `package.json` 中设置 `"type": "module"`**：将 `package.json` 文件中的 `"type"` 字段设置为 `"module"`，这样 `.js` 文件也会被当作 ES Module 处理。

```json
{
  "type": "module"
}
```

### ES Module 的特点

#### 静态结构

ES Module 的导入和导出是静态的，在编译时就可以确定。这使得工具（如打包工具、静态分析工具）可以更好地优化代码，例如进行 Tree Shaking（去除未使用的代码）。

#### 顶层 `this` 为 `undefined`

在 ES Module 的顶层作用域中，`this` 的值是 `undefined`，而不是全局对象（在浏览器中是 `window`，在 Node.js 中是 `global`）。

#### 异步加载

ES Module 在浏览器中是异步加载的，不会阻塞页面的渲染。

### 总结

ES Module 为 JavaScript 提供了一种标准的、统一的模块系统，通过命名导出和默认导出的方式可以方便地导出和导入模块。它具有静态结构、异步加载等特点，在浏览器和 Node.js 环境中都可以使用，有助于提高代码的可维护性和复用性。

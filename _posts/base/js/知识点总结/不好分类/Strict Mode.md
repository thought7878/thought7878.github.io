JavaScript 的严格模式（Strict Mode）是 ECMAScript 5 引入的一项特性，_它为代码的执行提供了更严格的语法和错误处理规则_。下面从严格模式的启用方式、特点、优势和使用场景等方面进行详细介绍。

### 启用严格模式

严格模式可以在*全局作用域*或*函数作用域*中启用，有以下两种方式：

#### 1. 全局启用

在整个脚本文件的开头添加 `"use strict";` 指令，即可在全局作用域启用严格模式。

```javascript
"use strict";
// 下面的代码都将在严格模式下执行
let num = 10;
function example() {
  // 此函数内部也是严格模式
}
```

#### 2. 函数内部启用

在函数体的开头添加 `"use strict";` 指令，*仅该函数内部的代码*会在严格模式下执行。

```javascript
function strictFunction() {
  "use strict";
  // 此函数内部是严格模式
  let localVar = 20;
}

function normalFunction() {
  // 此函数内部不是严格模式
  let anotherVar = 30;
}
```

### 严格模式的特点

#### 1. 禁止未声明的变量

在严格模式下，使用未声明的变量会抛出 `ReferenceError` 错误。而在非严格模式下，使用未声明的变量会隐式地创建一个全局变量。

```javascript
"use strict";
x = 10;
// 会抛出 ReferenceError: x is not defined
```

#### 2. 禁止删除不可删除的属性

在严格模式下，尝试删除不可删除的属性会抛出 `TypeError` 错误。非严格模式下，删除不可删除的属性会失败，但不会报错。

```javascript
"use strict";
delete Object.prototype;
// 会抛出 TypeError
```

#### 3. 禁止重复的参数名

在严格模式下，函数不能有重复的参数名。如果有重复参数名，会抛出 `SyntaxError` 错误。非严格模式下，重复的参数名是允许的。

```javascript
"use strict";
function duplicateParams(a, a) {
  // 会抛出 SyntaxError
}
```

#### 4. `this` 指针的不同处理

- 在严格模式下，函数内部的 `this` 不会自动指向全局对象。如果函数作为普通函数调用，_`this` 的值为 `undefined`_。

```javascript
"use strict";
function testThis() {
  console.log(this);
}
testThis();
```

- 而在非严格模式下，函数作为普通函数调用时，_`this` 指向全局对象（在浏览器中是 `window` 对象）_。

#### 5. 禁止八进制字面量

在严格模式下，不允许使用八进制字面量（以 `0` 开头的数字）。如果使用，会抛出 `SyntaxError` 错误。

```javascript
"use strict";
let octalNum = 010;
// 会抛出 SyntaxError
```

#### 6. 禁止使用 `with` 语句

`with` 语句可以将一个对象的属性添加到当前作用域中，但会使代码的可读性和性能变差，并且可能导致意外的变量冲突。在严格模式下，使用 `with` 语句会抛出 `SyntaxError` 错误。

```javascript
"use strict";
const obj = { prop: "value" };
with (obj) {
  // 会抛出 SyntaxError
  console.log(prop);
}
```

### 严格模式的优势

#### 1. 提高代码质量

严格模式*通过更严格的语法规则*，帮助开发者*避免一些常见的编程错误*，如未声明变量、重复参数名等，从而提高代码的可靠性和可维护性。

#### 2. 便于调试

严格模式会在代码出现潜在问题时立即抛出错误，而不是默默失败或产生不可预期的行为，这使得调试过程更加高效。

#### 3. 为未来的 JavaScript 版本做准备

随着 JavaScript 的不断发展，新的版本可能会采用更严格的语法规则。使用严格模式可以让开发者提前适应这些变化，使代码更具前瞻性。

### 使用场景

- **大型项目**：在大型项目中，代码的复杂度较高，使用严格模式可以帮助团队成员编写更规范、更安全的代码，减少潜在的错误。
- **新的 JavaScript 特性**：一些新的 JavaScript 特性可能只在严格模式下支持，为了使用这些特性，需要启用严格模式。

## ES 模块与严格模式的关系

### 自动启用严格模式

ES 模块*默认自动开启严格模式*，无需额外使用 `"use strict";` 指令来声明。*当你使用 ES 模块的语法（如 `import` 和 `export`）时*，模块内的代码会自动以严格模式运行。

```javascript
// module.js
// 这里不需要显式声明 "use strict";，代码会自动以严格模式运行
export const message = "Hello, ES Modules!";

function test() {
  // 严格模式下不允许未声明就使用变量
  // 下面这行代码会报错
  // x = 10;
  let y = 20;
  return y;
}

export { test };
```

```javascript
// main.js
import { message, test } from "./module.js";
console.log(message);
console.log(test());
```

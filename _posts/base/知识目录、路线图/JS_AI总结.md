JavaScript 是一种广泛用于 Web 开发的高级、动态、弱类型、基于原型的编程语言，下面从基础到高级列出 JavaScript 的主要知识点：

### 基础语法

- **变量与数据类型**
  - **变量声明**：`var`（存在变量提升，函数作用域）、`let`（块级作用域）、`const`（块级作用域，声明常量）。
  - **基本数据类型**：`Number`（数值类型，包含整数和浮点数）、`String`（字符串类型）、`Boolean`（布尔类型，值为 `true` 或 `false`）、`Null`（只有一个值 `null`，表示空对象引用）、`Undefined`（变量声明但未赋值时的默认值）、`Symbol`（ES6 新增，创建独一无二的值）。
  - **引用数据类型**：`Object`（对象类型，*无序的*数据集合）、`Array`（数组类型，*有序的*数据集合）、`Function`（函数类型，*可执行的代码块*）。
- **运算符**
  - **算术运算符**：`+`（加）、`-`（减）、`*`（乘）、`/`（除）、`%`（取余）、`++`（自增）、`--`（自减）。
  - **比较运算符**：`==`（相等）、`===`（严格相等，值和类型都相等）、`!=`（不相等）、`!==`（严格不相等）、`>`（大于）、`<`（小于）、`>=`（大于等于）、`<=`（小于等于）。
  - **逻辑运算符**：`&&`（逻辑与）、`||`（逻辑或）、`!`（逻辑非）。
  - **赋值运算符**：`=`（赋值）、`+=`（加等于）、`-=`（减等于）等。
  - **位运算符**：`&`（按位与）、`|`（按位或）、`^`（按位异或）、`~`（按位取反）、`<<`（左移）、`>>`（右移）、`>>>`（无符号右移）。
- **控制语句**
  - **条件语句**：`if...else`（单条件判断）、`else if`（多条件判断）、`switch...case`（多分支选择）。
  - **循环语句**：`for`（普通 for 循环）、`while`（当条件为真时循环）、`do...while`（至少执行一次循环体）、`for...in`（遍历对象的可枚举属性）、`for...of`（ES6 新增，遍历可迭代对象）。
  - **跳转语句**：`break`（跳出循环）、`continue`（跳过本次循环）、`return`（从函数中返回值并终止函数执行）。

### 函数

- **函数定义**
  - **函数声明**：使用 `function` 关键字定义函数，如 `function add(a, b) { return a + b; }`。
  - **函数表达式**：将函数赋值给一个变量，如 `const add = function(a, b) { return a + b; }`。
  - **箭头函数**：ES6 新增的简洁语法，如 `const add = (a, b) => a + b;`。
- **函数参数**
  - **形参和实参**：函数定义时的参数为形参，调用时传递的参数为实参。
  - **默认参数**：ES6 允许为函数参数设置默认值，如 `function add(a, b = 0) { return a + b; }`。
  - **剩余参数**：使用 `...` 语法收集多余的参数，如 `function sum(...numbers) { return numbers.reduce((acc, num) => acc + num, 0); }`。
- **函数作用域和闭包**
  - **作用域**：函数内部可以访问外部作用域的变量，但外部作用域无法访问函数内部的变量。
  - **闭包**：函数内部返回一个函数，该内部函数可以访问外部函数的变量，即使外部函数已经执行完毕。

### 对象

- **对象创建**
  - **对象字面量**：使用 `{}` 创建对象，如 `const person = { name: 'John', age: 30 };`。
  - **构造函数**：使用 `function` 定义构造函数，通过 `new` 关键字创建对象，如 `function Person(name, age) { this.name = name; this.age = age; } const person = new Person('John', 30);`。
  - **ES6 类**：使用 `class` 关键字定义类，通过 `new` 关键字创建对象，如 `class Person { constructor(name, age) { this.name = name; this.age = age; } } const person = new Person('John', 30);`。
- **对象属性和方法**
  - **属性访问**：使用 `.` 或 `[]` 访问对象的属性，如 `person.name` 或 `person['name']`。
  - **方法定义**：对象的属性值可以是函数，称为对象的方法，如 `const person = { sayHello() { console.log('Hello!'); } }; person.sayHello();`。
- **对象的继承**
  - **原型链继承**：通过原型对象实现继承，每个对象都有一个原型对象，原型对象又有自己的原型对象，以此类推。
  - **构造函数继承**：在子类构造函数中调用父类构造函数，如 `function Child(name, age) { Parent.call(this, name, age); }`。
  - **组合继承**：结合原型链继承和构造函数继承的优点。
  - **ES6 类继承**：使用 `extends` 关键字实现类的继承，如 `class Child extends Parent { constructor(name, age) { super(name, age); } }`。

### 数组

- **数组创建**：使用 `[]` 或 `new Array()` 创建数组，如 `const numbers = [1, 2, 3];` 或 `const numbers = new Array(1, 2, 3);`。
- **数组方法**
  - **遍历方法**：`forEach`（遍历数组）、`map`（返回一个新数组，新数组中的元素是原数组元素经过某种处理后的结果）、`filter`（返回一个新数组，新数组中的元素是原数组中满足某种条件的元素）、`reduce`（将数组元素累积为一个值）等。
  - **添加和删除元素方法**：`push`（在数组末尾添加元素）、`pop`（删除数组末尾的元素）、`unshift`（在数组开头添加元素）、`shift`（删除数组开头的元素）、`splice`（删除、插入或替换数组元素）等。
  - **其他方法**：`concat`（合并数组）、`join`（将数组元素转换为字符串）、`reverse`（反转数组元素的顺序）、`sort`（对数组元素进行排序）等。

### 异步编程

- **回调函数**：将一个函数作为参数传递给另一个函数，在另一个函数执行完毕后调用该函数。
- **Promise**：ES6 引入的一种异步编程解决方案，用于处理异步操作的结果。`Promise` 有三种状态：`pending`（进行中）、`fulfilled`（已成功）、`rejected`（已失败）。可以使用 `then` 方法处理成功的结果，使用 `catch` 方法处理失败的结果。
- **async/await**：ES8 引入的异步编程语法糖，基于 `Promise` 实现，使异步代码看起来更像同步代码。使用 `async` 关键字定义异步函数，使用 `await` 关键字等待 `Promise` 的结果。
- **事件循环**：JavaScript 是单线程的，事件循环负责处理异步任务。事件循环包括任务队列（宏任务队列和微任务队列），异步任务完成后会将回调函数放入相应的任务队列中，事件循环不断从任务队列中取出任务并执行。

### 模块化

- **ES6 模块**：使用 `import` 和 `export` 关键字实现模块的导入和导出。`export` 可以导出变量、函数、类等，`import` 可以导入其他模块导出的内容。
- **CommonJS**：服务器端 JavaScript（如 Node.js）使用的模块化规范，使用 `module.exports` 导出模块内容，使用 `require` 导入模块。
- **AMD 和 CMD**：浏览器端的模块化规范，AMD（异步模块定义）以 RequireJS 为代表，CMD（通用模块定义）以 Sea.js 为代表。

### 浏览器对象模型（BOM）

- **window 对象**：浏览器窗口的全局对象，包含了许多属性和方法，如 `alert`（弹出警告框）、`confirm`（弹出确认框）、`prompt`（弹出提示框）、`setTimeout`（设置定时器）、`setInterval`（设置间隔定时器）等。
- **location 对象**：用于获取和设置当前页面的 URL 信息，如 `location.href`（获取或设置当前页面的 URL）、`location.reload`（重新加载当前页面）等。
- **history 对象**：用于操作浏览器的历史记录，如 `history.back`（返回上一页）、`history.forward`（前进到下一页）、`history.go`（跳转到指定的历史记录位置）等。
- **navigator 对象**：用于获取浏览器的相关信息，如 `navigator.userAgent`（获取浏览器的用户代理字符串）等。

### 文档对象模型（DOM）

- **DOM 节点**：HTML 文档中的每个元素、文本、属性等都是一个 DOM 节点。常见的节点类型有元素节点、文本节点、属性节点等。
- **DOM 操作**
  - **节点选择**：使用 `document.getElementById`（通过 ID 选择元素）、`document.getElementsByTagName`（通过标签名选择元素）、`document.getElementsByClassName`（通过类名选择元素）、`document.querySelector`（通过 CSS 选择器选择第一个匹配的元素）、`document.querySelectorAll`（通过 CSS 选择器选择所有匹配的元素）等方法选择节点。
  - **节点创建和插入**：使用 `document.createElement`（创建元素节点）、`document.createTextNode`（创建文本节点）、`appendChild`（在父节点末尾插入子节点）、`insertBefore`（在指定节点之前插入新节点）等方法创建和插入节点。
  - **节点删除和替换**：使用 `removeChild`（删除子节点）、`replaceChild`（替换子节点）等方法删除和替换节点。
  - **节点属性和样式操作**：可以通过节点的属性（如 `id`、`className` 等）和 `style` 属性操作节点的属性和样式。

### 其他高级特性

- **正则表达式**：用于匹配和处理字符串的模式，使用 `/pattern/flags` 的语法创建正则表达式对象，如 `/abc/i` 表示不区分大小写匹配字符串中的 `abc`。
- **JSON**：JavaScript 对象表示法，是一种轻量级的数据交换格式。使用 `JSON.stringify` 将 JavaScript 对象转换为 JSON 字符串，使用 `JSON.parse` 将 JSON 字符串转换为 JavaScript 对象。
- **元编程**：ES6 引入了 `Proxy` 和 `Reflect` 对象，用于实现元编程。`Proxy` 可以拦截对象的基本操作，如属性访问、属性赋值等；`Reflect` 提供了一系列静态方法，用于操作对象。

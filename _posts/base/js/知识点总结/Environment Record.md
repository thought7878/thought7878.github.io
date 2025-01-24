在 JavaScript 里，**环境记录**（Environment Record）是*词法环境（Lexical Environment）的重要组成部分*，它主要负责**存储变量和函数的定义**，建立标识符（变量名和函数名）与具体值之间的关联。下面从不同方面为你详细介绍环境记录。

### 环境记录的类型

#### 1. 全局环境记录（Global Environment Record）

全局环境记录对应全局作用域，也就是整个 JavaScript 程序的最外层作用域。在浏览器环境中，全局环境记录与全局对象 `window` 相关联。它存储着所有的全局变量和全局函数，其外部环境引用为 `null`。

```javascript
// 全局变量
let globalVariable = "This is a global variable";

function globalFunction() {
  console.log("This is a global function");
}

// 全局环境记录中会存储 globalVariable 和 globalFunction
```

#### 2. 声明式环境记录（Declarative Environment Record）

- **函数环境记录（Function Environment Record）**：当调用一个函数时，会创建一个函数环境记录，它对应函数作用域。函数环境记录会存储函数的参数、局部变量和内部函数。

```javascript
function myFunction(param) {
  let localVar = "This is a local variable";
  // 函数环境记录会存储 param 和 localVar
}

myFunction("test");
```

- **块级环境记录（Block Environment Record）**：使用 `let`、`const` 声明变量时会创建块级环境记录，对应块级作用域，如 `if` 语句块、`for` 循环块等。块级环境记录能限制变量的作用范围，避免变量提升带来的问题。

```javascript
if (true) {
  let blockVar = "This is a block variable";
  // 块级环境记录会存储 blockVar
}
// 这里无法访问 blockVar
```

#### 3. 对象式环境记录（Object Environment Record）

对象式环境记录通常与 `with` 语句或 `catch` 子句相关联。`with` 语句可以将一个对象的属性添加到当前作用域中，`catch` 子句会将捕获的错误对象添加到其作用域中。不过，由于 `with` 语句会使代码的可读性和性能变差，并且可能导致意外的变量冲突，所以在实际开发中不建议使用。

```javascript
const obj = { prop: "value" };
with (obj) {
  console.log(prop);
  // 对象式环境记录会将 obj 的属性添加到作用域中
}
```

### 环境记录的操作

#### 1. 创建绑定（Create Binding）

当声明一个变量或函数时，环境记录会创建一个新的绑定，将标识符与值关联起来。例如，使用 `let`、`const` 或 `var` 声明变量，使用 `function` 声明函数时，都会在相应的环境记录中创建绑定。

```javascript
let newVar = "new value";
// 全局环境记录或块级环境记录会为 newVar 创建绑定
```

#### 2. 设置绑定值（Set Binding Value）

当给已声明的变量赋值时，环境记录会更新相应绑定的值。

```javascript
let num = 10;
num = 20;
// 环境记录会将 num 绑定的值从 10 更新为 20
```

#### 3. 查找绑定（Get Binding Value）

当访问一个变量或函数时，JavaScript 引擎会在当前环境记录中查找对应的绑定。如果找不到，会通过外部环境引用到外部环境记录中继续查找，直到找到绑定或到达全局环境记录。

```javascript
function outer() {
  let outerVar = "outer value";
  function inner() {
    console.log(outerVar);
    // 内部函数的环境记录中没有 outerVar 绑定，会到外部环境记录中查找
  }
  inner();
}

outer();
```

#### 4. 删除绑定（Delete Binding）

使用 `delete` 操作符可以删除环境记录中的绑定，但并不是所有的绑定都可以被删除。例如，使用 `var` 声明的全局变量不能被删除，而使用 `let` 或 `const` 声明的变量也不能被删除。

```javascript
var globalVar = "global";
delete globalVar;
console.log(globalVar);
// 输出 'global'，因为全局的 var 变量不能被删除
```

### 环境记录与作用域和闭包的关系

- **作用域**：环境记录是实现作用域的基础。不同的环境记录对应不同的作用域，通过外部环境引用形成作用域链，使得 JavaScript 引擎能够根据作用域规则查找变量和函数。
- **闭包**：闭包之所以能够访问外部函数的变量，是因为闭包保留了对外部函数环境记录的引用。即使外部函数执行完毕，其环境记录也不会被销毁，闭包仍然可以通过引用访问其中的变量。

```javascript
function outer() {
  let outerVar = "outer value";
  return function () {
    console.log(outerVar);
  };
}

let closure = outer();
closure();
// 闭包可以访问 outer 函数环境记录中的 outerVar
```

通过环境记录，JavaScript 能够有效地管理变量和函数的作用域，确保代码的正确执行和变量的正确访问。

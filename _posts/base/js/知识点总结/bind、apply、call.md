在 JavaScript 中，`Function.prototype.bind`、`Function.prototype.apply` 和 `Function.prototype.call` 是三个重要的方法，它们都与函数的调用上下文（`this` 关键字）有关。下面是对这三个方法的总结：

### 1. Function.prototype.bind
**`bind()`** 是函数对象的一个内置方法，它允许你**永久地绑定一个函数的 `this` 上下文**，并可以**预先填充部分参数**，然后**返回一个新的函数**。
#### 语法
```javascript
function.bind(thisArg[, arg1[, arg2[, ...]]])
```

#### 参数
- `thisArg`：新函数调用时绑定的 this 值。
- `arg1, arg2, ...`：传递给新函数的参数列表。

#### 返回值
返回一个新的函数，该函数在其*调用时具有固定的 this 值、预先填充部分参数*。

#### 示例
```javascript
function greet(greeting, name) {
    console.log(greeting + ', ' + name + '!');
}

const greetJohn = greet.bind(null, 'Hello', 'John');
greetJohn(); // 输出 "Hello, John!"
```

### 2. Function.prototype.apply
`apply` 方法用于*立即调用一个函数*，并指定函数的 `this` 值以及参数数组。

#### 语法
```javascript
function.apply(thisArg[, argsArray])
```

#### 参数
- `thisArg`：函数调用时绑定的 `this` 值。
- `argsArray`：*一个数组或类数组对象，其元素作为函数调用时的参数*。

#### 示例
```javascript
function greet(greeting, name) {
    console.log(greeting + ', ' + name + '!');
}

const names = ['Hello', 'Alice'];
greet.apply(null, names); // 输出 "Hello, Alice!"
```

### 3. Function.prototype.call
`call` 方法用于*立即调用一个函数*，并指定函数的 `this` 值以及参数列表。

#### 语法
```javascript
function.call(thisArg[, arg1[, arg2[, ...]]])
```

#### 参数
- `thisArg`：函数调用时绑定的 `this` 值。
- `arg1, arg2, ...`：传递给函数的参数列表。

#### 示例
```javascript
function greet(greeting, name) {
    console.log(greeting + ', ' + name + '!');
}

greet.call(null, 'Hello', 'Bob'); // 输出 "Hello, Bob!"
```

### 总结
- **`call(thisArg, arg1, arg2, ...)`**：*立即调用函数*，并绑定 `this` 、参数以*参数列表*传递。
- **`apply(thisArg, [argsArray])`**：*立即调用函数*，绑定 `this`、参数以*数组形式*传递。
- **`bind(thisArg, arg1, arg2, ...)`**：**不立即调用**，而是*返回一个绑定了 `this` 和部分参数的新函数*。

---

- **bind**：*创建一个新的函数*，该函数在其调用时具有固定的 `this` 值。通常用于绑定事件处理器或在构造函数中设置 `this` 的值。
- **apply**：*立即调用一个函数*，并指定 `this` 值以及*参数数组*。通常用于合并多个数组或将类数组对象转换为数组。
- **call**：*立即调用一个函数*，并指定 `this` 值以及*参数列表*。通常用于调用一个函数并传递多个参数。

**这些方法都非常有用，尤其是在需要改变函数调用上下文或者动态传递参数时**。理解和掌握这些方法可以帮助你编写更加灵活和强大的 JavaScript 代码。
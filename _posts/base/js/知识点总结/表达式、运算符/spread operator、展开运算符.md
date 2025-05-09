展开运算符（spread operator）是ECMAScript 6（ES6）引入的一种新特性，它使用三个点（`...`）来表示。展开运算符的主要功能是在函数调用、数组字面量、或对象字面量中将一个可迭代的对象（如数组或类数组对象）的元素展开成单独的元素，或者将一个对象的可枚举属性展开成键值对。

### 解决的问题：

1. **函数调用时传递参数**：
   展开运算符可以让你将数组或类数组对象的元素作为单独的参数传递给函数，而不是作为一个整体。这在处理需要多个独立参数的函数时非常有用，尤其是在处理动态参数数量的情况下。

   示例：
   ```javascript
   function sum(a, b, c) {
       return a + b + c;
   }
   let numbers = [1, 2, 3];
   console.log(sum(...numbers)); // 输出 6
   ```

2. **数组的合并**：
   展开运算符可以用来合并数组，无需使用 `concat` 方法。

   示例：
   ```javascript
   let arr1 = [1, 2];
   let arr2 = [3, 4];
   let merged = [...arr1, ...arr2]; // 结果是 [1, 2, 3, 4]
   ```

3. **对象属性的复制和扩展**：
   展开运算符也可以用于对象，可以将一个对象的所有可枚举属性复制到新的对象中，或者将多个对象的属性合并到一个新对象中。

   示例：
   ```javascript
   let obj1 = { x: 1 };
   let obj2 = { y: 2 };
   let combined = { ...obj1, ...obj2, z: 3 }; // 结果是 { x: 1, y: 2, z: 3 }
   ```

4. **参数捕获**：
   在函数内部，展开运算符可以用来捕获任意数量的参数为数组。

   示例：
   ```javascript
   function logArgs(...args) {
       console.log(args); // args 是一个数组，包含了所有传递给函数的参数
   }
   logArgs(1, 2, 3); // 输出 [1, 2, 3]
   ```

### 总结：
展开运算符极大地提高了JavaScript的灵活性和代码的可读性，使得处理数组和对象的操作更为直观和简洁。无论是函数调用、数组操作还是对象属性的复制和扩展，展开运算符都提供了一种优雅的解决方案。
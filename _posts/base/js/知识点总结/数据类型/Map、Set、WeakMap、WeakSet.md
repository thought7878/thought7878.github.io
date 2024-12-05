JavaScript 在其发展历程中不断引入新的数据类型，*以增强语言的功能和表达能力*。以下是一些近年来 JavaScript 新增的数据类型：

### 1. `BigInt`
- **介绍**：`BigInt` 是一种内置对象，用于表示任意精度的整数。它*解决了 JavaScript 中 `Number` 类型无法精确表示大整数的问题*。
- **使用示例**：
  ```javascript
  let bigIntNum = BigInt(1234567890123456789012345678901234567890);
  console.log(bigIntNum); // 1234567890123456789012345678901234567890n

  // 也可以使用后缀 `n` 直接声明
  let anotherBigIntNum = 1234567890123456789012345678901234567890n;
  console.log(anotherBigIntNum); // 1234567890123456789012345678901234567890n
  ```

### 2. `Symbol`
- **介绍**：`Symbol` 是一种*唯一且不可变*的数据类型，*主要用于对象属性的键，以避免属性名冲突*。
- **使用示例**：
  ```javascript
  let sym1 = Symbol('key1');
  let sym2 = Symbol('key1');

  console.log(sym1 === sym2); // false

  let obj = {};
  obj[sym1] = 'value1';
  obj[sym2] = 'value2';

  console.log(obj[sym1]); // value1
  console.log(obj[sym2]); // value2
  ```

### 3. `Promise`
- **介绍**：虽然 `Promise` 早在 ES6 中就被引入，但它是一个重要的异步编程工具，用于*处理异步操作的结果*。
- **使用示例**：
  ```javascript
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('Success!');
    }, 1000);
  });

  promise.then(result => {
    console.log(result); // Success!
  }).catch(error => {
    console.error(error);
  });
  ```

### 4. `Map`
- **介绍**：`Map` 是一个*键值对的集合*，其中*键可以是任何数据类型，而不仅仅是字符串*。
- **使用示例**：
  ```javascript
  let map = new Map();

  map.set('name', 'Alice');
  map.set(1, 'one');
  map.set(true, 'true');

  console.log(map.get('name')); // Alice
  console.log(map.get(1)); // one
  console.log(map.get(true)); // true
  ```

### 5. `Set`
参考 [[Set]]

- **介绍**：`Set` 是一个*不包含重复值的集合*。
- **使用示例**：
  ```javascript
  let set = new Set();

  set.add(1);
  set.add(2);
  set.add(2);

  console.log(set.size); // 2
  console.log(set.has(1)); // true
  console.log(set.has(2)); // true
  console.log(set.has(3)); // false
  ```

### 6. `WeakMap`
参考 [[WeakMap]]

- **介绍**：`WeakMap` 是一个键弱引用的键值对集合，键必须是对象，值可以是任意值。
- **使用示例**：
  ```javascript
  let weakMap = new WeakMap();

  let key1 = {};
  let key2 = {};

  weakMap.set(key1, 'value1');
  weakMap.set(key2, 'value2');

  console.log(weakMap.get(key1)); // value1
  console.log(weakMap.get(key2)); // value2

  key1 = null; // key1 被垃圾回收
  console.log(weakMap.has(key1)); // false
  ```

### 7. `WeakSet`
- **介绍**：`WeakSet` 是一个只包含对象的集合，对象是弱引用的。
- **使用示例**：
  ```javascript
  let weakSet = new WeakSet();

  let obj1 = {};
  let obj2 = {};

  weakSet.add(obj1);
  weakSet.add(obj2);

  console.log(weakSet.has(obj1)); // true
  console.log(weakSet.has(obj2)); // true

  obj1 = null; // obj1 被垃圾回收
  console.log(weakSet.has(obj1)); // false
  ```

### 总结

这些新增的数据类型和对象增强了 JavaScript 的功能，使其能够更好地处理各种复杂的数据结构和异步操作。通过合理使用这些数据类型，你可以编写更高效、更可靠的代码。
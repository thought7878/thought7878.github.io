JavaScript 中的 `Object` 对象提供了许多内置方法，用于处理和操作对象。这些方法可以帮助你创建、修改、查询和删除对象的属性。以下是一些常用的 `Object` 方法及其示例：

### 1. `Object.keys`
- **用途**：返回一个包含对象自身所有可枚举属性的数组。
- **示例**：
  ```javascript
  let obj = { a: 1, b: 2, c: 3 };
  let keys = Object.keys(obj);
  console.log(keys); // ["a", "b", "c"]
  ```

### 2. `Object.values`
- **用途**：返回一个包含对象自身所有可枚举属性值的数组。
- **示例**：
  ```javascript
  let obj = { a: 1, b: 2, c: 3 };
  let values = Object.values(obj);
  console.log(values); // [1, 2, 3]
  ```

### 3. `Object.entries`
- **用途**：返回一个包含对象自身所有可枚举属性的键值对数组。
- **示例**：
  ```javascript
  let obj = { a: 1, b: 2, c: 3 };
  let entries = Object.entries(obj);
  console.log(entries); // [["a", 1], ["b", 2], ["c", 3]]
  ```

### 4. `Object.assign`
- **用途**：将所有可枚举属性的值从一个或多个源对象复制到目标对象。返回目标对象。
- **示例**：
  ```javascript
  let target = { a: 1 };
  let source1 = { b: 2 };
  let source2 = { c: 3 };

  let result = Object.assign(target, source1, source2);
  console.log(result); // { a: 1, b: 2, c: 3 }
  console.log(target); // { a: 1, b: 2, c: 3 }
  ```

### 5. `Object.create`
- **用途**：创建一个新对象，使用现有的对象作为原型。
- **示例**：
  ```javascript
  let prototypeObj = { x: 10 };
  let newObj = Object.create(prototypeObj);
  newObj.y = 20;
  console.log(newObj.x); // 10
  console.log(newObj.y); // 20
  ```

### 6. `Object.defineProperty`
- **用途**：在一个对象上定义一个新属性，或修改现有属性，并返回该对象。
- **示例**：
  ```javascript
  let obj = {};

  Object.defineProperty(obj, 'a', {
    value: 1,
    writable: true,
    enumerable: true,
    configurable: true
  });

  console.log(obj.a); // 1
  ```

### 7. `Object.defineProperties`
- **用途**：在一个对象上定义或修改多个属性，并返回该对象。
- **示例**：
  ```javascript
  let obj = {};

  Object.defineProperties(obj, {
    a: {
      value: 1,
      writable: true
    },
    b: {
      value: 2,
      writable: false
    }
  });

  console.log(obj.a); // 1
  console.log(obj.b); // 2
  ```

### 8. `Object.getOwnPropertyDescriptor`
- **用途**：返回指定对象上一个自有属性对应的属性描述符。
- **示例**：
  ```javascript
  let obj = { a: 1 };

  let descriptor = Object.getOwnPropertyDescriptor(obj, 'a');
  console.log(descriptor); // { value: 1, writable: true, enumerable: true, configurable: true }
  ```

### 9. `Object.getOwnPropertyNames`
- **用途**：返回一个包含对象自身所有可枚举和不可枚举属性名称的数组。
- **示例**：
  ```javascript
  let obj = { a: 1, b: 2 };
  Object.defineProperty(obj, 'c', {
    value: 3,
    enumerable: false
  });

  let names = Object.getOwnPropertyNames(obj);
  console.log(names); // ["a", "b", "c"]
  ```

### 10. `Object.getPrototypeOf`
- **用途**：返回指定对象的原型（内部 `[[Prototype]]` 属性）。
- **示例**：
  ```javascript
  let prototypeObj = { x: 10 };
  let obj = Object.create(prototypeObj);

  let proto = Object.getPrototypeOf(obj);
  console.log(proto); // { x: 10 }
  ```

### 11. `Object.setPrototypeOf`
- **用途**：设置指定对象的原型（内部 `[[Prototype]]` 属性）。
- **示例**：
  ```javascript
  let prototypeObj = { x: 10 };
  let obj = { y: 20 };

  Object.setPrototypeOf(obj, prototypeObj);
  console.log(obj.x); // 10
  ```

### 12. `Object.is`
- **用途**：比较两个值是否严格相等，与 `===` 类似，但处理特殊值（如 `NaN`）时更准确。
- **示例**：
  ```javascript
  console.log(Object.is(NaN, NaN)); // true
  console.log(NaN === NaN); // false
  console.log(Object.is(+0, -0)); // false
  console.log(+0 === -0); // true
  ```

### 13. `Object.freeze`
- **用途**：冻结一个对象，使对象不可修改。
- **示例**：
  ```javascript
  let obj = { a: 1 };

  Object.freeze(obj);
  obj.a = 2; // 无效操作
  console.log(obj.a); // 1
  ```

### 14. `Object.seal`
- **用途**：密封一个对象，使其不可扩展，但可以修改现有属性的值。
- **示例**：
  ```javascript
  let obj = { a: 1 };

  Object.seal(obj);
  obj.a = 2; // 有效操作
  console.log(obj.a); // 2
  delete obj.a; // 无效操作
  console.log(obj.a); // 2
  ```

### 15. `Object.preventExtensions`
- **用途**：防止扩展一个对象，使其不能添加新的属性。
- **示例**：
  ```javascript
  let obj = { a: 1 };

  Object.preventExtensions(obj);
  obj.b = 2; // 无效操作
  console.log(obj.b); // undefined
  ```

### 总结

这些 `Object` 方法是 JavaScript 中非常强大的工具，可以帮助你更有效地处理和操作对象。通过合理使用这些方法，你可以编写更简洁、更高效的代码。
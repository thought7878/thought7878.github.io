`Object.assign()` 是 ES6 引入的一个静态方法，用于将一个或多个源对象的可枚举属性复制到目标对象中。它常用于浅拷贝、合并对象或扩展对象的功能。

---

## **1. 什么是 `Object.assign()`？**

`Object.assign(target, ...sources)` 的作用是：
- 将所有源对象（`sources`）的**可枚举自有属性**复制到目标对象（`target`）中。
- 如果目标对象中已有同名属性，则会被覆盖。
- 返回修改后的目标对象。

### **语法**
```javascript
Object.assign(target, source1, source2, ...);
```

- **`target`**：目标对象，接收属性的对象。
- **`source1, source2, ...`**：一个或多个源对象，提供要复制的属性。

### **返回值**
返回修改后的目标对象。

---

## **2. 基本用法**

### **(1) 合并对象**
```javascript
const target = { a: 1, b: 2 };
const source = { b: 3, c: 4 };

const result = Object.assign(target, source);

console.log(result); // 输出: { a: 1, b: 3, c: 4 }
console.log(target); // 输出: { a: 1, b: 3, c: 4 }（目标对象被修改）
```

在这个例子中：
- `source` 中的 `b` 覆盖了 `target` 中的 `b`。
- `source` 中的 `c` 被添加到 `target` 中。

---

### **(2) 多个源对象**
可以同时合并多个源对象，后面的源对象会覆盖前面的同名属性。

```javascript
const target = { a: 1 };
const source1 = { b: 2 };
const source2 = { c: 3, a: 5 };

const result = Object.assign(target, source1, source2);

console.log(result); // 输出: { a: 5, b: 2, c: 3 }
```

---

### **(3) 创建新对象**
如果不想修改原始目标对象，可以传递一个空对象作为目标对象。

```javascript
const obj1 = { a: 1 };
const obj2 = { b: 2 };

const merged = Object.assign({}, obj1, obj2);

console.log(merged); // 输出: { a: 1, b: 2 }
console.log(obj1);   // 输出: { a: 1 }（未被修改）
```

---

## **3. 深拷贝与浅拷贝**

`Object.assign()` 只能进行**浅拷贝**，即只复制对象的第一层属性。如果属性值是引用类型（如数组或对象），则复制的是引用地址，而不是深拷贝。

### **示例：浅拷贝**
```javascript
const obj1 = { a: { b: 1 } };
const obj2 = Object.assign({}, obj1);

obj2.a.b = 2;

console.log(obj1.a.b); // 输出: 2（obj1 和 obj2 共享同一个引用）
```

### **解决方法：深拷贝**
如果需要深拷贝，可以使用其他方法（如递归拷贝或 `JSON.parse(JSON.stringify())`）。

```javascript
const obj1 = { a: { b: 1 } };
const obj2 = JSON.parse(JSON.stringify(obj1));

obj2.a.b = 2;

console.log(obj1.a.b); // 输出: 1（obj1 和 obj2 不共享引用）
```

---

## **4. 注意事项**

### **(1) 只复制可枚举的自有属性**
`Object.assign()` 只复制源对象的**可枚举自有属性**，不会复制继承的属性或不可枚举的属性。

#### 示例：
```javascript
const obj = Object.create(
  { inherited: true }, // 继承的属性
  {
    own: {
      value: 42,
      enumerable: false, // 不可枚举的自有属性
    },
  }
);

const result = Object.assign({}, obj);

console.log(result); // 输出: {}（没有复制任何属性）
```

---

### **(2) 属性覆盖**
如果源对象中有同名属性，后面的源对象会覆盖前面的。

#### 示例：
```javascript
const target = {};
const source1 = { a: 1 };
const source2 = { a: 2 };

Object.assign(target, source1, source2);

console.log(target); // 输出: { a: 2 }
```

---

### **(3) 不支持 Symbol 类型的属性**
`Object.assign()` 不会复制 `Symbol` 类型的属性。

#### 示例：
```javascript
const source = { [Symbol("key")]: "value" };
const target = {};

Object.assign(target, source);

console.log(target); // 输出: {}
```

---

## **5. 使用场景**

### **(1) 合并配置对象**
在开发中，`Object.assign()` 常用于合并默认配置和用户提供的配置。

#### 示例：
```javascript
const defaultConfig = { theme: "light", timeout: 5000 };
const userConfig = { theme: "dark" };

const finalConfig = Object.assign({}, defaultConfig, userConfig);

console.log(finalConfig); // 输出: { theme: "dark", timeout: 5000 }
```

---

### **(2) 浅拷贝对象**
如果只需要浅拷贝对象，可以直接使用 `Object.assign()`。

#### 示例：
```javascript
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original);

console.log(copy); // 输出: { a: 1, b: 2 }
```

---

### **(3) 扩展对象功能**
可以通过 `Object.assign()` 动态扩展对象的功能。

#### 示例：
```javascript
const utils = {
  log(value) {
    console.log(value);
  },
};

Object.assign(utils, {
  warn(value) {
    console.warn(value);
  },
});

utils.log("This is a log message");   // 输出: This is a log message
utils.warn("This is a warning");     // 输出: This is a warning
```

---

## **6. 总结**

`Object.assign()` 是 JavaScript 中一个简单而强大的工具，具有以下特点：
1. **合并对象**：可以将多个源对象的属性合并到目标对象中。
2. **浅拷贝**：只能复制对象的第一层属性，无法实现深拷贝。
3. **灵活性高**：适用于配置合并、对象扩展等场景。

需要注意的是，`Object.assign()` 只能处理可枚举的自有属性，并且对引用类型的属性是浅拷贝。对于更复杂的需求（如深拷贝），需要使用其他方法或库（如 Lodash）。
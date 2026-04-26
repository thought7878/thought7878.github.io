
`Map` 是 ES6（ES2015）引入的一种**键值对集合**数据结构。它在语法和语义上弥补了传统 `Object` 作为字典使用时的诸多缺陷，是现代 JavaScript 开发中处理映射关系的首选方案之一。

---

## 一、什么是 Map？
`Map` 是一个内置对象，用于保存键值对，并且**严格记住键的原始插入顺序**。任何值（对象或原始值）都可以作为键或值。

```js
const m = new Map();
m.set('name', 'Alice');
m.set(1, 'one');
m.set(true, 'bool');
console.log(m.size); // 3
```

---

## 二、Map 与 Object 的核心区别

| 特性           | `Object`                         | `Map`                   |
| ------------ | -------------------------------- | ----------------------- |
| **键的类型**     | 仅限 `String` 或 `Symbol`           | **任意类型**（对象、函数、NaN等）    |
| **插入顺序**     | ES6 后部分保证，但规则复杂                  | **严格保持插入顺序**            |
| **获取大小**     | 需 `Object.keys(obj).length`      | 原生 `size` 属性            |
| **迭代支持**     | 需借助 `Object.keys/values/entries` | **原生可迭代**，支持 `for...of` |
| **原型链干扰**    | 有默认原型，可能键名冲突                     | 无原型，纯净的键值对容器            |
| **频繁增删性能**   | 引擎优化较好，但结构变化时可能降级                | **专为频繁增删优化**，平均 O(1)    |
| **JSON 序列化** | 原生支持                             | 不支持，需手动转换               |

> 💡 **经验法则**：  
> - 用 `Object`：*数据结构固定、需要 JSON 序列化、键名为字符串/Symbol 的配置项*。  
> - 用 `Map`：*键类型多样、频繁增删、需要保持顺序、键值对数量动态变化*。

---

## 三、创建与初始化

```js
// 1. 空 Map
const m1 = new Map();

// 2. 传入可迭代对象（通常是二维数组）
const m2 = new Map([
  ['a', 1],
  ['b', 2],
  [true, 'yes']
]);

// 3. 从 Object 转换
const obj = { x: 10, y: 20 };
const m3 = new Map(Object.entries(obj));

// 4. 链式初始化（利用 set 返回 this）
const m4 = new Map()
  .set('foo', 1)
  .set('bar', 2)
  .set('baz', 3);
```

---

## 四、核心 API 详解

| 方法/属性       | 说明                                      | 返回值              |
|----------------|-------------------------------------------|---------------------|
| `set(key, val)` | 添加或更新键值对                           | `Map` 实例（支持链式）|
| `get(key)`      | 获取键对应的值，不存在返回 `undefined`     | 任意值              |
| `has(key)`      | 检查键是否存在                             | `boolean`           |
| `delete(key)`   | 删除指定键值对                             | `boolean`（是否删除成功）|
| `clear()`       | 清空所有键值对                             | `undefined`         |
| `size`          | 只读属性，返回键值对数量                   | `number`            |

```js
const m = new Map();
m.set('a', 1).set('b', 2);
console.log(m.get('a'));    // 1
console.log(m.has('b'));    // true
console.log(m.delete('b')); // true
console.log(m.size);        // 1
m.clear();
console.log(m.size);        // 0
```

---

## 五、遍历方式

`Map` 原生实现 `Iterable` 协议，支持多种遍历方式：

```js
const m = new Map([['x', 10], ['y', 20], ['z', 30]]);

// 1. for...of（默认遍历 entries）
for (const [key, value] of m) {
  console.log(key, value);
}

// 2. 专用迭代器
for (const key of m.keys())   console.log(key);
for (const val of m.values()) console.log(val);
for (const entry of m.entries()) console.log(entry); // ['x', 10] ...

// 3. forEach（⚠️ 注意参数顺序是 value, key, map）
m.forEach((value, key, mapRef) => {
  console.log(`${key} => ${value}`);
});

// 4. 展开运算符 & 数组转换
const arr = [...m]; // [['x',10], ['y',20], ['z',30]]
const keys = [...m.keys()];
```

---

## 六、键的类型与特殊行为

### 1. 任意类型均可作键
```js
const objKey = { id: 1 };
const fnKey = function() {};
const m = new Map();
m.set(objKey, 'object value');
m.set(fnKey, 'function value');
m.set(NaN, 'not a number');
m.set(undefined, 'undef');
```

### 2. 引用类型按**引用地址**比较
```js
const m = new Map();
m.set({}, 'a');
m.set({}, 'b');
console.log(m.size); // 2（两个不同对象，即使内容相同）
```

### 3. 特殊值的相等性算法：`SameValueZero`
- `NaN` 被视为相等：`m.set(NaN, 1); m.get(NaN) // 1`
- `+0` 与 `-0` 被视为相等
- 其他值使用严格相等 \`\=\=\=\` 判断

---

## 七、**性能与适用场景**

| 场景                     | 推荐方案     | 原因                   |
| ---------------------- | -------- | -------------------- |
| 键为对象/函数/非字符串           | `Map`    | `Object` 会隐式转字符串导致冲突 |
| 频繁插入/删除/查找             | `Map`    | 内部哈希表优化，避免对象隐藏类失效    |
| 需要严格保持插入顺序             | `Map`    | 原生保证，无需额外逻辑          |
| 数据需直接 `JSON.stringify` | `Object` | `Map` 序列化结果为 `{}`    |
| 静态配置/结构固定的数据           | `Object` | 语法更简洁，引擎对字面量优化极好     |

> 📊 **性能提示**：V8 引擎对普通对象做了极致优化（隐藏类、内联缓存），在**键固定且为字符串**的场景下，`Object` 可能比 `Map` 更快。但在动态键、非字符串键、频繁增删场景下，`Map` 表现更稳定。

---

## 八、Map 与 WeakMap 对比

| 特性            | `Map`                     | `WeakMap`                     |
|-----------------|---------------------------|-------------------------------|
| 键类型          | 任意类型                  | **仅限对象**（不含 null）      |
| 引用强度        | 强引用（阻止 GC）         | **弱引用**（键被回收则条目自动清除）|
| 可遍历性        | ✅ 支持                    | ❌ 不可遍历                    |
| `size` / `clear`| ✅ 支持                    | ❌ 不支持                      |
| 典型用途        | 通用字典、缓存、状态管理   | DOM 节点关联数据、私有字段模拟、防内存泄漏缓存 |

```js
const wm = new WeakMap();
let obj = { name: 'temp' };
wm.set(obj, 'metadata');
obj = null; // 垃圾回收后，WeakMap 中的对应条目自动消失
```

---

## 九、注意事项与最佳实践

1. **`forEach` 参数顺序易错**  
   `Map.prototype.forEach((value, key, map) => {})`，与数组的 `(item, index)` 不同。

2. **JSON 序列化需转换**  
   ```js
   const m = new Map([['a', 1]]);
   JSON.stringify(m); // "{}"
   // 正确做法：
   JSON.stringify(Object.fromEntries(m)); // '{"a":1}'
   // 或转数组：JSON.stringify([...m])
   ```

3. **引用类型键可能导致内存泄漏**  
   若用大对象作键且长期不删除，`Map` 会强引用阻止 GC。考虑改用 `WeakMap` 或定期 `delete`。

4. **不要混用 `Object` 语法操作 `Map`**  
   `map['key'] = 1` 会在 Map 实例上添加普通属性，**不会**进入 Map 内部数据结构，`size` 不变且无法用 `get()` 读取。

5. **兼容性**  
   所有现代浏览器与 Node.js 均原生支持。老旧环境（IE11）需引入 `core-js` 或 `es6-shim` polyfill。

---

## 十、总结

`Map` 是 JavaScript 中为**动态键值对管理**量身定制的现代数据结构。它解决了 `Object` 作为字典使用时的键类型限制、顺序不确定性、原型污染和性能瓶颈等问题。掌握 `Map` 的 API、键比较规则、遍历方式以及与 `Object`/`WeakMap` 的边界，能让你在状态管理、缓存设计、算法实现等场景中写出更安全、高效、可维护的代码。

如有具体使用场景或性能疑问，可提供代码片段，我可针对性分析优化方案。
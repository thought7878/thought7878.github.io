`WeakMap` 是 JavaScript 中的一种*特殊 Map 类型，它**只接受对象作为键**，并且这些键是弱引用的*。这意味着 `WeakMap` 的键**不会阻止垃圾回收机制**，***如果一个对象仅被 `WeakMap` 引用（即没有其他强引用），那么这个对象会被垃圾回收器回收***。这使得 `WeakMap` 特别适合用于**存储那些不应该影响对象生命周期的数据**。

### 主要特点

1. **键必须是对象**：`WeakMap` 的键只能是对象，不能是原始值（如字符串、数字等）。
2. **弱引用**：`WeakMap` 对其键持有弱引用，这意味着它们不会阻止垃圾回收。
3. **不可枚举**：与 `Map` 不同，`WeakMap` 没有 `keys()`、`values()`、`entries()` 或 `[Symbol.iterator]` 方法，因此你*不能直接遍历它的内容*。
4. **更小的 API**：相比 `Map`，`WeakMap` 提供了一个更为有限的方法集合。

### 创建 `WeakMap`

使用 `new WeakMap()` 来创建一个新的 `WeakMap` 实例：

```javascript
const wm = new WeakMap();
```

你可以通过传入一个可迭代对象来初始化 `WeakMap`，该对象应包含键值对数组：

```javascript
const obj = { id: 1 };
const wm = new WeakMap([[obj, 'value']]);
```

### 基本操作

#### 设置键值对

使用 `set(key, value)` 方法添加或更新键值对：

```javascript
const obj = {};
wm.set(obj, 'some data');
```

#### 获取键对应的值

使用 `get(key)` 方法获取指定键的值：

```javascript
console.log(wm.get(obj)); // 输出: some data
```

#### 检查键是否存在

使用 `has(key)` 方法检查某个键是否存在于 `WeakMap` 中：

```javascript
console.log(wm.has(obj)); // true
```

#### 删除键值对

使用 `delete(key)` 方法删除指定键的键值对：

```javascript
wm.delete(obj);
console.log(wm.has(obj)); // false
```

### 使用场景

`WeakMap` 适用于以下几种情况：

- **私有数据**：可以用来*为对象*附加一些不希望公开的数据，因为只有拥有对象引用的一方才能访问到 `WeakMap` 中的数据。
- **临时数据**：当需要*为对象*关联一些临时数据时，使用 `WeakMap` 可以确保这些数据不会阻止对象被垃圾回收。
- **DOM 节点关联数据**：在处理 DOM 元素时，可以使用 `WeakMap` 将数据关联到特定的 DOM 节点上，而不会影响节点的垃圾回收。

### 示例

下面是一个简单的例子，展示了如何使用 `WeakMap` 来*存储私有数据：*

```javascript
function MyClass() {
    const privateData = new WeakMap();

    class MyPublicClass {
        constructor(data) {
            privateData.set(this, data);
        }

        getPrivateData() {
            return privateData.get(this);
        }
    }

    return MyPublicClass;
}

const MyClassImpl = MyClass();
const instance = new MyClassImpl({ secret: 'my secret' });
console.log(instance.getPrivateData()); // 输出: { secret: 'my secret' }
```

在这个例子中，`privateData` 是一个 `WeakMap`，它存储了每个 `MyPublicClass` 实例的私有数据。由于 `WeakMap` 的键是对象本身，所以只有 `MyPublicClass` 的实例方法能够访问这些私有数据。

### 总结

`WeakMap` 是一种非常有用的数据结构，特别是在你需要确保某些数据不会意外地阻止对象被垃圾回收的情况下。它提供的功能虽然比 `Map` 简单，但在适当的场景下可以带来很大的便利和性能优势。

## 相关资料
[[13-q13-weakmap]]

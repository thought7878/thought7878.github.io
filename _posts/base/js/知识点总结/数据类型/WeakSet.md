`WeakSet` 是 JavaScript 中的一种集合数据结构，它类似于 `Set`，但具有一些独特的行为和限制。`WeakSet` 只能包含对象（不能包含原始值如数字、字符串等），并且这些对象是弱引用的，这意味着它们不会阻止垃圾回收机制。这使得 `WeakSet` 特别适合用于存储那些不应该影响对象生命周期的数据。

### 主要特点

1. **键必须是对象**：`WeakSet` 的成员只能是对象，不能是原始值。
2. **弱引用**：`WeakSet` 对其成员持有**弱引用**，**因此如果一个对象仅被 `WeakSet` 引用（即没有其他强引用），那么这个对象可能会被垃圾回收器回收**。
3. **不可迭代**：与 `Set` 不同，`WeakSet` 没有提供遍历其成员的方法，如 `keys()`、`values()` 或 `[Symbol.iterator]`。
4. **有限的 API**：相比 `Set`，`WeakSet` 提供了一个更为有限的方法集合。

### 创建 `WeakSet`

使用 `new WeakSet()` 来创建一个新的 `WeakSet` 实例：

```javascript
const ws = new WeakSet();
```

你可以通过传入一个可迭代对象来初始化 `WeakSet`，该对象应包含对象元素：

```javascript
const obj1 = {};
const obj2 = {};
const ws = new WeakSet([obj1, obj2]);
```

### 基本操作

#### 添加成员

使用 `add(value)` 方法添加对象到 `WeakSet` 中：

```javascript
const obj = {};
ws.add(obj);
```

#### 检查成员是否存在

使用 `has(value)` 方法检查某个对象是否存在于 `WeakSet` 中：

```javascript
console.log(ws.has(obj)); // true
```

#### 删除成员

使用 `delete(value)` 方法从 `WeakSet` 中删除指定的对象：

```javascript
ws.delete(obj);
console.log(ws.has(obj)); // false
```

### 使用场景

`WeakSet` 适用于以下几种情况：

- **对象标记**：可以用来标记某些对象，而不会阻止这些对象被垃圾回收。例如，你可以在应用程序中使用 `WeakSet` 来跟踪哪些对象已经被处理过。
- **私有数据关联**：可以用来为对象附加一些不希望公开的数据，因为只有拥有对象引用的一方才能访问到 `WeakSet` 中的数据。
- **DOM 节点管理**：在处理 DOM 元素时，可以使用 `WeakSet` 将信息关联到特定的 DOM 节点上，而不影响节点的垃圾回收。

### 示例

下面是一个简单的例子，展示了如何使用 `WeakSet` 来跟踪已经处理过的对象：

```javascript
const processedObjects = new WeakSet();

function processObject(obj) {
    if (processedObjects.has(obj)) {
        console.log('Object already processed.');
        return;
    }

    // 处理对象的逻辑...
    console.log('Processing object.');

    // 标记对象为已处理
    processedObjects.add(obj);
}

const obj1 = { id: 1 };
processObject(obj1); // 输出: Processing object.
processObject(obj1); // 输出: Object already processed.
```

在这个例子中，`processedObjects` 是一个 `WeakSet`，它用于跟踪哪些对象已经被处理过。由于 `WeakSet` 的成员是弱引用的，所以一旦没有任何其他地方引用这些对象，它们就可以被垃圾回收。

### 总结

`WeakSet` 是一种非常有用的数据结构，特别是在你需要确保某些数据不会意外地阻止对象被垃圾回收的情况下。它提供的功能虽然比 `Set` 简单，但在适当的场景下可以带来很大的便利和性能优势。`WeakSet` 的主要应用场景包括对象标记、私有数据关联以及 DOM 节点管理等。
JavaScript 的数组（Array）是一种用于存储有序数据集合的数据结构，支持动态扩展和多种操作方法。以下是关于 JavaScript 数组的详细总结，涵盖定义、特性、常用方法以及使用技巧。

---

## 一、数组的基本概念
### 1. 定义
- 数组是**有序的元素集合**，可以包含任意类型的数据（如数字、字符串、对象等）。
- 数组的*长度是动态的*，可以*根据需要自动扩展或收缩*。

### 2. 创建数组
#### 方法 1：字面量方式
```javascript
const arr = [1, 2, 3]; // 创建一个包含 1, 2, 3 的数组
```

#### 方法 2：构造函数方式
```javascript
const arr = new Array(3); // 创建一个长度为 3 的空数组
const arr2 = new Array(1, 2, 3); // 创建一个包含 1, 2, 3 的数组
```
> **注意**：`new Array(3)` 和 `[3]` 的区别：
> - `new Array(3)` 创建一个长度为 3 的空数组。
> - `[3]` 创建一个包含单个元素 `3` 的数组。

---

## 二、数组的特性
1. **索引访问**  
   - 数组通过索引访问元素，索引从 `0` 开始。
   ```javascript
   const arr = ['a', 'b', 'c'];
   console.log(arr[0]); // 输出 'a'
   ```

2. **动态长度**  
   - 数组的长度会随着元素的增减而变化。
   ```javascript
   const arr = [];
   arr.push(1); // 添加元素
   console.log(arr.length); // 输出 1
   ```

3. **稀疏数组**  
   - 数组可以是稀疏的（即某些索引没有值）。
   ```javascript
   const sparseArr = [];
   sparseArr[10] = 'value';
   console.log(sparseArr); // [ <10 empty items>, 'value' ]
   ```

4. **多维数组**  
   - 数组可以嵌套，形成多维数组。
   ```javascript
   const matrix = [
       [1, 2, 3],
       [4, 5, 6],
       [7, 8, 9]
   ];
   console.log(matrix[0][1]); // 输出 2
   ```

---

## 三、数组的常用方法
### 1. 增加/删除元素
#### 添加元素
- `push(...items)`：向数组*末尾添加元素*，返回新长度。
  ```javascript
  const arr = [1, 2];
  arr.push(3); // [1, 2, 3]
  ```
- `unshift(...items)`：向数组*开头添加元素*，返回新长度。“把（已移动的东西）移回原处”
  ```javascript
  const arr = [2, 3];
  arr.unshift(1); // [1, 2, 3]
  ```

#### 删除元素
- `pop()`：*移除数组末尾的元素*，返回被移除的元素。“取出；拔出；分开”
  ```javascript
  const arr = [1, 2, 3];
  arr.pop(); // 返回 3，数组变为 [1, 2]
  ```
- `shift()`：*移除数组开头的元素*，返回被移除的元素。“移动；转移”
  ```javascript
  const arr = [1, 2, 3];
  arr.shift(); // 返回 1，数组变为 [2, 3]
  ```

### 2. 修改数组
#### 替换部分元素
- `splice(start, deleteCount, ...items)`：从 `start` 索引开始，*删除 `deleteCount` 个元素，并插入新的元素*。“拼接；接合”
  ```javascript
  const arr = [1, 2, 3, 4];
  arr.splice(1, 2, 'a', 'b'); // [1, 'a', 'b', 4]
  ```

#### 复制或填充
- `slice(start, end)`：*提取子数组*（不修改原数组），返回从 `start` 到 `end`（不包括 `end`）的子数组。“把… 切成薄片”
  ```javascript
  const arr = [1, 2, 3, 4];
  const subArr = arr.slice(1, 3); // [2, 3]
  ```
- `fill(value, start, end)`：用 `value` 填充数组的指定范围。
  ```javascript
  const arr = [1, 2, 3, 4];
  arr.fill(0, 1, 3); // [1, 0, 0, 4]
  ```

### 3. 查找与过滤
#### 查找元素
- `indexOf(item)`：返回*第一个匹配项的索引*，未找到返回 `-1`。
  ```javascript
  const arr = [1, 2, 3];
  console.log(arr.indexOf(2)); // 1
  ```
- `find(callback)`：返回第一个满足条件的元素。
  ```javascript
  const arr = [1, 2, 3];
  const result = arr.find(x => x > 1); // 2
  ```

#### 过滤元素
- `filter(callback)`：返回所有满足条件的元素组成的新数组。
  ```javascript
  const arr = [1, 2, 3];
  const filtered = arr.filter(x => x > 1); // [2, 3]
  ```

### 4. 遍历数组
#### 遍历方法
- `forEach(callback)`：对每个元素执行回调函数。
  ```javascript
  const arr = [1, 2, 3];
  arr.forEach(x => console.log(x));
  ```
- `map(callback)`：返回每个元素经过回调处理后的新数组。
  ```javascript
  const arr = [1, 2, 3];
  const doubled = arr.map(x => x * 2); // [2, 4, 6]
  ```

#### 检查条件
- `every(callback)`：检查是否所有元素都满足条件。
  ```javascript
  const arr = [1, 2, 3];
  const allPositive = arr.every(x => x > 0); // true
  ```
- `some(callback)`：检查是否有至少一个元素满足条件。
  ```javascript
  const arr = [1, 2, 3];
  const hasEven = arr.some(x => x % 2 === 0); // true
  ```

### 5. 排序与反转
- `sort([compareFunction])`：对数组进行排序。
  ```javascript
  const arr = [3, 1, 2];
  arr.sort((a, b) => a - b); // [1, 2, 3]
  ```
- `reverse()`：反转数组。
  ```javascript
  const arr = [1, 2, 3];
  arr.reverse(); // [3, 2, 1]
  ```

### 6. 合并与分割
- `concat(...arrays)`：合并多个数组。
  ```javascript
  const arr1 = [1, 2];
  const arr2 = [3, 4];
  const merged = arr1.concat(arr2); // [1, 2, 3, 4]
  ```
- `join(separator)`：将数组元素连接成字符串。
  ```javascript
  const arr = [1, 2, 3];
  const str = arr.join('-'); // "1-2-3"
  ```

---

## 四、数组的高级特性
### 1. 可变性
- 数组是引用类型，修改原数组会影响其他引用。
  ```javascript
  const arr1 = [1, 2, 3];
  const arr2 = arr1;
  arr2.push(4);
  console.log(arr1); // [1, 2, 3, 4]
  ```

### 2. ES6 新增方法
- `includes(item)`：检查数组是否包含某个值。
  ```javascript
  const arr = [1, 2, 3];
  console.log(arr.includes(2)); // true
  ```
- `flat(depth)`：将多维数组展平到指定深度。
  ```javascript
  const arr = [1, [2, [3]]];
  console.log(arr.flat(2)); // [1, 2, 3]
  ```
- `flatMap(callback)`：先映射再展平。
  ```javascript
  const arr = [1, 2, 3];
  const result = arr.flatMap(x => [x, x * 2]); // [1, 2, 2, 4, 3, 6]
  ```

---

## 五、数组的性能优化
1. **避免频繁修改数组长度**  
   动态调整数组长度会导致性能开销，尽量预分配固定大小的数组。
   ```javascript
   const arr = new Array(1000); // 预分配长度
   ```

2. **使用高效的方法**  
   - `for` 循环通常比 `forEach` 更快。
   - 使用 `Map/Set` 替代数组来处理唯一值查找。

3. **减少内存占用**  
   - 对于稀疏数组，考虑使用对象或其他数据结构替代。

---

## 六、总结
JavaScript 的数组是一个强大且灵活的数据结构，适用于各种场景。掌握其基本操作、常用方法和高级特性，能够帮助你更高效地处理数据。以下是一些学习建议：
1. **多实践**：通过刷题（如 LeetCode）熟悉数组的操作。
2. **理解原理**：了解数组的底层实现（如连续内存分配）。
3. **结合实际应用**：在项目中尝试使用数组解决实际问题。


## 相关资料
- [[2、快速上手——从0到1掌握算法面试需要的数据结构（一）]]
- 
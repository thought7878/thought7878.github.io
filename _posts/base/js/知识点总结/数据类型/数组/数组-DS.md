### JavaScript 数组总结

#### **1. 基本概念**
- **定义**：数组是有序的元素集合，用于存储多个值。
- **创建方式**：
  ```javascript
  let arr1 = [1, 2, 3];               // 字面量
  let arr2 = new Array(3);            // 创建长度为3的空数组
  let arr3 = Array.of(1, 2, 3);       // ES6：解决 new Array(3) 的歧义
  ```
- **索引**：从 `0` 开始，最大索引为 `arr.length - 1`，越界访问返回 `undefined`。

---

#### **2. 核心方法**
| **方法**                   | **功能**                     | **是否修改原数组** | **示例**                                         |
| ------------------------ | -------------------------- | ----------- | ---------------------------------------------- |
| **增删操作**                 |                            |             |                                                |
| `push()`                 | 末尾添加元素，返回新长度               | ✔️          | `arr.push(4)` → `[1,2,3,4]`                    |
| `pop()`                  | 删除并返回末尾元素                  | ✔️          | `arr.pop()` → `3` (原数组变 `[1,2]`)               |
| `unshift()`              | 开头添加元素，返回新长度               | ✔️          | `arr.unshift(0)` → `[0,1,2,3]`                 |
| `shift()`                | 删除并返回开头元素                  | ✔️          | `arr.shift()` → `0` (原数组变 `[1,2,3]`)           |
| `splice(start, n, ...)`  | 从 `start` 删除 `n` 个元素，插入新元素 | ✔️          | `arr.splice(1, 2, 'a')` → 替换索引1-2为 `'a'`       |
| **查询与转换**                |                            |             |                                                |
| `slice(start, end)`      | 提取子数组（不修改原数组）              | ❌           | `arr.slice(1,3)` → `[2,3]`                     |
| `concat(arr2)`           | 合并数组                       | ❌           | `arr.concat([4,5])` → `[1,2,3,4,5]`            |
| `join(separator)`        | 将数组转为字符串，`separator` 为分隔符  | ❌           | `arr.join('-')` → `"1-2-3"`                    |
| **遍历与迭代**                |                            |             |                                                |
| `forEach(callback)`      | 遍历数组，无返回值                  | ❌           | `arr.forEach(v => console.log(v))`             |
| `map(callback)`          | 返回新数组，元素为回调返回值             | ❌           | `arr.map(v => v * 2)` → `[2,4,6]`              |
| `filter(callback)`       | 返回满足条件的新数组                 | ❌           | `arr.filter(v => v > 1)` → `[2,3]`             |
| `reduce(callback, init)` | 累积计算结果（如求和、求最大值）           | ❌           | `arr.reduce((sum, v) => sum + v, 0)` → `6`     |
| **查找与判断**                |                            |             |                                                |
| `indexOf(value)`         | 返回第一个匹配元素的索引，无则返回 `-1`     | ❌           | `arr.indexOf(2)` → `1`                         |
| `includes(value)`        | 判断是否包含某元素                  | ❌           | `arr.includes(2)` → `true`                     |
| `find(callback)`         | 返回第一个满足条件的元素               | ❌           | `arr.find(v => v > 1)` → `2`                   |
| `some(callback)`         | 判断是否至少有一个元素满足条件            | ❌           | `arr.some(v => v > 2)` → `true`                |
| **排序与调整**                |                            |             |                                                |
| `sort([compareFunc])`    | 排序（默认按字符串Unicode排序）        | ✔️          | `arr.sort((a,b) => a - b)` → 数字升序              |
| `reverse()`              | 反转数组                       | ✔️          | `arr.reverse()` → `[3,2,1]`                    |
| **ES6+ 新增方法**            |                            |             |                                                |
| `flat(depth)`            | 扁平化嵌套数组                    | ❌           | `[[1,2],3].flat()` → `[1,2,3]`                 |
| `flatMap(callback)`      | 先 `map` 后 `flat`           | ❌           | `arr.flatMap(v => [v, v*2])` → `[1,2,2,4,3,6]` |
| `Array.from(obj)`        | 将类数组或可迭代对象转为数组             | ❌           | `Array.from('123')` → `['1','2','3']`          |

---

#### **3. 关键特性**
- **动态大小**：数组长度自动扩展/收缩。
  ```javascript
  let arr = [1, 2];
  arr.length = 5;    // 扩展为 [1, 2, empty × 3]
  arr.length = 1;    // 截断为 [1]
  ```
- **稀疏数组**：允许存在空位（empty slots），如 `[1, ,3]`，访问空位返回 `undefined`。
- **类型检测**：
  ```javascript
  typeof [1,2];      // "object"
  Array.isArray([1,2]); // true
  ```

---

#### **4. 性能注意**
- **高效操作**：`push/pop`（末尾操作）快于 `shift/unshift`（开头操作）。
- **深拷贝方法**：
  ```javascript
  // 浅拷贝
  let copy1 = arr.slice();
  let copy2 = [...arr];
  
  // 深拷贝（简单场景）
  let deepCopy = JSON.parse(JSON.stringify(arr)); // 无法处理函数、循环引用
  ```

---

#### **5. 多维数组**
- **定义与访问**：
  ```javascript
  let matrix = [[1, 2], [3, 4]];
  matrix[0][1];      // 2
  ```
- **操作**：结合 `map` 和 `flat` 处理嵌套结构。

---

#### **一句话总结**
JavaScript 数组是灵活的动态数据结构，通过丰富的原生方法（增删、遍历、转换）实现高效操作，需注意方法的副作用（是否修改原数组）及性能特性。
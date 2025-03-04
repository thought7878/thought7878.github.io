### 什么是时间复杂度？

`时间复杂度（Time Complexity）`是***衡量算法运行时间**随输入数据规模增长而变化*的指标。*它描述了算法执行所需的时间与输入数据规模之间的关系*，通常用大 O 表示法（Big-O Notation）来表示。

**时间复杂度的主要目的**是帮助我们*评估算法的效率*，尤其是在处理大规模数据时的表现。通过分析时间复杂度，我们可以选择更高效的算法或优化现有算法。

---

### 大 O 表示法

`大 O 表示法`是一种*数学符号*，用于描述函数的增长趋势。在算法分析中，它表示算法的最坏情况下的时间复杂度，*忽略常数项和低阶项，只关注主导项*（即随着输入规模增长影响最大的部分）。

#### 常见的时间复杂度

| 时间复杂度          | 描述                       | 示例         |
| -------------- | ------------------------ | ---------- |
| **O(1)**       | *常数*时间复杂度，*与输入规模无关*      | 访问数组中的某个元素 |
| **O(log n)**   | [[对数]]时间复杂度，*每次操作减少问题规模* | 二分查找       |
| **O(n)**       | 线性时间复杂度，操作次数与输入规模*成正比*   | 遍历数组中的所有元素 |
| **O(n log n)** | 线性对数时间复杂度                | 快速排序、归并排序  |
| **O(n²)**      | 平方时间复杂度，操作次数与输入规模的平方成正比  | 双重循环遍历数组   |
| **O(2ⁿ)**      | 指数时间复杂度，操作次数随输入规模指数增长    | 暴力破解某些组合问题 |
| **O(n!)**      | 阶乘时间复杂度，操作次数随输入规模阶乘增长    | 全排列问题      |

---

### 如何计算时间复杂度？

计算时间复杂度的核心思想是分析算法中每一步操作的执行次数，并找出其与输入规模 `n` 的关系。

#### 步骤：
1. **确定输入规模**：明确输入数据的规模（如数组长度、字符串长度等）。
2. **分析基本操作**：找出算法中最耗时的操作（如循环、递归等）。
3. **计算操作次数**：统计这些操作的执行次数，并用数学表达式表示。
4. **简化表达式**：去掉常数项和低阶项，只保留主导项。

---

### 时间复杂度的分类

#### 1. **最好时间复杂度（Best Case）**
- 描述算法在最优情况下的性能。
- 例如，在一个已排序的数组中进行线性搜索，如果目标值是第一个元素，则只需一次比较。

#### 2. **最坏时间复杂度（Worst Case）**
- 描述算法在最差情况下的性能。
- 例如，在一个未排序的数组中进行线性搜索，目标值可能是最后一个元素或不存在。

#### 3. **平均时间复杂度（Average Case）**
- 描述算法在随机输入下的平均性能。
- 例如，在一个随机分布的数组中进行线性搜索，平均需要检查一半的元素。

通常，我们更关注**最坏时间复杂度**，因为它代表了算法在最不利情况下的表现。

---

### 常见算法的时间复杂度分析

#### 1. **常数时间复杂度 O(1)**
```javascript
function constantTimeExample(arr) {
    return arr[0]; // 直接访问数组的第一个元素
}
```
- 无论数组大小如何，访问第一个元素的操作始终只需要一次。

---

#### 2. **线性时间复杂度 O(n)**
```javascript
function linearTimeExample(arr) {
    for (let i = 0; i < arr.length; i++) {
        console.log(arr[i]); // 遍历数组中的每个元素
    }
}
```
- 循环次数与数组长度 `n` 成正比，因此时间复杂度为 O(n)。

---

#### 3. **平方时间复杂度 O(n²)**
```javascript
function quadraticTimeExample(arr) {
    for (let i = 0; i < arr.length; i++) {
        for (let j = 0; j < arr.length; j++) {
            console.log(arr[i], arr[j]); // 双重循环遍历数组
        }
    }
}
```
- 外层循环运行 `n` 次，内层循环也运行 `n` 次，总操作次数为 `n * n`，因此时间复杂度为 O(n²)。

---

#### 4. **对数时间复杂度 O(log n)**
```javascript
function binarySearch(arr, target) {
    let left = 0;
    let right = arr.length - 1;

    while (left <= right) {
        const mid = Math.floor((left + right) / 2);
        if (arr[mid] === target) {
            return mid; // 找到目标值
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1; // 未找到目标值
}
```
- 每次迭代将搜索范围缩小一半，因此时间复杂度为 O(log n)。

---

#### 5. **线性对数时间复杂度 O(n log n)**
```javascript
function mergeSort(arr) {
    if (arr.length <= 1) return arr;

    const mid = Math.floor(arr.length / 2);
    const left = mergeSort(arr.slice(0, mid));
    const right = mergeSort(arr.slice(mid));

    return merge(left, right); // 合并两个有序数组
}

function merge(left, right) {
    const result = [];
    let i = 0, j = 0;

    while (i < left.length && j < right.length) {
        if (left[i] < right[j]) {
            result.push(left[i]);
            i++;
        } else {
            result.push(right[j]);
            j++;
        }
    }

    return result.concat(left.slice(i)).concat(right.slice(j));
}
```
- 归并排序将数组分成两半（log n 次），然后合并数组（n 次），因此时间复杂度为 O(n log n)。

---

#### 6. **指数时间复杂度 O(2ⁿ)**
```javascript
function fibonacciRecursive(n) {
    if (n <= 1) return n;
    return fibonacciRecursive(n - 1) + fibonacciRecursive(n - 2);
}
```
- 每次递归调用都会产生两个新的分支，形成一棵深度为 `n` 的递归树，因此时间复杂度为 O(2ⁿ)。

---

#### 7. **阶乘时间复杂度 O(n!)**
```javascript
function factorialTimeExample(n) {
    if (n === 0) return 1;
    let result = 1;
    for (let i = 1; i <= n; i++) {
        result *= i;
    }
    return result;
}
```
- 阶乘复杂度通常出现在全排列问题中，例如生成所有可能的排列组合。

---

### 总结

时间复杂度是衡量算法效率的重要工具。通过分析时间复杂度，我们可以：

1. **选择更高效的算法**：优先选择时间复杂度较低的算法。
2. **优化现有代码**：识别瓶颈并改进性能。
3. **预测算法性能**：了解算法在不同输入规模下的表现。

掌握时间复杂度的基本概念和常见算法的时间复杂度分析方法，对于编写高效代码和解决实际问题至关重要。
栈（Stack）是一种重要的**线性数据结构**，遵循**后进先出（LIFO, Last In First Out）** 的原则。它在计算机科学中有着广泛的应用，例如函数调用栈、表达式求值、括号匹配等。

以下是关于栈的详细总结，包括定义、特性、操作方法、实现方式以及应用场景。

---

## 一、栈的基本概念

### 1. 定义
- 栈是一种只能在一端进行插入或删除操作的线性表。
- 栈的操作端称为**栈顶（Top）**，另一端称为**栈底（Bottom）**。
- 新元素总是被添加到栈顶，移除时也总是从栈顶开始。

**示例：**
```plaintext
入栈顺序：A -> B -> C
栈的状态：[A, B, C] （C 在栈顶）
出栈顺序：C -> B -> A
```

---

### 2. 特性
1. **后进先出（LIFO）**  
   - 最后进入栈的元素最先被移除。
   - *类似于叠盘子*：最后放上去的盘子最先被拿走。

2. **受限操作**  
   - 只能在栈顶进行插入（`push`）和删除（`pop`）操作。
   - 无法直接访问栈中间的元素。

3. **动态大小**  
   - 栈的大小通常是动态的，可以根据需要扩展或收缩。

---

## 二、栈的基本操作

栈的核心操作包括以下几种：

### 1. 入栈（Push）
- 将一个新元素压入栈顶。
- 时间复杂度：`O(1)`。

**示例：**
```javascript
stack.push('A'); // 栈变为 [A]
stack.push('B'); // 栈变为 [A, B]
```

---

### 2. 出栈（Pop）
- 移除并返回栈顶元素。
- 如果栈为空，则抛出错误或返回特殊值（如 `undefined`）。
- 时间复杂度：`O(1)`。

**示例：**
```javascript
stack.pop(); // 返回 'B'，栈变为 [A]
stack.pop(); // 返回 'A'，栈变为空 []
```

---

### 3. 查看栈顶元素（Peek/Top）
- 返回栈顶元素但不移除它。
- 时间复杂度：`O(1)`。

**示例：**
```javascript
stack.peek(); // 返回栈顶元素 'A'
```

---

### 4. 判断栈是否为空（isEmpty）
- 检查栈是否为空。
- 时间复杂度：`O(1)`。

**示例：**
```javascript
stack.isEmpty(); // 如果栈为空，返回 true；否则返回 false
```

---

### 5. 获取栈的大小（Size）
- 返回栈中元素的数量。
- 时间复杂度：`O(1)`。

**示例：**
```javascript
stack.size(); // 返回栈中元素的数量
```

---

## 三、栈的实现方式

栈可以通过多种方式实现，最常见的是*基于数组或链表*。

### 1. 基于数组的实现
使用数组模拟栈，利用数组的末尾作为栈顶。

**示例代码（JavaScript）：**
```javascript
class Stack {
    constructor() {
        this.items = [];
    }

    push(element) {
        this.items.push(element);
    }

    pop() {
        if (this.isEmpty()) {
            throw new Error("Stack is empty");
        }
        return this.items.pop();
    }

    peek() {
        if (this.isEmpty()) {
            throw new Error("Stack is empty");
        }
        return this.items[this.items.length - 1];
    }

    isEmpty() {
        return this.items.length === 0;
    }

    size() {
        return this.items.length;
    }
}
```

---

### 2. 基于链表的实现
使用单链表模拟栈，链表的头部作为栈顶。

**示例代码（JavaScript）：**
```javascript
class Node {
    constructor(value) {
        this.value = value;
        this.next = null;
    }
}

class Stack {
    constructor() {
        this.top = null;
        this.size = 0;
    }

    push(value) {
        const newNode = new Node(value);
        newNode.next = this.top;
        this.top = newNode;
        this.size++;
    }

    pop() {
        if (this.isEmpty()) {
            throw new Error("Stack is empty");
        }
        const poppedValue = this.top.value;
        this.top = this.top.next;
        this.size--;
        return poppedValue;
    }

    peek() {
        if (this.isEmpty()) {
            throw new Error("Stack is empty");
        }
        return this.top.value;
    }

    isEmpty() {
        return this.top === null;
    }

    size() {
        return this.size;
    }
}
```

---

## 四、栈的应用场景

栈在实际开发中有许多应用，以下是一些常见的例子：

### 1. 函数调用栈
- 每次调用函数时，当前函数的上下文（如局部变量、返回地址）会被压入调用栈。
- 当函数执行完毕时，上下文从栈中弹出，恢复到上一级函数。

---

### 2. 表达式求值与语法解析
- **中缀表达式转后缀表达式**：用于计算数学表达式。
- **括号匹配**：检查括号是否成对出现且嵌套正确。

**示例：** 括号匹配
```javascript
function isValidParentheses(s) {
    const stack = [];
    const map = { ')': '(', ']': '[', '}': '{' };

    for (let char of s) {
        if (char === '(' || char === '[' || char === '{') {
            stack.push(char);
        } else {
            if (stack.pop() !== map[char]) {
                return false;
            }
        }
    }
    return stack.length === 0;
}

console.log(isValidParentheses("()[]{}")); // true
console.log(isValidParentheses("(]"));     // false
```

---

### 3. 深度优先搜索（DFS）
- 使用栈实现递归的非递归版本。
- 例如：图的遍历、迷宫问题。

---

### 4. 回溯算法
- 在解决排列组合、八皇后问题等场景时，使用栈保存状态以便回溯。

---

### 5. 浏览器历史记录
- 浏览器的前进和后退功能可以使用两个栈实现：
  - 一个栈保存当前页面之前的页面。
  - 另一个栈保存已经回退的页面。

---

## 五、栈的优缺点

### 优点
1. **简单高效**：操作的时间复杂度均为 `O(1)`。
2. **易于实现**：基于数组或链表即可轻松实现。
3. **适用范围广**：适用于多种场景，如递归、表达式求值等。

### 缺点
1. **受限操作**：只能在一端进行操作，灵活性较低。
2. **容量限制**：基于数组实现时，可能需要动态扩容。

---

## 六、总结

栈是一种基础且重要的数据结构，具有简单高效的特性，广泛应用于函数调用、表达式求值、深度优先搜索等场景。掌握栈的定义、操作方法以及实现方式，能够帮助你更高效地解决实际问题。

### 学习建议
1. **动手实现**：尝试用数组和链表分别实现栈，并比较两者的优缺点。
2. **刷题巩固**：通过 LeetCode 等平台练习栈相关的题目（如括号匹配、表达式求值）。
3. **结合实际应用**：在项目中尝试使用栈解决递归或回溯问题。

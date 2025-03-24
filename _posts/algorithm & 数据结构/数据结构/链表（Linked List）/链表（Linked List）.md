**链表（Linked List）** 通过*动态内存管理*和*高效的增删*操作，在特定场景下展现出独特优势，是*理解指针操作*和*复杂数据结构*的基础。

---

## 定义与特点

### 定义
`链表（Linked List）`是一种*通过指针（引用）连接节点*的线性数据结构。

`节点（Node）`（每个元素）包含两部分：
- **数据域**：存储实际的数据。
- **指针域**：指向下一个节点的引用（或地址）。

### 特点
- **非连续内存**：*节点在内存中非连续存储*
- **按需扩展**：无需预先分配固定空间，*大小可以动态调整*，不需要预先分配固定的空间
- **高效增删**：插入/删除节点的时间复杂度为 O(1)（已知位置时）
- **随机访问效率低** ：链表不像数组那样支持随机访问，*访问某个节点需要从头节点开始遍历*，时间复杂度为 O(n)。

- **对比数组**：

链表的插入/删除效率较高，而访问效率较低；数组的访问效率较高，而插入效率较低。

| 特性        | 数组（相同类型）                  | 链表                                 |
| --------- | ------------------------- | ---------------------------------- |
| **内存分配**  | 连续内存                      | 动态分配，非连续的分散内存（指针连接）                |
| **访问速度**  | O(1)（随机访问）                | O(n)（遍历，顺序访问）                      |
| **插入/删除** | O(1)（尾部）；O(n)（其他位置，需移动元素） | O(1)（首部、*已知位置*）；O(n)（*未知位置*，需遍历查找） |
| **空间开销**  | 固定大小，可能浪费                 | 额外指针空间                             |
| **适用场景**  | 频繁访问、数据量固定                | 频繁增删、动态大小                          |
| **缓存友好性** | 高（空间局部性）                  | 低                                  |

数组在内存中的“连续”：
![[45bed2e978aea10fc577e888a5f293bc_MD5.png]]

链表在内存中的“非连续”：
![[78b973f718ed58a44f63c041de3c9807_MD5.png]]
简化一下：
![[191e95cff3a33c6ee3f50905791b06b0_MD5.png]] 
*有时还会设定一个 head 节点*来专门指向链表的开始位置：  
![[f49d5bdddbb3acf60fb4ab6291bf5ba9_MD5.png]] 


## 链表的基本操作

**插入操作**：
  
- **头部插入**：将新节点的 `next` 指向原头节点，并更新头节点为新节点（时间复杂度O(1)）。
- **尾部插入**：遍历至最后一个节点，将其 `next` 指向新节点（时间复杂度O(n)）。
- **指定位置插入**：找到目标位置的前驱节点，调整指针（时间复杂度O(n)）。

**删除操作**：
 
- **头部删除**：直接移动头指针到下一个节点（O(1)）。
- **尾部删除**：遍历至倒数第二个节点，断开其 `next` 指针（O(n)）。
- **指定位置删除**：调整前驱节点的 `next` 以跳过目标节点（O(n)）。

**查找** ：
查找链表中的某个节点。

**遍历** ：
遍历链表中的所有节点。

**反转链表**：
通过迭代调整指针方向，将每个节点的 `next` 指向前一个节点。

**边界处理**：
始终检查链表是否为空或索引是否越界，避免空指针异常。

## 单向链表的基本操作

单向链表是最简单的链表形式，每个节点只有一个指向下一个节点的指针。

**节点类：**

```javascript
/**
 * 链表节点类
 */
class Node {
  constructor(value) {
    this.value = value;  // 节点存储的值
    this.next = null;    // 指向下一个节点的指针（默认为null）
  }
}
```

**链表类：**
头节点（head）：就是第一个节点，head = firstNode，head保存第一个节点的引用

```js
/**
 * 链表类
 */
class LinkedList {
  constructor() {
    this.head = null;   // 链表头节点，第一个节点（初始为空链表）
    this.size = 0;      // 链表长度计数器
  }

  //增删改查的方法
  //...
}

```

### 插入
#### 头部插入
将新节点的 `next` 指向原头节点，并更新头节点为新节点（时间复杂度O(1)）。

```js
/**
   * 在链表头部插入新节点
   * 时间复杂度：O(1)
   * @param {*} value - 要插入的值
   */
  prepend(value) {
    const newNode = new Node(value);
    newNode.next = this.head;  // 新节点指向原头节点
    this.head = newNode;        // 更新头节点为新节点
    this.size++;
  }

```


#### 尾部插入
遍历至最后一个节点，将其 `next` 指向新节点（时间复杂度O(n)）。

```js
/**
   * 在链表尾部插入新节点
   * 时间复杂度：O(n)
   * @param {*} value - 要插入的值
   */
  append(value) {
    const newNode = new Node(value);
    // 处理空链表情况
    if (this.head === null) {
      this.head = newNode;
    } else {
      // 遍历到最后一个节点
      let current = this.head;
      while (current.next !== null) {//???
        current = current.next;
      }
      current.next = newNode;  // 最后一个节点指向新节点
    }
    this.size++;
  }

```

![[fa22836b8cb4ea612fd8ee9872468792_MD5.png]]

#### 指定位置插入
找到目标位置的前驱节点，调整指针（时间复杂度O(n)）。

```js
/**
   * 在指定索引位置插入节点
   * 时间复杂度：O(n)
   * @param {number} index - 要插入的位置（0-based）
   * @param {*} value - 要插入的值
   * @returns {boolean} 是否插入成功
   */
  insertAt(index, value) {
    if (index < 0 || index > this.size) return false;  // 越界检查
    
    // 处理头部插入的特殊情况
    if (index === 0) {
      this.prepend(value);
      return true;
    }

    const newNode = new Node(value);
    // 找到插入位置的前驱节点
    let current = this.head;
    for (let i = 0; i < index - 1; i++) {
      current = current.next;
    }
    // 调整指针连接
    newNode.next = current.next;
    current.next = newNode;
    this.size++;
    return true;
  }

```

插入前：   
![[ebe6b0ad45fd798d55e9e0f2638a8bf7_MD5.png]]
插入后：   
![[3f22303796854d5b1edc5aa9a91c7ba7_MD5.png]]
  

### 删除
#### 头部删除
直接移动头指针到下一个节点（O(1)）。
```js
  /**
   * 删除头部节点
   * 时间复杂度：O(1)
   * @returns {*} 被删除节点的值
   */
  deleteHead() {
    if (this.head === null) return null;
    const value = this.head.value;
    this.head = this.head.next;  // 头指针后移
    this.size--;
    return value;
  }
```

#### 尾部删除
遍历至倒数第二个节点，断开其 `next` 指针（O(n)）。

```js
/**
   * 删除尾部节点
   * 时间复杂度：O(n)
   * @returns {*} 被删除节点的值
   */
  deleteTail() {
    if (this.head === null) return null;
    // 处理只有一个节点的特殊情况
    if (this.head.next === null) {
      return this.deleteHead();
    }

    // 找到倒数第二个节点
    let current = this.head;
    while (current.next.next !== null) {
      current = current.next;
    }
    const value = current.next.value;
    current.next = null;  // 断开最后一个节点的连接
    this.size--;
    return value;
  }
```

#### 指定位置删除
调整前驱节点的 `next` 以跳过目标节点（O(n)）。

```js
/**
   * 删除指定索引位置的节点
   * 时间复杂度：O(n)
   * @param {number} index - 要删除的位置（0-based）
   * @returns {*} 被删除节点的值
   */
  deleteAt(index) {
    if (index < 0 || index >= this.size) return null;  // 越界检查
    if (index === 0) return this.deleteHead();        // 处理头部删除

    // 找到要删除节点的前驱节点
    let current = this.head;
    for (let i = 0; i < index - 1; i++) {
      current = current.next;
    }
    const value = current.next.value;
    current.next = current.next.next;  // 跳过要删除的节点
    this.size--;
    return value;
  }
```

### 查找
查找值对应的第一个索引。

```js
/**
   * 查找值对应的第一个索引
   * 时间复杂度：O(n)
   * @param {*} value - 要查找的值
   * @returns {number} 找到的索引，未找到返回-1
   */
  find(value) {
    let current = this.head;
    let index = 0;
    while (current !== null) {
      if (current.value === value) return index;
      current = current.next;
      index++;
    }
    return -1;
  }
```

### 遍历
从头节点开始，依次访问每个节点，直到末尾。
参考：[[遍历链表]]

### 完整代码：
```js
/**
 * 链表节点类
 */
class Node {
  constructor(value) {
    this.value = value;  // 节点存储的值
    this.next = null;    // 指向下一个节点的指针（默认为null）
  }
}

/**
 * 链表类
 */
class LinkedList {
  constructor() {
    this.head = null;   // 链表头节点（初始为空链表）
    this.size = 0;      // 链表长度计数器
  }

  /**
   * 在链表头部插入新节点
   * 时间复杂度：O(1)
   * @param {*} value - 要插入的值
   */
  prepend(value) {
    const newNode = new Node(value);
    newNode.next = this.head;  // 新节点指向原头节点
    this.head = newNode;        // 更新头节点为新节点
    this.size++;
  }

  /**
   * 在链表尾部插入新节点
   * 时间复杂度：O(n)
   * @param {*} value - 要插入的值
   */
  append(value) {
    const newNode = new Node(value);
    // 处理空链表情况
    if (this.head === null) {
      this.head = newNode;
    } else {
      // 遍历到最后一个节点
      let current = this.head;
      while (current.next !== null) {
        current = current.next;
      }
      current.next = newNode;  // 最后一个节点指向新节点
    }
    this.size++;
  }

  /**
   * 在指定索引位置插入节点
   * 时间复杂度：O(n)
   * @param {number} index - 要插入的位置（0-based）
   * @param {*} value - 要插入的值
   * @returns {boolean} 是否插入成功
   */
  insertAt(index, value) {
    if (index < 0 || index > this.size) return false;  // 越界检查
    
    // 处理头部插入的特殊情况
    if (index === 0) {
      this.prepend(value);
      return true;
    }

    const newNode = new Node(value);
    // 找到插入位置的前驱节点
    let current = this.head;
    for (let i = 0; i < index - 1; i++) {
      current = current.next;
    }
    // 调整指针连接
    newNode.next = current.next;
    current.next = newNode;
    this.size++;
    return true;
  }

  /**
   * 删除头部节点
   * 时间复杂度：O(1)
   * @returns {*} 被删除节点的值
   */
  deleteHead() {
    if (this.head === null) return null;
    const value = this.head.value;
    this.head = this.head.next;  // 头指针后移
    this.size--;
    return value;
  }

  /**
   * 删除尾部节点
   * 时间复杂度：O(n)
   * @returns {*} 被删除节点的值
   */
  deleteTail() {
    if (this.head === null) return null;
    // 处理只有一个节点的特殊情况
    if (this.head.next === null) {
      return this.deleteHead();
    }

    // 找到倒数第二个节点
    let current = this.head;
    while (current.next.next !== null) {
      current = current.next;
    }
    const value = current.next.value;
    current.next = null;  // 断开最后一个节点的连接
    this.size--;
    return value;
  }

  /**
   * 删除指定索引位置的节点
   * 时间复杂度：O(n)
   * @param {number} index - 要删除的位置（0-based）
   * @returns {*} 被删除节点的值
   */
  deleteAt(index) {
    if (index < 0 || index >= this.size) return null;  // 越界检查
    if (index === 0) return this.deleteHead();        // 处理头部删除

    // 找到要删除节点的前驱节点
    let current = this.head;
    for (let i = 0; i < index - 1; i++) {
      current = current.next;
    }
    const value = current.next.value;
    current.next = current.next.next;  // 跳过要删除的节点
    this.size--;
    return value;
  }

  /**
   * 查找值对应的第一个索引
   * 时间复杂度：O(n)
   * @param {*} value - 要查找的值
   * @returns {number} 找到的索引，未找到返回-1
   */
  find(value) {
    let current = this.head;
    let index = 0;
    while (current !== null) {
      if (current.value === value) return index;
      current = current.next;
      index++;
    }
    return -1;
  }

  /**
   * 反转链表
   * 时间复杂度：O(n)
   */
  reverse() {
    let prev = null;      // 前驱指针
    let current = this.head;  // 当前指针
    while (current !== null) {
      const next = current.next;  // 临时保存下一个节点
      current.next = prev;        // 反转指针方向
      prev = current;             // 前驱指针后移
      current = next;             // 当前指针后移
    }
    this.head = prev;  // 更新头指针
  }

  /**
   * 将链表转换为数组
   * 时间复杂度：O(n)
   * @returns {Array} 包含链表元素的数组
   */
  toArray() {
    const array = [];
    let current = this.head;
    while (current !== null) {
      array.push(current.value);
      current = current.next;
    }
    return array;
  }

  /**
   * 可视化打印链表结构
   */
  print() {
    let str = '';
    let current = this.head;
    while (current !== null) {
      str += `${current.value} -> `;
      current = current.next;
    }
    str += 'null';
    console.log(str);
  }
}
```

---

## 链表的类型

### 1. 单向链表（Singly Linked List）

- **结构**：每个节点包含数据域和指向下一节点的指针
- **JavaScript 实现**：

  ```javascript
  class ListNode {
    constructor(val) {
      this.val = val; // 节点值
      this.next = null; // 指向下一节点
    }
  }

  class SinglyLinkedList {
    constructor() {
      this.head = null; // 链表头节点
      this.size = 0; // 链表长度
    }

    // 头部插入节点
    addAtHead(val) {
      const newNode = new ListNode(val);
      newNode.next = this.head;
      this.head = newNode;
      this.size++;
    }

    // 尾部插入节点
    addAtTail(val) {
      const newNode = new ListNode(val);
      if (!this.head) {
        this.head = newNode;
      } else {
        let current = this.head;
        while (current.next) {
          current = current.next;
        }
        current.next = newNode;
      }
      this.size++;
    }

    // 删除节点
    deleteNode(val) {
      if (!this.head) return;
      if (this.head.val === val) {
        this.head = this.head.next;
        this.size--;
        return;
      }
      let current = this.head;
      while (current.next) {
        if (current.next.val === val) {
          current.next = current.next.next;
          this.size--;
          return;
        }
        current = current.next;
      }
    }
  }
  ```

### 2. 双向链表（Doubly Linked List）

- **结构**：节点包含*前驱*和*后继*指针
- **优势**：支持双向遍历，删除操作更高效
- **实现**：
  ```javascript
  class DoublyListNode {
    constructor(val) {
      this.val = val;
      this.prev = null; // 前驱指针
      this.next = null; // 后继指针
    }
  }
  ```

### 3. 循环链表（Circular Linked List）

- **结构**：*尾节点指向头节点形成环*
- **应用场景**：轮播图、约瑟夫环问题

---

## 链表的经典应用场景

### 1. 实现高级数据结构

- **栈/队列**：通过链表实现动态容量
- **哈希表冲突处理**：链地址法解决哈希冲突
- **图结构**：邻接表表示图的连接关系

### 2. 实际开发案例

- **浏览器历史记录**：双向链表实现前进/后退功能
- **音乐播放列表**：循环链表实现循环播放
- **LRU 缓存淘汰算法**：哈希表+双向链表实现 O(1) 操作

---

## 链表的性能优化

### 1. 虚拟头节点（Dummy Head）
参考：[[虚拟头节点（Dummy Head）]]

- **作用**：*简化边界条件处理*（如空链表或头节点操作）
- **代码示例**：

  ```javascript
  class OptimizedLinkedList {
    constructor() {
      this.dummy = new ListNode(-1); // 虚拟头节点
      this.size = 0;
    }

    addAtIndex(index, val) {
      if (index > this.size) return;
      const newNode = new ListNode(val);
      let prev = this.dummy;
      for (let i = 0; i < index; i++) {
        prev = prev.next;
      }
      newNode.next = prev.next;
      prev.next = newNode;
      this.size++;
    }
  }
  ```

### 2. 快慢指针技巧

- **应用场景**：检测循环、寻找中间节点
- **经典问题**：判断链表是否有环
  ```javascript
  function hasCycle(head) {
    let slow = head;
    let fast = head;
    while (fast && fast.next) {
      slow = slow.next;
      fast = fast.next.next;
      if (slow === fast) return true;
    }
    return false;
  }
  ```

---

## 链表的常见问题与解决方案

| 问题类型           | 解决方法     | 代码示例               |
| -------------- | -------- | ------------------ |
| **反转链表**       | 迭代法或递归法  | [见下方完整代码](#反转链表)   |
| **合并有序链表**     | 双指针比较节点  | [见下方完整代码](#合并有序链表) |
| **删除倒数第 N 节点** | 快慢指针间距控制 | [见下方完整代码](#删除倒数节点) |

---

### 反转链表

```javascript
function reverseList(head) {
  let prev = null;
  let current = head;
  while (current) {
    const nextTemp = current.next;
    current.next = prev;
    prev = current;
    current = nextTemp;
  }
  return prev;
}
```

### 合并有序链表
参考：[[_posts/algorithm & 数据结构/数据结构/链表（Linked List）/合并有序链表|合并有序链表]]、[[1.合并有序链表|1.合并有序链表]]

```javascript
function mergeTwoLists(l1, l2) {
  const dummy = new ListNode(-1);
  let current = dummy;
  while (l1 && l2) {
    if (l1.val < l2.val) {
      current.next = l1;
      l1 = l1.next;
    } else {
      current.next = l2;
      l2 = l2.next;
    }
    current = current.next;
  }
  current.next = l1 || l2;
  return dummy.next;
}
```

### 删除倒数节点

```javascript
function removeNthFromEnd(head, n) {
  const dummy = new ListNode(-1);
  dummy.next = head;
  let fast = dummy;
  let slow = dummy;
  for (let i = 0; i <= n; i++) {
    fast = fast.next;
  }
  while (fast) {
    fast = fast.next;
    slow = slow.next;
  }
  slow.next = slow.next.next;
  return dummy.next;
}
```

---

## 六、链表学习建议

1. **图形化辅助**：用纸笔绘制节点指针变化过程
2. **调试技巧**：在循环中打印节点值观察链表状态
3. **实战练习**：LeetCode 经典题目（#206 反转链表、#141 环形链表、#21 合并链表）

---



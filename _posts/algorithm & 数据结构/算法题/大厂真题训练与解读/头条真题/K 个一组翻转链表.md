
> 题目描述：
> 	给你一个链表，每  k  个节点一组进行翻转，请你返回翻转后的链表。  
> 	k  是一个正整数，它的值小于或等于链表的长度。  
> 	如果节点总数不是  k  的整数倍，那么请将最后剩余的节点保持原有顺序。

> 示例：
> 	给你这个链表：1->2->3->4->5  
> 	当  k = 2 时，应当返回: 2->1->4->3->5  
> 	当  k = 3 时，应当返回: 3->2->1->4->5

**命题关键字：链表、链表的翻转、复杂数据处理**

参考：[[27、大厂真题训练与解读——头条真题#K 个一组翻转链表]]


以下是用 JavaScript 实现“每 k 个节点一组翻转链表”的代码。我们将使用**分组翻转**的方法来解决这个问题，时间复杂度为 O(n)，空间复杂度为 O(1)。

---

### 解题思路

1. **问题分析**：
   - 给定一个链表和正整数 `k`，要求每 `k` 个节点一组进行翻转。
   - 如果最后一组节点不足 `k` 个，则保持原有顺序。

2. **核心思想**：
   - 使用一个辅助函数 `reverseGroup` 来翻转长度为 `k` 的子链表。
   - 遍历链表，找到每组的起始和结束节点：
     - 如果当前组的节点数不足 `k`，则直接返回结果。
     - 否则，翻转当前组，并将其与前一组和后一组连接起来。
   - 返回翻转后的链表。

3. **算法步骤**：
   - 定义一个虚拟头节点 `dummy` 指向链表头部，方便处理边界情况。
   - 遍历链表，尝试对每组 `k` 个节点进行翻转。
   - 如果当前组不足 `k` 个节点，则保持原有顺序。
   - 返回翻转后的链表。

4. **时间复杂度**：O(n)，其中 n 是链表的节点数。
5. **空间复杂度**：O(1)，仅使用常量级别的额外空间。

---

### 代码实现

```javascript
// 定义链表节点类
class ListNode {
    constructor(val = 0, next = null) {
        this.val = val;
        this.next = next;
    }
}

function reverseKGroup(head, k) {
    if (!head || k === 1) return head;

    // 创建虚拟头节点
    const dummy = new ListNode(0);
    dummy.next = head;

    let prevGroupEnd = dummy; // 上一组的尾节点
    let current = head; // 当前组的起始节点

    while (current) {
        // 找到当前组的结束节点
        let groupStart = current;
        let groupEnd = prevGroupEnd;

        // 检查当前组是否有 k 个节点
        for (let i = 0; i < k; i++) {
            groupEnd = groupEnd.next;
            if (!groupEnd) return dummy.next; // 不足 k 个节点，直接返回
        }

        const nextGroupStart = groupEnd.next; // 下一组的起始节点

        // 翻转当前组
        reverseList(groupStart, groupEnd);

        // 将翻转后的组连接到链表中
        prevGroupEnd.next = groupEnd; // 上一组的尾节点指向当前组的新头节点
        groupStart.next = nextGroupStart; // 当前组的旧头节点指向下一组的起始节点

        // 更新指针
        prevGroupEnd = groupStart; // 当前组的旧头节点成为新的尾节点
        current = nextGroupStart; // 移动到下一组的起始节点
    }

    return dummy.next;
}

// 辅助函数：翻转从 start 到 end 的子链表
function reverseList(start, end) {
    let prev = null;
    let current = start;

    while (prev !== end) {
        const nextNode = current.next;
        current.next = prev;
        prev = current;
        current = nextNode;
    }
}
```

---

### 测试代码

为了验证上述实现的正确性，我们可以编写一些测试用例：

```javascript
// 辅助函数：创建链表
function createLinkedList(arr) {
    const dummy = new ListNode();
    let current = dummy;
    for (const val of arr) {
        current.next = new ListNode(val);
        current = current.next;
    }
    return dummy.next;
}

// 辅助函数：打印链表
function printLinkedList(head) {
    const result = [];
    let current = head;
    while (current) {
        result.push(current.val);
        current = current.next;
    }
    return result;
}

// 测试用例
const list1 = createLinkedList([1, 2, 3, 4, 5]);
console.log(printLinkedList(reverseKGroup(list1, 2))); 
// 输出: [2, 1, 4, 3, 5]

const list2 = createLinkedList([1, 2, 3, 4, 5]);
console.log(printLinkedList(reverseKGroup(list2, 3))); 
// 输出: [3, 2, 1, 4, 5]

const list3 = createLinkedList([1, 2, 3, 4, 5]);
console.log(printLinkedList(reverseKGroup(list3, 1))); 
// 输出: [1, 2, 3, 4, 5]

const list4 = createLinkedList([1]);
console.log(printLinkedList(reverseKGroup(list4, 2))); 
// 输出: [1]
```

---

### 运行结果

```plaintext
输入: 链表: 1->2->3->4->5, k = 2
输出: 2->1->4->3->5
```

```plaintext
输入: 链表: 1->2->3->4->5, k = 3
输出: 3->2->1->4->5
```

```plaintext
输入: 链表: 1->2->3->4->5, k = 1
输出: 1->2->3->4->5
```

```plaintext
输入: 链表: 1, k = 2
输出: 1
```

---

### 代码详解

1. **虚拟头节点**：
   - 创建一个虚拟头节点 `dummy`，方便处理链表头部的翻转。

2. **遍历链表**：
   - 使用 `prevGroupEnd` 记录上一组的尾节点。
   - 使用 `current` 遍历链表，找到每组的起始和结束节点。

3. **检查节点数量**：
   - 如果当前组不足 `k` 个节点，则直接返回结果。

4. **翻转子链表**：
   - 调用 `reverseList` 翻转当前组的节点。

5. **连接翻转后的组**：
   - 将翻转后的组与前一组和后一组连接起来。

6. **返回结果**：
   - 最终返回翻转后的链表。

---

### 总结

- **分组翻转**是解决本问题的核心方法，能够高效地翻转每组节点。
- 时间复杂度为 O(n)，空间复杂度为 O(1)。
- 通过虚拟头节点和指针操作，避免了复杂的边界条件处理。


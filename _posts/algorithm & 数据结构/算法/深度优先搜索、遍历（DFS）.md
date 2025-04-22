`深度优先遍历（Depth-First Search，DFS）` 是一种用于*遍历或搜索树、图等数据结构*的算法。它的**核心思想**是从起始节点开始，沿着一条路径*尽可能深入*地访问节点，直到*到达叶子节点或无法继续为止*，然后回溯到上一个节点，继续探索其他未访问的分支。

DFS 在前端开发中常用于**解决 DOM 树操作、依赖解析、路径查找**等问题。以下是关于深度优先遍历的详细讲解：

---

## 1. 深度优先遍历的基本概念

### (1) 核心思想
- **深度优先**：优先访问当前节点的子节点，直到无法继续深入为止。
- **回溯**：当某个分支的所有节点都被访问后，返回到上一级节点，继续探索其他分支。

### (2) 遍历顺序
对于树或图，DFS 的遍历顺序通常有以下三种：
- **前序遍历（Pre-order）**：先访问根节点，再依次访问左子树和右子树。
- **中序遍历（In-order）**：先访问左子树，再访问根节点，最后访问右子树（主要适用于二叉树）。
- **后序遍历（Post-order）**：先访问左子树和右子树，最后访问根节点。

---

## 2. 深度优先遍历的实现方式

DFS 可以通过两种方式实现：
1. **递归实现**
2. **栈实现**

### (1) 递归实现
递归是*最直观*的实现方式，利用*函数调用栈*来**实现回溯**。

**示例代码（二叉树的前序遍历）：**
```javascript
class TreeNode {
  constructor(value, left = null, right = null) {
    this.value = value;
    this.left = left;
    this.right = right;
  }
}

function dfsPreOrder(node, result = []) {
  if (!node) return result;

  // 访问当前节点
  result.push(node.value);

  // 递归访问左子树
  dfsPreOrder(node.left, result);

  // 递归访问右子树
  dfsPreOrder(node.right, result);

  return result;
}

// 测试
const tree = new TreeNode(1, 
  new TreeNode(2, new TreeNode(4), new TreeNode(5)), 
  new TreeNode(3)
);

console.log(dfsPreOrder(tree)); // 输出: [1, 2, 4, 5, 3]
```

---

### (2) 栈实现
使用*显式的栈*模拟递归过程，**避免递归深度过大导致栈溢出**。

**示例代码（二叉树的前序遍历）：**
```javascript
function dfsPreOrderWithStack(root) {
  if (!root) return [];

  const stack = [root]; // 初始化栈
  const result = [];

  while (stack.length > 0) {
    const node = stack.pop(); // 弹出栈顶节点
    result.push(node.value);  // 访问当前节点

    // 先右后左入栈，保证左子树先被访问
    if (node.right) stack.push(node.right);
    if (node.left) stack.push(node.left);
  }

  return result;
}

// 测试
const tree = new TreeNode(1, 
  new TreeNode(2, new TreeNode(4), new TreeNode(5)), 
  new TreeNode(3)
);

console.log(dfsPreOrderWithStack(tree)); // 输出: [1, 2, 4, 5, 3]
```

---

## 3. 深度优先遍历的应用场景

### (1) DOM 树操作
在前端开发中，**DFS 常用于遍历 DOM 树**，查找特定节点或修改样式。

**示例：查找所有文本节点**
```javascript
function getTextNodes(node, result = []) {
  if (node.nodeType === Node.TEXT_NODE) {
    result.push(node.textContent.trim());
  } else if (node.nodeType === Node.ELEMENT_NODE) {
    for (let child of node.childNodes) {
      getTextNodes(child, result);
    }
  }
  return result;
}

// 测试
const div = document.createElement("div");
div.innerHTML = "<p>Hello <span>world</span>!</p>";
console.log(getTextNodes(div)); // 输出: ["Hello", "world", "!"]
```

---

### (2) 图的连通性检测
DFS 可以用来检测图中是否存在从起点到终点的路径。

**示例：检测路径是否存在**
```javascript
function hasPath(graph, start, end, visited = new Set()) {
  if (start === end) return true;
  visited.add(start);

  for (let neighbor of graph[start] || []) {
    if (!visited.has(neighbor)) {
      if (hasPath(graph, neighbor, end, visited)) {
        return true;
      }
    }
  }

  return false;
}

// 测试
const graph = {
  A: ["B", "C"],
  B: ["D"],
  C: ["E"],
  D: ["F"],
  E: [],
  F: [],
};

console.log(hasPath(graph, "A", "F")); // 输出: true
console.log(hasPath(graph, "A", "G")); // 输出: false
```

---

### (3) 拓扑排序
在有向无环图（DAG）中，DFS 可以用于生成拓扑排序。

**示例：拓扑排序**
```javascript
function topologicalSort(graph) {
  const visited = new Set();
  const stack = [];

  function dfs(node) {
    if (visited.has(node)) return;
    visited.add(node);

    for (let neighbor of graph[node] || []) {
      dfs(neighbor);
    }

    stack.push(node);
  }

  for (let node in graph) {
    if (!visited.has(node)) {
      dfs(node);
    }
  }

  return stack.reverse();
}

// 测试
const graph = {
  A: ["C"],
  B: ["C", "D"],
  C: ["E"],
  D: ["F"],
  E: [],
  F: [],
};

console.log(topologicalSort(graph)); // 输出: ["B", "D", "A", "C", "F", "E"]
```

---

## 4. 深度优先遍历的时间复杂度与空间复杂度

### (1) 时间复杂度
- 对于树或图，DFS 的时间复杂度为 **O(V + E)**，其中：
  - `V` 是节点数。
  - `E` 是边数。

### (2) 空间复杂度
- 递归实现的空间复杂度为 **O(H)**，其中 `H` 是树的高度（最坏情况下为 `O(V)`）。
- 栈实现的空间复杂度为 **O(V)**，因为需要存储所有节点。

---

## 5. 深度优先遍历的优缺点

### 优点
1. **实现简单**：递归实现非常直观。
2. **适合深度优先问题**：如路径查找、连通性检测等。

### 缺点
1. **递归深度限制**：对于深层的树或图，可能导致栈溢出。
2. **不适合广度优先问题**：如最短路径问题更适合广度优先搜索（BFS）。

---

## 6. 总结

`深度优先遍历`是一种*高效且常用*的算法，适用于*树、图*等数据结构的遍历和搜索。它的核心思想是沿着一条路径尽可能深入地访问节点，直到无法继续为止，然后回溯并探索其他分支。

在前端开发中，DFS 广泛应用于 DOM 树操作、依赖解析、路径查找等场景。掌握 DFS 的递归和栈实现方式，可以帮助开发者更灵活地解决实际问题。
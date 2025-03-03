二叉树（Binary Tree）是一种非常重要的数据结构，它是一种*特殊的树*形结构，其中每个节点*最多有两个子节点*，分别称为**左子节点**和**右子节点**。二叉树在计算机科学中有广泛的应用，例如用于实现二叉搜索树、堆、表达式树等。

## 1. 二叉树的基本概念

- **节点（Node）**：二叉树中的每个元素称为节点，节点包含数据和指向其左子节点和右子节点的引用。
- **根节点（Root Node）**：二叉树的最顶层节点，没有父节点。
- **叶子节点（Leaf Node）**：没有子节点的节点称为叶子节点。
- **父节点（Parent Node）**：一个节点直接连接到另一个节点时，前者称为后者的父节点。
- **子节点（Child Node）**：一个节点直接连接到另一个节点时，后者称为前者的子节点。
- **深度（Depth）**：从根节点到某个节点的边的数量称为该节点的深度。
- **高度（Height）**：从某个节点到叶子节点的最长路径上的边的数量称为该节点的高度。整棵树的高度是根节点的高度。
- **子树（Subtree）**：二叉树中的任意节点及其后代构成的树称为子树。

## 2. 二叉树的性质

- 每个节点*最多有两个子节点*。
- 左子树和右子树是有顺序的，*不能随意交换*。
- 二叉树可以为空（即没有任何节点），也可以只有一个根节点。

## 3. 二叉树的类型

### 3.1 满二叉树（Full Binary Tree）
满二叉树是指*除最后一层外*，每一层上的所有节点*都有两个子节点*的二叉树。

### 3.2 完全二叉树（Complete Binary Tree）
完全二叉树是指*除最后一层外*，其它各层的节点数都达到最大值，并且最后一层的节点都集中在该层最左边的二叉树。

### 3.3 平衡二叉树（Balanced Binary Tree）
平衡二叉树是指任何节点的左右子树的高度差不超过 1 的二叉树。

### 3.4 二叉搜索树（Binary Search Tree, BST）
二叉搜索树是一种特殊的二叉树，满足以下性质：
- 左子树上所有节点的值均小于它的根节点的值。
- 右子树上所有节点的值均大于它的根节点的值。
- 左右子树也分别为二叉搜索树。

## 4. 二叉树的实现

我们可以*使用类*来表示二叉树的节点，并通过递归或迭代的方式实现常见的操作（如插入、删除、查找、遍历等）。

### 4.1 定义二叉树的节点

```javascript
class TreeNode {
    constructor(value) {
        this.value = value;  // 节点的值
        this.left = null;    // 左子节点
        this.right = null;   // 右子节点
    }
}
```

### 4.2 创建二叉树

```javascript
// 创建二叉树
const root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
root.left.left = new TreeNode(4);
root.left.right = new TreeNode(5);
```

## 5. 二叉树的遍历

`二叉树的遍历`是指*按照某种顺序访问树中的所有节点*。

每一棵二叉树都应该由这三部分组成：  

![[9c261cfab6da9069f401f150a2ecd231_MD5.png]]

*对树的遍历，就可以看做是对这三个部分的遍历*。

*左子树一定先于右子树遍历*，那么遍历的顺序有三种： 
- **前序遍历**：根结点 -> 左子树 -> 右子树（根结点在前）
- **中序遍历**：左子树 -> 根结点 -> 右子树（根结点在中）
- **后序遍历**：左子树 -> 右子树 -> 根结点（根结点在后）


### 5.1 前序遍历（Pre-order Traversal）
前序遍历：根结点 -> 左子树 -> 右子树（根结点在前）。先访问根节点，再访问左子树，最后访问右子树。

```javascript
function preOrderTraversal(node) {
    if (node === null) return;
    console.log(node.value);  // 访问当前节点
    preOrderTraversal(node.left);  // 递归遍历左子树
    preOrderTraversal(node.right); // 递归遍历右子树
}

// 使用前序遍历
preOrderTraversal(root);  // 输出: A B D E C F
```

![[544460ad761bd5400e2bf48968d65756_MD5.gif]] 

### 5.2 中序遍历（In-order Traversal）
中序遍历：左子树 -> 根结点 -> 右子树（根结点在中）。先访问左子树，再访问根节点，最后访问右子树。

唯一发生改变的是递归式里调用递归函数的顺序——*左子树的访问会优先于根结点*

```javascript
function inOrderTraversal(node) {
    if (node === null) return;
    inOrderTraversal(node.left);  // 递归遍历左子树
    console.log(node.value);      // 访问当前节点
    inOrderTraversal(node.right); // 递归遍历右子树
}

// 使用中序遍历
inOrderTraversal(root);  // 输出: D B E A C F
```

![[8866733140588535575a15282397cd73_MD5.gif]]

### 5.3 后序遍历（Post-order Traversal）
后序遍历：左子树 -> 右子树 -> 根结点（根结点在后）。先访问左子树，再访问右子树，最后访问根节点。

```javascript
function postOrderTraversal(node) {
    if (node === null) return;
    postOrderTraversal(node.left);  // 递归遍历左子树
    postOrderTraversal(node.right); // 递归遍历右子树
    console.log(node.value);        // 访问当前节点
}

// 使用后序遍历
postOrderTraversal(root);  // 输出: D E B F C A
```

![[daeed460039cd91d1af9e5e17a6f55b8_MD5.gif]]

### 5.4 层序遍历（Level-order Traversal）
*按层次从上到下、从左到右*依次访问每个节点。

```javascript
function levelOrderTraversal(root) {
    if (!root) return;

    const queue = [root];  // 初始化队列，根节点入队

    while (queue.length > 0) {
        const currentNode = queue.shift();  // 出队
        console.log(currentNode.value);    // 访问当前节点

        if (currentNode.left) {
            queue.push(currentNode.left);  // 左子节点入队
        }
        if (currentNode.right) {
            queue.push(currentNode.right); // 右子节点入队
        }
    }
}

// 使用层序遍历
levelOrderTraversal(root);  // 输出: 1 2 3 4 5
```

## 6. 二叉搜索树（Binary Search Tree, BST）

二叉搜索树是一种特殊的二叉树，满足以下性质：
- 左子树上所有节点的值均小于它的根节点的值。
- 右子树上所有节点的值均大于它的根节点的值。
- 左右子树也分别为二叉搜索树。

### 6.1 二叉搜索树的实现

```javascript
class TreeNode {
    constructor(value) {
        this.value = value;
        this.left = null;
        this.right = null;
    }
}

class BinarySearchTree {
    constructor() {
        this.root = null;
    }

    // 插入节点
    insert(value) {
        const newNode = new TreeNode(value);
        if (this.root === null) {
            this.root = newNode;
        } else {
            this.insertNode(this.root, newNode);
        }
    }

    insertNode(node, newNode) {
        if (newNode.value < node.value) {
            if (node.left === null) {
                node.left = newNode;
            } else {
                this.insertNode(node.left, newNode);
            }
        } else {
            if (node.right === null) {
                node.right = newNode;
            } else {
                this.insertNode(node.right, newNode);
            }
        }
    }

    // 查找节点
    search(node, value) {
        if (node === null) {
            return false;
        }
        if (value < node.value) {
            return this.search(node.left, value);
        } else if (value > node.value) {
            return this.search(node.right, value);
        } else {
            return true;
        }
    }

    // 中序遍历（递归）
    inOrderTraverse(node, callback) {
        if (node !== null) {
            this.inOrderTraverse(node.left, callback);
            callback(node.value);
            this.inOrderTraverse(node.right, callback);
        }
    }

    // 前序遍历（递归）
    preOrderTraverse(node, callback) {
        if (node !== null) {
            callback(node.value);
            this.preOrderTraverse(node.left, callback);
            this.preOrderTraverse(node.right, callback);
        }
    }

    // 后序遍历（递归）
    postOrderTraverse(node, callback) {
        if (node !== null) {
            this.postOrderTraverse(node.left, callback);
            this.postOrderTraverse(node.right, callback);
            callback(node.value);
        }
    }
}

// 使用二叉搜索树
const bst = new BinarySearchTree();
bst.insert(10);
bst.insert(5);
bst.insert(15);
bst.insert(3);
bst.insert(7);

console.log(bst.search(bst.root, 7));  // 输出: true
console.log(bst.search(bst.root, 20)); // 输出: false

// 中序遍历
bst.inOrderTraverse(bst.root, value => console.log(value));  // 输出: 3 5 7 10 15
```

## 7. 二叉树的应用

- **文件系统**：文件系统通常使用树形结构来组织文件和目录。
- **编译器**：编译器使用语法树来表示程序的结构。
- **数据库索引**：数据库中的 B 树、B+ 树等索引结构都是基于树的。
- **表达式求值**：二叉树可以用来表示数学表达式，便于进行求值和转换。

## 8. 总结

二叉树是一种非常灵活且强大的数据结构，能够高效地表示和处理具有层次关系的数据。二叉搜索树、AVL 树、红黑树等是二叉树的不同变种，各自适用于不同的应用场景。掌握二叉树的基本概念、常见操作（如插入、删除、查找）以及遍历方式，对于理解和解决许多算法问题非常重要。
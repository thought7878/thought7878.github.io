对单个ReactElement元素进行协调处理，复用或创建对应的 Fiber 节点。
该函数用于处理具有特定 key 的单个 React 元素，会尝试复用current fiber或创建新的 Fiber 节点。

**算法：**
- 1.遍历current fiber/现有的子节点链表，寻找可复用的fiber
	- 比较 current fiber 与 ReactElement 的key是否匹配？这是`diff 算法`的关键一步
	- key 匹配：
		- 检查元素类型是否匹配？（非 Fragment 类型的元素）
		- 类型匹配：
			- 删除该节点之后的所有兄弟节点
			- *复用current fiber*，使用新元素的 props。`useFiber`-->`createWorkInProgress`
			- 返回复用的新fiber
		- 类型不匹配：
			- 删除整个子树
			- 终止循环，*向下执行2的创建新fiber*
	- key 不匹配：
		- 删除该current fiber
		- 并继续检查下一个兄弟节点。回到1，继续下一个循环
- 2.如果遍历完current fiber都没有找到匹配项或key 匹配且类型不匹配，*创建一个新的 Fiber 节点*，根据ReactElement
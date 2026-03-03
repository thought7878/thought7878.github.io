对单个ReactElement元素进行协调处理，复用或创建对应的 Fiber 节点。
该函数用于处理具有特定 key 的单个 React 元素，会尝试复用current fiber或创建新的 Fiber 节点。

**算法：**
- 遍历current/现有的子节点链表，寻找可复用的fiber
	- 比较 current fiber 与 ReactElement 的key是否匹配，这是 React diff 算法的关键一步，匹配
	- key 不匹配，删除该current fiber并继续检查下一个兄弟节点
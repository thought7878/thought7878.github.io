对单个ReactElement元素进行协调处理，复用或创建对应的 Fiber 节点。
该函数用于处理具有特定 key 的单个 React 元素，会尝试复用current fiber或创建新的 Fiber 节点。

## 算法
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
			- 终止循环，*向下执行“2”的创建新fiber*
	- key 不匹配：
		- 删除该current fiber？
		- 并继续检查下一个兄弟节点。回到“1”，继续下一个循环
- 2.如果遍历完current fiber都没有找到匹配项 或 key 匹配且类型不匹配，*创建一个新的 Fiber 节点*，根据ReactElement
	- 根据 ReactElement 元素类型创建对应的新fiber
		- 普通元素类型：
			- *创建 Fiber*，根据ReactElement，`createFiberFromElement`
			- 建立与父fiber的关系
			- 返回新fiber
		- Fragment 类型：
			- 创建 Fragment Fiber
			- 建立与父fiber的关系
			- 返回新fiber


## 源码
`packages/react-reconciler/src/ReactChildFiber.old.js`

```ts
/**
   * 对单个元素进行协调处理，复用或创建对应的 Fiber 节点
   * 该函数用于处理具有特定 key 的单个 React 元素，会尝试复用current fiber或创建新的 Fiber 节点
   *
   * @param {Fiber} returnFiber - 父级 Fiber 节点/wip，新创建或复用的节点将以此为父节点
   * @param {Fiber | null} currentFirstChild - current/当前的第一个子 Fiber 节点，用于对比查找可复用节点
   * @param {ReactElement} element - 要协调的新 React 元素/ReactElement
   * @param {Lanes} lanes - 表示优先级的 lanes，用于调度和渲染控制
   * @returns {Fiber} 返回复用或新创建的 Fiber 节点
   */
  function reconcileSingleElement(
    returnFiber: Fiber,
    currentFirstChild: Fiber | null,
    element: ReactElement,
    lanes: Lanes,
  ): Fiber {
    // 提取新元素的 key 属性
    const key = element.key;
    // 从current/当前的第一个子节点开始遍历
    let child = currentFirstChild;

    // 遍历current/现有的子节点链表，寻找可复用的fiber
    while (child !== null) {
      // TODO: If key === null and child.key === null, then this only applies to
      // the first item in the list.

      // ！！！比较 current fiber 与 ReactElement 的key是否匹配，这是 React diff 算法的关键一步
      if (child.key === key) {
        // 获取新元素的类型
        const elementType = element.type;

        // 如果是 Fragment 类型
        if (elementType === REACT_FRAGMENT_TYPE) {
          // 检查现有节点是否也是 Fragment 类型
          if (child.tag === Fragment) {
            // 删除该节点之后的所有兄弟节点
            deleteRemainingChildren(returnFiber, child.sibling);
            // 复用现有节点，使用新元素的 props.children
            const existing = useFiber(child, element.props.children);
            existing.return = returnFiber;
            // 开发环境下添加调试信息
            if (__DEV__) {
              existing._debugSource = element._source;
              existing._debugOwner = element._owner;
            }
            return existing;
          }
        } else {
          // ！！！非 Fragment 类型的元素，检查元素类型是否匹配
          if (
            // 检查元素类型是否相同
            child.elementType === elementType ||
            // 在开发环境下，为了热重载功能做额外检查
            (__DEV__
              ? isCompatibleFamilyForHotReloading(child, element)
              : false) ||
            // 对于懒加载组件，需要解析实际类型进行比较
            // 我们需要在上面的热重载检查之后执行此操作，
            // 因为热重载在生产环境中的语义不同，因为它不会重新挂起
            (typeof elementType === 'object' &&
              elementType !== null &&
              elementType.$$typeof === REACT_LAZY_TYPE &&
              resolveLazy(elementType) === child.type)
          ) {
            // 匹配成功，删除该节点之后的所有兄弟节点
            deleteRemainingChildren(returnFiber, child.sibling);
            // 复用current fiber，使用新元素的 props
            const existing = useFiber(child, element.props);
            // 处理 ref 引用
            existing.ref = coerceRef(returnFiber, child, element);
            existing.return = returnFiber;
            // 开发环境下添加调试信息
            if (__DEV__) {
              existing._debugSource = element._source;
              existing._debugOwner = element._owner;
            }
            return existing;
          }
        }
        // 没有匹配成功的情况：类型不匹配，删除整个子树
        deleteRemainingChildren(returnFiber, child);
        break;
      } else {
        // key 不匹配，删除该current fiber并继续检查下一个兄弟节点
        deleteChild(returnFiber, child);
      }
      child = child.sibling;
    }

    // ！！！如果遍历完current fiber都没有找到匹配项 或 key 匹配且类型不匹配，
    // ！！！创建一个新的 Fiber 节点，根据ReactElement

    // 根据 ReactElement 元素类型创建对应的新fiber
    if (element.type === REACT_FRAGMENT_TYPE) {
      // 如果是 Fragment 类型，创建 Fragment Fiber
      const created = createFiberFromFragment(
        element.props.children,
        returnFiber.mode,
        lanes,
        element.key,
      );
      created.return = returnFiber;
      return created;
    } else {
      // 普通元素类型，创建 Fiber，根据ReactElement
      const created = createFiberFromElement(element, returnFiber.mode, lanes);
      // 处理 ref 引用
      created.ref = coerceRef(returnFiber, currentFirstChild, element);
      created.return = returnFiber;
      return created;
    }
  }
```
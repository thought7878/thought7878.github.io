函数式编程（Functional Programming, FP）是一种**以函数为核心构建程序**的编程范式。它强调**计算即函数求值**，避免共享状态、可变数据和副作用，从而提升代码的可预测性、可测试性和可维护性。

在前端开发中，FP 的思想被广泛应用于 React、Vue、状态管理（如 Redux）、数据处理等场景。下面从核心概念、原则、实践到前端应用，系统讲解函数式编程。

---

## 一、函数式编程的核心原则

### 1. **纯函数（Pure Functions）**
- **定义**：给定相同输入，总是返回相同输出，且**无副作用**。
- **副作用**包括：
  - 修改全局变量
  - 修改传入参数（引用类型）
  - 发起网络请求、DOM 操作、console.log 等

✅ 纯函数示例：
```js
// 纯函数：无副作用，只依赖输入
const add = (a, b) => a + b;

// 纯函数：返回新数组，不修改原数组
const double = (arr) => arr.map(x => x * 2);
```

❌ 非纯函数示例：
```js
let counter = 0;
const increment = () => counter++; // 依赖并修改外部状态

const mutateArray = (arr) => {
  arr.push(100); // 修改输入参数
  return arr;
};
```

> ✅ **优势**：易于测试、可缓存（memoization）、可并行执行。

---

### 2. **不可变性（Immutability）**
- 数据一旦创建，**不能被修改**。
- 更新数据时，**返回新副本**而非修改原数据。

前端实践：
```js
// ❌ 可变操作（破坏不可变性）
state.user.name = 'Alice';

// ✅ 不可变更新（Redux 风格）
const newState = {
  ...state,
  user: {
    ...state.user,
    name: 'Alice'
  }
};

// 或使用 immer（在可变语法下生成不可变结果）
import { produce } from 'immer';
const newState = produce(state, draft => {
  draft.user.name = 'Alice';
});
```

> 🔒 不可变性避免了“意外状态变更”，是响应式框架（React/Vue）高效 diff 的基础。

---

### 3. **函数是一等公民（First-Class Functions）**
- 函数可以像变量一样：
  - 赋值给变量
  - 作为参数传递
  - 作为函数返回值

```js
const greet = (name) => `Hello, ${name}`;
const callWith = (fn, arg) => fn(arg); // 高阶函数

callWith(greet, 'Alice'); // "Hello, Alice"
```

---

### 4. **高阶函数（Higher-Order Functions, HOF）**
- 接收函数作为参数，或返回函数的函数。
- 常见 HOF：`map`, `filter`, `reduce`, `compose`, `curry`

```js
const numbers = [1, 2, 3, 4];

// map: 转换
const doubled = numbers.map(x => x * 2);

// filter: 筛选
const evens = numbers.filter(x => x % 2 === 0);

// reduce: 聚合
const sum = numbers.reduce((acc, x) => acc + x, 0);
```

---

### 5. **函数组合（Composition）**
- 将多个小函数组合成复杂逻辑，替代嵌套调用。
- 数学表达：`f(g(x))` → `compose(f, g)(x)`

```js
const toUpper = str => str.toUpperCase();
const exclaim = str => str + '!';

// 手动组合
const shout = str => exclaim(toUpper(str));

// 通用 compose 函数
const compose = (...fns) => (value) => fns.reduceRight((acc, fn) => fn(acc), value);

const shout = compose(exclaim, toUpper);
shout('hello'); // "HELLO!"
```

> 🧩 组合优于继承，组合优于深层嵌套。

---

### 6. **柯里化（Currying）**
- 将接受多个参数的函数，转换为**每次只接受一个参数**的函数链。

```js
// 普通函数
const add = (a, b, c) => a + b + c;

// 柯里化
const curriedAdd = a => b => c => a + b + c;

curriedAdd(1)(2)(3); // 6

// 实用场景：创建配置化函数
const multiply = a => b => a * b;
const double = multiply(2);
double(5); // 10
```

---

## 二、函数式编程在前端的典型应用

### 1. **React 函数式组件 + Hooks**
- 组件是纯函数（输入 props → 输出 JSX）
- Hooks（如 `useMemo`, `useCallback`）用于优化和保持纯度

```jsx
const UserProfile = ({ user }) => {
  const displayName = useMemo(() => 
    user.firstName + ' ' + user.lastName, 
    [user]
  );
  return <div>{displayName}</div>;
};
```

### 2. **状态管理（Redux）**
- Reducer 必须是纯函数
- 状态不可变更新

```js
const userReducer = (state = initialState, action) => {
  switch (action.type) {
    case 'SET_NAME':
      return { ...state, name: action.payload }; // 返回新对象
    default:
      return state;
  }
};
```

### 3. **数据流处理**
- 使用 `map/filter/reduce` 替代 for 循环
- 链式调用清晰表达意图

```js
// 获取活跃用户的邮箱列表
const activeEmails = users
  .filter(user => user.isActive)
  .map(user => user.email);
```

### 4. **工具库支持**
- **Lodash/fp**：提供函数式版本的工具函数
- **Ramda**：专为 FP 设计的 JS 库（自动柯里化、数据最后参数等）
- **RxJS**：响应式编程 + 函数式操作符（map, filter, switchMap...）

---

## 三、函数式编程的优势与挑战

### ✅ 优势
| 优势 | 说明 |
|------|------|
| **可测试性** | 纯函数无需 mock 依赖，单元测试简单 |
| **可维护性** | 逻辑解耦，函数职责单一 |
| **并发安全** | 无共享状态，天然避免竞态条件 |
| **推理简单** | 无副作用，代码行为可预测 |

### ⚠️ 挑战
- 学习曲线较陡（尤其组合、monad 等高级概念）
- 过度抽象可能导致可读性下降
- JavaScript 原生不强制不可变性（需靠规范或库）

> 💡 **建议**：在前端中**适度使用 FP**，不必追求“纯函数式”，而是吸收其核心思想：**纯函数、不可变、组合**。

---

## 四、实践建议（前端开发者）

1. **优先使用 `map/filter/reduce` 而非 for 循环**
2. **避免直接修改 props 或 state**
3. **将业务逻辑抽离为纯函数**（便于测试）
4. **使用 TypeScript 增强函数签名清晰度**
5. **用 `useCallback`/`useMemo` 保持引用稳定（React）**
6. **状态更新始终返回新对象/数组**

---

## 五、一句话总结

> 函数式编程不是“用函数写代码”，而是**用数学函数的思维方式构建无副作用、可组合、可推理的程序**。

在前端工程化、组件化、状态复杂的今天，FP 提供了一套强大的工具和哲学，帮助我们写出更**健壮、简洁、可维护**的代码。

如需深入，可学习：
- 《Mostly Adequate Guide to FP》（免费在线书）
- Ramda 文档
- Redux 官方教程中的“不可变更新”章节

是否需要我结合具体前端场景（如表单处理、API 调用、状态同步）给出 FP 实战示例？
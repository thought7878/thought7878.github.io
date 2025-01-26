# 组件化

React，⽤组件创建⽤户界⾯。

因此在我们使⽤ React 构建⽤户界⾯时，⾸先要把它分解成⼀个个的组件。然后，把这些组件组合在⼀起，使⽤数据流来进⾏通信。

# 状态 state

React UI = fn(state)

state 是变量，⼀般情况下，state 改变之后，组件会更新。

当你有⼀个值：

- 这个值变化之后，⻚⾯需要随之更新，那么这个变量就可以定义成 state，⽐
  如 useState、useReducer、useContext、第三⽅状态管理库。

- 如果你只是需要记忆这个值，但是这个值的改变之后，⻚⾯不需要随之更新，
  那么可以使⽤ useRef 定义。

- 如果你不需要记忆，那么就是个普通变量。如果你需要缓存这个值，那么可以
  使⽤ useMemo 或者 useCallback。

注意：使⽤ useState 的时候，如果 state 不发⽣变化，组件不会更新。但是 useReducer 反之。

# 单向数据流

**单向数据流**，即对不可变数据状态使⽤*单向变换*。

React 的基础模型就是单向数据流，我们在修改 state 的时候，不会像 Vue 那样直接
修改，⽽是通过 setState 函数进⾏修改。

React 的传统状态管理库 Redux 亦是遵循如此模式，Redux 使⽤ reducer 作为数据
修改规则，⽽ reducer 是纯函数，不能直接修改 state，只能返回新的 state。

如下⼀个 React input 的例⼦：

```jsx
import { useState } from "react";
export default function Input() {
const [input, setInput] = useState("");
return <input value={input} onChange={(e) => setInput(e.target.value} />
```

但是，这个数据不可变性亦是被很多初学者吐槽，觉得学习成本⽐较⾼~因此现在⼀些周边库也⽀持在 React 中实现直接修改数据，⽐如 Immer。

# 参考

[](https://www.bilibili.com/video/BV1SDm2Y3ETD?spm_id_from=333.788.player.switch&vd_source=22af953ea4c09540ad1966711a2d53f0&p=4)

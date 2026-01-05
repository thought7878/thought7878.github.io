

本视频讲述了**React中状态与props的核心区别**，包括*状态作为变量可触发组件更新、props作为常量不可在子组件中直接修改*，并通过官网示例说明父子组件通信及状态提升的实现方式。

![[_posts/react/总结/核心概念、原理、源码/源码/教程/React18底层源码深入剖析/第2章 学前技术储备：React核心知识点讲解/media/4fb180db59b9044b4d7cf599b46610ff_MD5.webp]]

### 核心概念对比 
[00:15](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=15)

- 状态（data/state）
    - 状态是一个*变量*。
    - *状态值改变后，组件随之更新*。
    - 可理解为组件的“记忆”，即在上一次基础上进行更新。
    - 常使用 `useState`、`useReducer` 或类组件中的 `state` 定义。
- 属性（props）
    - props 是*父组件传给子组件的数据、信息*。
    - *在子组件中是一个常量，不可直接修改*。
    - *用于父子组件通信*。

- 变量 vs 常量
    - state 是变量，可以修改。
    - props 是常量，在子组件中不能被修改。
- 修改影响
    - state 修改会触发组件更新。
    - props 不允许在接收它的组件中被修改，但可通过父组件修改后再传递。

### 状态提升示例
[02:07](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=127)

- 问题背景
    - 子组件接收到一个 `is_active` 的 props 属性值。
    - 若希望动态修改该值，不能在子组件中直接修改。
- 解决方案：状态提升
    - 将状态定义在父组件中。
    - 使用 `useState` 创建状态变量并传给子组件。
    - 当需要修改时，在父组件中修改状态，从而触发子组件更新。
- 示例结构
    - 父组件：Operation
    - 子组件：Panel
    - 传递的属性：`title`、`is_active`
- 代码逻辑说明
    - 父组件定义状态：
        ```jsx
        const [activeIndex, setActiveIndex] = useState(0);
        ```
    - 传递状态到子组件：
        ```jsx
        <Panel title="Example" is_active={activeIndex === 0} />
        ```
    - 子组件使用 props 接收并展示：
        ```jsx
        function Panel({ title, is_active }) {
          return (
            <div>
              <h1>{title}</h1>
              {is_active && <p>Active</p>}
            </div>
          );
        }
        ```

### 关于defaultProps的补充说明 
[05:05](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=305)

- defaultProps 是一种设置默认属性的方式。
- 若父组件未传递某个属性，则使用默认值。
- 若父组件传递了属性，则以父组件传入为准。
- 注意：defaultProps 并不是修改 props 的方式，仅是提供默认值。

### 总结回顾 
[05:38](https://b.quark.cn/apps/5AZ7aRopS/routes/quark-video-ai-summary/pc?debug=0&fid=ba7f518fec334a9cb194637e1574ea0f#?seek_t=338)

- state 是组件内部的状态变量，可修改并触发更新。
- props 是从父组件传来的信息，在子组件中为只读常量。
- 修改 props 需要回到其源头（父组件）进行。
- 状态提升是一种解决跨层级通信的方式，结合了 state 和 props 的使用。
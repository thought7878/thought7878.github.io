> 为什么使用 Zustand？Zustand 是什么？怎么用 Zustand？

### 为什么使用 Zustand？

#### 1. **轻量级与简洁性**

- **极简 API**：Zustand 的核心 API 仅需一个 `create` 函数即可完成状态定义，无需 Redux 的 `reducer`、`action` 等复杂模板代码。
- **体积小**：仅约 1KB 大小，几乎无性能负担。

#### 2. **高性能与渲染优化**

- **按需更新**：组件仅订阅需要的状态字段，避免 Context API 因全局状态变化导致的不必要渲染。
- **自动浅层比较**：状态更新时自动对比新旧值，减少无效重渲染。

#### 3. **灵活性**

- **脱离组件层级**：状态独立于 React 组件树，跨组件共享无需层层传递 props。
- **支持异步与副作用**：直接在 store 中处理异步逻辑（如 API 请求），无需中间件。

#### 4. **TypeScript 友好**

- 天生支持 TypeScript，类型推断完善，开发体验流畅。

---

### Zustand 是什么？

**Zustand** 是 React 生态中一个*轻量级、高性能的状态管理库*，由 Poimandres 团队开发。*它基于 Flux 架构思想*，但通过简化的 API 和 Hooks 集成，让开发者能快速管理全局或局部状态，*尤其适合中小型应用或复杂场景的局部状态共享*。

---

### 怎么用 Zustand？

#### 基础使用示例

1. **安装**

   ```bash
   npm install zustand
   ```

2. **定义 Store**  
   使用 `create` 函数创建一个 store，包含状态和更新方法：

   ```tsx
   import { create } from "zustand";

   // 定义类型（TypeScript）
   interface CounterStore {
     count: number;
     increment: () => void;
     decrement: () => void;
   }

   // 创建 store
   const useCounterStore = create<CounterStore>((set) => ({
     count: 0,
     increment: () => set((state) => ({ count: state.count + 1 })),
     decrement: () => set((state) => ({ count: state.count - 1 })),
   }));
   ```

3. **在组件中使用**  
   通过 `useStore` Hook *按需订阅状态*：

   ```tsx
   function Counter() {
     const { count, increment, decrement } = useCounterStore();

     return (
       <div>
         <button onClick={decrement}>-</button>
         <span>{count}</span>
         <button onClick={increment}>+</button>
       </div>
     );
   }
   ```

#### 高级功能

- **状态持久化**：结合插件（如 `persist`）*实现本地存储*：

  ```tsx
  import { persist } from "zustand/middleware";

  const useStore = create(
    persist(
      (set) => ({
        data: {},
        setData: (newData) => set({ data: newData }),
      }),
      { name: "app-storage" } // 存储到 localStorage
    )
  );
  ```

- **中间件**：集成 `immer`（不可变数据）、`devtools`（Redux 调试工具支持）等：

  ```tsx
  import { immer } from "zustand/middleware/immer";

  const useStore = create(
    immer((set) => ({
      list: [],
      addItem: (item) =>
        set((state) => {
          state.list.push(item);
        }),
    }))
  );
  ```

- **跨组件选择性订阅**：*避免全局状态变化触发无关渲染*：
  ```tsx
  // 只订阅 count 字段
  const count = useCounterStore((state) => state.count);
  ```

---

### 对比其他状态管理工具

| 工具          | 优点              | 缺点            |
| ----------- | --------------- | ------------- |
| **Redux**   | 严格的架构，强大的中间件生态  | 样板代码多，学习成本高   |
| **Context** | React 原生，简单场景适用 | 性能差，不适合高频更新   |
| **Recoil**  | 原子化状态管理，灵活      | 体积较大，API 较复杂  |
| **Zustand** | 轻量、高性能、API 简洁   | 社区生态稍弱于 Redux |

---

### 总结

- **何时使用 Zustand**：

  - 需要轻量级全局状态管理。
  - 追求高性能和简洁代码。
  - 中小型项目或局部复杂状态共享。

- **核心优势**：
  - 减少模板代码，提升开发效率。
  - 按需更新，性能优化到位。
  - 灵活适应多种场景需求。

通过 Zustand，开发者可以更专注于业务逻辑，而非状态管理框架的复杂性。

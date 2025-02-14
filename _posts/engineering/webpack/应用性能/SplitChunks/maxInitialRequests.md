`maxInitialRequests` 是 Webpack 中的一个配置选项，属于 `SplitChunksPlugin` 的一部分。它的作用是限制初始加载（同步模块）时并行请求的最大数量，从而优化页面的初始加载性能。

---

### **1. 基本概念**
- **定义**：  
  `maxInitialRequests` 指的是在初始加载时，Webpack 允许的最大并行请求数量。
- **默认值**：  
  默认值为 `30`（Webpack 5 中）。
- **适用场景**：  
  当项目中有大量同步模块时，过多的并行请求可能会导致网络拥塞或降低页面加载速度。通过设置 `maxInitialRequests`，可以限制并行请求数量，优化资源加载。

---

### **2. 配置位置**
`maxInitialRequests` 是 `SplitChunksPlugin` 的一个选项，通常在 Webpack 配置文件中通过 `optimization.splitChunks` 进行设置。

```javascript
module.exports = {
  optimization: {
    splitChunks: {
      chunks: 'all', // 对同步和异步模块都进行代码分割
      maxInitialRequests: 5, // 限制初始加载时的最大并行请求数量
    },
  },
};
```

---

### **3. 工作原理**
- **初始加载模块**：  
  在 Webpack 中，初始加载模块是指在页面加载时立即需要的同步模块。这些模块会被打包成多个 Chunk，并通过 HTTP 请求加载。
- **限制请求数量**：  
  如果初始加载模块的数量较多，Webpack 会根据 `maxInitialRequests` 的值，将多个模块合并到同一个 Chunk 中，以减少并行请求数量。

---

### **4. 示例**
假设项目中有以下同步模块：

```javascript
// 同步导入模块
import './moduleA';
import './moduleB';
import './moduleC';
import './moduleD';
import './moduleE';
```

如果 `maxInitialRequests` 设置为 `3`，Webpack 会尝试将这些模块合并为最多 3 个 Chunk，而不是为每个模块生成单独的文件。

---

### **5. 与 `maxAsyncRequests` 的区别**
- **`maxInitialRequests`**：  
  限制初始加载（同步模块）时的最大并行请求数量。
- **`maxAsyncRequests`**：  
  限制异步加载模块时的最大并行请求数量。

两者的作用类似，但分别针对同步模块和异步模块。

---

### **6. 使用建议**
- **小型项目**：  
  小型项目中同步模块较少，通常不需要调整 `maxInitialRequests`。
- **大型项目**：  
  大型项目中同步模块较多时，可以通过调整 `maxInitialRequests` 来优化加载性能。例如：
  - 减少并行请求数量，避免网络拥塞。
  - 合并小模块，减少 HTTP 请求开销。
- **合理取值**：  
  根据项目的实际情况设置合理的值，通常建议在 `5-10` 之间。

---

### **7. 注意事项**
- **平衡点**：  
  设置过低可能导致单个 Chunk 文件过大，影响加载速度；设置过高可能增加并行请求数量，导致网络性能下降。
- **结合其他优化**：  
  `maxInitialRequests` 应与其他优化选项（如 `minSize`、`maxSize` 等）配合使用，以达到最佳效果。

---

### **总结**
`maxInitialRequests` 是 Webpack 中用于限制初始加载时并行请求数量的重要配置选项。通过合理设置该值，可以优化资源加载性能，减少网络拥塞，提升用户体验。

学习前端的 ES Module（ECMAScript 模块）是掌握现代 JavaScript 开发的重要一步。

### 一、什么是 ES Module？
- **模块化**：将代码拆分成独立的功能模块，避免全局污染。
- **静态加载**：模块依赖在代码编译阶段确定（与 CommonJS 的动态加载不同）。
- **浏览器支持**：现代浏览器均支持 `<script type="module">`。

---

### 二、基础用法
#### 1. 在 HTML 中启用 ES Module
```html
<!-- 注意：必须使用 .js 扩展名，且通过服务器访问（不能本地 file://） -->
<script type="module" src="main.js"></script>
```

#### 2. 导出（Export）
- **命名导出**：一个模块*可导出多个值*。
  ```js
  // math.js
  export const add = (a, b) => a + b;
  export const PI = 3.1415;
  ```
- **默认导出**：一个模块*只能有一个默认导出*。
  ```js
  // utils.js
  const sayHello = () => console.log("Hello!");
  export default sayHello;
  ```

#### 3. 导入（Import）
- **导入命名导出**：使用 `{}` 解构。
  ```js
  // main.js
  import { add, PI } from './math.js';
  console.log(add(2, 3)); // 5
  ```
- **导入默认导出**：无需 `{}`。
  ```js
  import sayHello from './utils.js';
  sayHello(); // "Hello!"
  ```
- **混合导入**：
  ```js
  import { add, PI }, sayHello from './combined.js';
  ```

#### 4. 其他导入方式
- **重命名**：*解决命名冲突*。
  ```js
  import { add as addFunction } from './math.js';
  ```
- **整体导入**：
  ```js
  import * as math from './math.js';
  console.log(math.add(1, 2));
  ```

---

### 三、动态导入（按需加载）
使用 `import()` **函数**（返回 Promise）：
```js
// 在需要时加载模块
button.addEventListener('click', async () => {
  const module = await import('./dynamic-module.js');
  module.doSomething();
});
```

参考 [[Import()]]

---

### 四、注意事项
1. **文件路径**：必须写完整路径（如 `./file.js`），不能省略扩展名。
2. **严格模式**：*模块默认在严格模式下运行*（无需写 `"use strict"`）。
3. **跨域限制**：通过 `http://` 或 `https://` 协议访问，本地文件需用服务器启动。
4. **导出的是引用**：模块内的变量是“动态绑定”，导入方获取的是**最新值（而非拷贝）**。
5. **循环依赖**：需谨慎设计，确保模块加载顺序不会导致未定义的值被访问。

>参考：[[导出的是引用]]、[[循环依赖]]、[[循环依赖]]

---

### 五、与 CommonJS 的区别
| 特性                | ES Module                      | CommonJS（Node.js）           |
|---------------------|--------------------------------|-------------------------------|
| 加载方式            | 静态（编译时）                | 动态（运行时）                |
| 导入语法            | `import`                      | `require()`                   |
| 导出语法            | `export` / `export default`   | `module.exports` / `exports`  |
| 浏览器支持          | 原生支持                      | 需构建工具转换（如 Webpack）  |
| 模块顶层的 `this`   | `undefined`                   | 指向 `module.exports`         |

---

### 六、扩展学习
- **构建工具**：了解 Webpack/Rollup 如何打包 ES Module。
- **Node.js 支持**：Node.js 从 v14+ 开始支持 ES Module（需在 `package.json` 设置 `"type": "module"`）。
- **模块解析**：学习 `import.meta.url` 和裸模块名称（如 `import React from 'react'`）的处理。

在前端工程化中，**JSX 编译**是将类 XML 语法转换为标准 JavaScript 函数调用的关键步骤。需要明确一个核心认知：**JSX 不是 React 专属语法，而是一种通用语法扩展；编译工具只负责语法转换，运行时行为由目标框架决定。**

下面从编译本质、主流工具、运行时机制、工程集成到现代演进，进行系统化详解。

---
## 一、核心概念：JSX 编译的本质

浏览器无法直接执行 JSX，编译器会将其转换为函数调用。以 `<div className="app">Hello</div>` 为例：

| 模式 | 输出代码 | 特点 |
|------|----------|------|
| **Classic（经典）** | `React.createElement('div', {className: 'app'}, 'Hello')` | 需手动 `import React`，Tree-shaking 差，React 16 及以下默认 |
| **Automatic（自动）** | `import {jsx as _jsx} from 'react/jsx-runtime'; _jsx('div', {className: 'app', children: 'Hello'})` | 自动注入运行时，无需 import React，支持更好 Tree-shaking，React 17+ 标准 |

> 💡 **关键结论**：现代项目应统一使用 `Automatic` 模式。编译工具仅做语法转换，不介入 Virtual DOM  diff、响应式更新等运行时逻辑。

---
## 二、主流 JSX 编译工具全景对比

| 工具                                | 底层语言       | 编译速度       | 配置复杂度 | 运行时模式支持                       | 典型定位                               |
| --------------------------------- | ---------- | ---------- | ----- | ----------------------------- | ---------------------------------- |
| **Babel** (`@babel/preset-react`) | JavaScript | ⭐⭐         | 中     | Classic / Automatic           | 行业标准、插件生态最全、企业定制首选                 |
| **SWC** (`@swc/core`)             | Rust       | ⭐⭐⭐⭐⭐      | 低     | Classic / Automatic           | Next.js / Turbopack 默认、Babel 高性能替代 |
| **esbuild**                       | Go         | ⭐⭐⭐⭐⭐+     | 极低    | Classic / Automatic           | Vite 开发服务器核心、极速构建                  |
| **TypeScript** (`tsc`)            | TS/JS      | ⭐⭐⭐        | 中     | Classic / Automatic (TS 4.1+) | TS 项目零依赖方案、轻量转译                    |
| **Sucrase**                       | JavaScript | ⭐⭐⭐⭐       | 低     | Classic                       | 开发环境极速转译、不推荐生产                     |
| **框架专用编译器**                       | 各异         | ⭐⭐⭐⭐~⭐⭐⭐⭐⭐ | 低     | 自定义运行时                        | Vue / Solid / Preact / Qwik 等深度优化  |

---
## 三、核心工具深度解析与配置

### 1. Babel (`@babel/preset-react`)
- **机制**：解析 JSX AST → 按配置生成 `createElement` 或 `jsx-runtime` 调用 → 走 Babel 插件链。
- **配置示例**：
  ```json
  // babel.config.json
  {
    "presets": [
      ["@babel/preset-react", {
        "runtime": "automatic",
        "importSource": "@emotion/react" // 自定义运行时（如 CSS-in-JS）
      }]
    ]
  }
  ```
- **优点**：支持所有 React 版本、可自定义 `pragma`、插件生态无敌（如 `@emotion/babel-plugin`、`babel-plugin-react-compiler`）。
- **缺点**：纯 JS 实现速度慢；配置链路长。
- **场景**：企业级项目、强定制需求、老项目维护、需 AST 级别干预。

### 2. SWC (`@swc/core`)
- **机制**：Rust 多线程解析 → 直接输出 JSX 运行时调用 → 可衔接语法降级。
- **配置示例** (`.swcrc`)：
  ```json
  {
    "jsc": {
      "parser": { "syntax": "ecmascript", "jsx": true },
      "transform": {
        "react": {
          "runtime": "automatic",
          "importSource": "react"
        }
      }
    }
  }
  ```
- **优点**：速度比 Babel 快 10~20 倍；Next.js / Vite / Turbopack 默认集成；配置简洁。
- **缺点**：自定义 AST 插件生态仍在补齐；极端边缘语法支持略逊 Babel。
- **场景**：现代 React 项目、追求构建性能、框架默认技术栈。

### 3. esbuild
- **机制**：单遍词法分析 → 剥离 JSX → 输出运行时调用，专注极致速度。
- **配置示例**：
  ```js
  require('esbuild').build({
    entryPoints: ['src/app.jsx'],
    jsx: 'automatic',
    jsxImportSource: 'react',
    bundle: true,
    outfile: 'dist/app.js'
  })
  ```
- **优点**：毫秒级编译；API 极简；内置打包/压缩/SourceMap。
- **缺点**：不支持复杂 AST 插件；仅做基础转换；生产构建通常配合 Rollup/Vite。
- **场景**：Vite 开发服务器、现代项目热更新、对速度极度敏感的场景。

### 4. TypeScript (`tsc`)
- **机制**：TS 编译器原生支持 JSX 转换，无需额外依赖。
- **配置示例** (`tsconfig.json`)：
  ```json
  {
    "compilerOptions": {
      "jsx": "react-jsx",        // automatic 模式（TS 4.1+）
      "jsxImportSource": "react" // 自定义运行时源
    }
  }
  ```
  > 旧版配置：`"jsx": "react"`（Classic） / `"jsx": "preserve"`（交给 Babel/SWC 处理）
- **优点**：零额外依赖；与 TS 类型检查无缝集成；输出稳定。
- **缺点**：编译速度中等；不处理打包与压缩。
- **场景**：纯 TS 项目、不想引入额外编译链的轻量级转换。

### 5. 非 React 框架的 JSX 编译
JSX 是通用语法，不同框架通过 `pragma` 或 `importSource` 指向自有运行时：
| 框架 | 编译工具 | 运行时函数 | 配置关键 |
|------|----------|------------|----------|
| **Preact** | Babel/SWC/esbuild | `h()` / `jsx()` | `jsxImportSource: 'preact'` |
| **Solid.js** | `babel-preset-solid` | 编译为细粒度 DOM 操作（非 VDOM） | 框架专属 preset，输出直接 DOM 指令 |
| **Vue 3** | `@vue/compiler-sfc` / `@vitejs/plugin-vue-jsx` | `createVNode()` / `resolveComponent()` | 官方插件处理，支持 Vue 响应式语义 |
| **Qwik** | `@builder.io/qwik` | `$()` 延迟加载标记 | 编译时拆分 chunk，实现可恢复性 |

---
## 四、关键机制：Classic vs Automatic Runtime 深度对比

| 维度 | Classic (`react`) | Automatic (`react-jsx`) |
|------|-------------------|--------------------------|
| **是否需要 `import React`** | ✅ 必须 | ❌ 自动注入 `react/jsx-runtime` |
| **Tree-shaking 效果** | 差（全量引入 React） | 优（仅引入使用的 jsx/jsxs 函数） |
| **Fragment `<>` 处理** | 转为 `React.Fragment` | 转为 `jsx-runtime` 内置片段，体积更小 |
| **React 版本要求** | 任意 | ≥ 17.0.0（或 `@react-jsx/runtime` polyfill） |
| **包体积影响** | 多 2~5KB gzip | 减少冗余，现代打包器优化更好 |
| **配置字段** | `runtime: 'classic'` / `jsx: 'react'` | `runtime: 'automatic'` / `jsx: 'react-jsx'` |

> ✅ **强制规范**：新项目统一 `automatic`；老项目迁移时注意 React 版本 ≥17，并移除文件顶部冗余的 `import React from 'react'`。

---
## 五、与主流构建工具的集成

| 构建工具 | 集成方式 | 备注 |
|----------|----------|------|
| **Webpack** | `babel-loader` / `swc-loader` / `esbuild-loader` + 对应 JSX 配置 | 推荐 `swc-loader`，性能提升 50%+ |
| **Vite** | 开发期 esbuild 自动处理；生产期 `@vitejs/plugin-react`（底层 Babel 或 SWC） | 默认 `automatic`，零配置开箱 |
| **Rollup** | `@rollup/plugin-babel` / `@rollup/plugin-swc` | 适合类库构建，配合 `jsxImportSource` |
| **Next.js** | 默认 SWC 编译 JSX，`next.config.js` 可切换 Babel | 12+ 版本全面转向 SWC |
| **Create React App** | 历史使用 Babel，已官方弃用 | 建议迁移 Vite / Next.js / Rsbuild |

---
## 六、常见问题与排查指南

| 问题现象 | 原因分析 | 解决方案 |
|----------|----------|----------|
| `React is not defined` | Classic 模式未 import React，或 Automatic 配置错误/React<17 | 检查 `runtime` 配置；升级 React；或顶部补 `import React` |
| `jsx-runtime not found` | 依赖未安装、打包器未解析 `react/jsx-runtime`、Monorepo 路径错乱 | `npm ls react` 确认版本；检查 alias/externals；pnpm 需 `shamefully-hoist` 或正确 peer |
| 自定义 pragma 不生效 | 同时配置了 `runtime: 'automatic'` 与 `pragma`（互斥） | Automatic 模式用 `importSource`；Classic 模式用 `pragma` |
| TSX 文件编译报错 | loader 未识别 `.tsx`，或 parser 未开 `tsx` | 确保 loader `test: /\.[jt]sx?$/`；SWC 开 `parser.tsx: true` |
| 生产包体积异常大 | 未开 Automatic 导致全量引入 React；或未 Tree-shake | 强制 `runtime: 'automatic'`；检查打包器 `sideEffects` 与 `module` 字段 |
| 非 React 框架 JSX 渲染异常 | `jsxImportSource` 指向错误，或运行时未适配 | Preact 配 `preact`，Solid 用官方 preset，Vue 用 `@vitejs/plugin-vue-jsx` |

**排查三板斧**：
```bash
# 1. 查看最终生效的 JSX 配置
npm pkg get babel / cat .swcrc / cat tsconfig.json

# 2. 检查 react/jsx-runtime 是否可解析
node -e "console.log(require.resolve('react/jsx-runtime'))"

# 3. 查看编译后源码（确认输出模式）
npx esbuild src/app.jsx --jsx=automatic --bundle | head -20
```

---
## 七、现代趋势与最佳实践

### 🔮 演进趋势
1. **Automatic Runtime 成为绝对标准**：React 17+ 已普及，React 19 进一步优化 `jsx-runtime` 内联与编译时静态分析。
2. **Rust/Go 编译器主导构建链**：SWC / esbuild 取代 Babel 成为默认 JSX 转换器，Babel 退居“插件生态层”。
3. **框架定制编译时优化**：
   - Solid / Qwik：JSX 编译为直接 DOM 操作或延迟加载标记，跳过 VDOM。
   - React Compiler (React 19)：在 JSX 转换后插入自动记忆化（`useMemo`/`useCallback` 编译时生成）。
4. **JSX 与模板语法融合**：Vue / Svelte / Angular 坚持模板优先，但提供 JSX 插件满足复杂逻辑场景；React 生态探索 `react-server-components` 下的编译时边界。

### ✅ 工程化最佳实践
1. **统一 Automatic 模式**：所有工具链配置 `runtime: 'automatic'` 或 `jsx: 'react-jsx'`。
2. **明确 `jsxImportSource`**：非 React 框架必须指定，避免运行时错乱。
3. **开发用 esbuild/SWC，生产用 SWC/Babel**：兼顾 HMR 速度与生产稳定性。
4. **TS 项目开启 `jsx: 'react-jsx'` + `isolatedModules: true`**：确保兼容快速编译器。
5. **移除冗余 `import React`**：配合 ESLint 规则 `react/jsx-uses-react` / `react/react-in-jsx-scope` 设为 `off`。
6. **类库发布注意 `peerDependencies`**：声明 `react: '>=17.0.0'`，避免 `jsx-runtime` 解析失败。

---
## 八、选型决策树与总结

```
是否需要自定义 AST 插件或强兼容老版本？
 ├─ 是 → Babel + @babel/preset-react (runtime: automatic)
 └─ 否 → 
      ├─ 现代 React 项目 / Next.js / Vite → SWC 或 esbuild（框架默认）
      ├─ 纯 TS 项目 / 轻量转换 → tsc (jsx: 'react-jsx')
      ├─ 非 React 框架 → 使用官方 JSX 插件 + 配置 jsxImportSource
      └─ 追求极致开发体验 → Vite (esbuild dev + SWC prod)
```

| 工具 | 一句话定位 | 核心优势 | 注意事项 |
|------|------------|----------|----------|
| Babel | 生态与定制王者 | 插件无限、兼容所有版本 | 速度慢、配置重 |
| SWC | 现代高性能标准 | 极速、框架默认、兼容 Babel 配置 | 自定义 AST 插件仍在补齐 |
| esbuild | 速度至上的转换引擎 | 毫秒级、API 极简 | 仅基础转换，生产常配合 Rollup |
| tsc | TS 零依赖方案 | 无需额外工具、类型无缝 | 不处理打包，速度中等 |
| 框架专用编译器 | 运行时深度优化 | 跳过 VDOM、编译时响应式 | 仅限特定框架，不可跨用 |

> 📌 **现代建议**：  
> **JSX 编译已高度标准化。优先使用框架默认工具链，强制 Automatic Runtime，将工程精力从“编译配置”转向“运行时优化与架构设计”。** 除非需要自定义 AST 转换或维护 legacy 项目，否则不再推荐手动编排 Babel 链路。

如需具体场景配置（如：Webpack + SWC + React 19 完整模板、Vite 自定义 jsxImportSource 实战、Solid/Vue JSX 编译差异、React Compiler 与 JSX 转换的协同机制等），可提供技术栈与目标，我将给出可落地的工程配置与代码示例。
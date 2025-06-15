该项目使用 **TypeScript** 进行类型检查和编译，其 TypeScript 环境是通过以下方式搭建的：

---

## ✅ 1. 安装依赖

在 [package.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/package.json) 中定义了 TypeScript 的核心依赖：

```json
"devDependencies": {
  "typescript": "~5.8.3",
  "ts-node": "^10.9.2",
  "tslib": "^2.8.1"
}
```

- [typescript](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/node_modules/typescript): TypeScript 编译器。
- [ts-node](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/node_modules/ts-node): 支持直接运行 `.ts` 文件（用于开发环境）。
- [tslib](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/node_modules/tslib): 辅助函数库，用于减少重复代码。

---

## ✅ 2. 配置文件：[tsconfig.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig.json)

项目中提供了多个 [tsconfig.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig.json) 文件以适配不同构建目标，如 ESM、CJS 和文档网站等。以下是主配置文件的典型结构（从检索结果推测）：

### 示例 [tsconfig.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig.json)：
```json
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "jsx": "react-jsx",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "moduleResolution": "node",
    "types": ["jest", "react", "react-dom"],
    "lib": ["es2021", "dom"],
    "moduleDetection": "auto",
    "allowImportingTsExtensions": true,
    "noEmit": true
  },
  "include": ["src"]
}
```

说明：
- 使用 `ESNext` 作为编译目标。
- 启用严格模式 (`strict: true`) 提高类型安全性。
- 设置 `outDir` 和 `rootDir` 指定输出目录和源码目录。
- 开启类型声明生成 (`declaration: true`)。
- 支持 JSX（React）语法。
- 包含 Jest、React 类型定义以便在测试和组件中使用。

此外还有：
- `tsconfig-cjs.json`：用于 CommonJS 构建。
- `tsconfig-esm.json`：用于 ES Module 构建。
- `tsconfig-docs.json`：用于文档网站的 TypeScript 配置。

---

## ✅ 3. 编译脚本命令（`package.json`）

项目提供了多种 TypeScript 编译方式，定义在 `scripts` 字段中：

```json
"build:cjs": "tsc --project tsconfig-cjs.json && echo '{ \"type\": \"commonjs\" }' > dist/cjs/package.json",
"build:esm": "tsc --project tsconfig-esm.json",
"typecheck": "tsc --project ./tsconfig.json --noEmit",
"typecheck-watch": "tsc --project ./tsconfig.json --noEmit --watch"
```

| 脚本名 | 用途 |
|--------|------|
| `build:cjs` | 使用 `tsconfig-cjs.json` 构建 CJS 格式的包 |
| `build:esm` | 使用 `tsconfig-esm.json` 构建 ESM 格式的包 |
| `typecheck` | 仅做类型检查，不生成文件 |
| `typecheck-watch` | 在 watch 模式下进行类型检查 |

---

## ✅ 4. 与构建工具集成

### 构建工具支持

- **Vite**: 用于开发示例应用（`examples-app`），支持 TypeScript 即时编译。
- **Docusaurus**: 文档网站也基于 TypeScript 配置。
- **Jest + SWC**: 测试使用 [@swc/jest](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/node_modules/@swc/jest) 替代默认 Babel 编译器，提升速度。

---

## ✅ 5. 类型定义支持

### 类型定义文件
- 所有导出模块都带有类型定义文件（`.d.ts`）。
- 主入口文件定义了类型路径：
```json
"types": "./dist/cjs/index.d.ts",
```
- 支持 CSS Modules 类型定义（[style.module.css.d.ts](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/src/style.module.css.d.ts)）。

---

## ✅ 6. 与 ESLint / Prettier 集成

项目中还集成了 TypeScript 的 Lint 工具链：
- [@typescript-eslint/eslint-plugin](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/node_modules/@typescript-eslint/eslint-plugin): 提供 TypeScript 特定规则。
- [@typescript-eslint/parser](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/node_modules/@typescript-eslint/parser): 将 TypeScript 代码解析为 AST。

相关依赖项：
```json
"@typescript-eslint/eslint-plugin": "^8.29.1",
"@typescript-eslint/parser": "^8.29.1",
```

ESLint 配置中引用了 TypeScript 解析器和插件，确保类型安全性和代码风格统一。

---

## ✅ 7. IDE 支持建议

推荐使用 VS Code，并打开 `.code-workspace` 文件以获得最佳开发体验：
- 自动启用 TypeScript Watch Mode。
- 支持多项目管理（如文档网站 + 示例应用 + 主库）。
- 集成 Docusaurus 和 Vite 开发服务器。

---

## ✅ 8. 开发流程建议

根据 [CONTRIBUTING.md](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/CONTRIBUTING.md) 的指引，开发者可以执行以下命令来快速启动开发：

```bash
pnpm install
pnpm tsc --project tsconfig-esm.json --watch
```

或使用预设的 VSCode 工作区，自动启动：
- TypeScript 编译器（watch 模式）
- Docusaurus 文档网站
- Vite 示例应用

---

## ✅ 9. 示例应用中的 TypeScript 配置（[examples-app/tsconfig.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/examples-app/tsconfig.json)）

```json
{
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "lib": ["DOM", "DOM.Iterable", "ESNext"],
    "allowJs": false,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "strict": true,
    "module": "ESNext",
    "moduleResolution": "Node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "types": ["vite-plugin-react-swc/client", "@testing-library/jest-dom"]
  },
  "include": ["./src/**/*"]
}
```

这个配置专为 `examples-app` 设计，启用 React JSX 并兼容 Vite + SWC。

---

## ✅ 10. TypeScript + React 支持

- 使用 React 19.x（从 package.json 推断）。
- 所有组件均使用 `.tsx` 扩展名。
- 类型定义完整，包括组件 props、hooks 返回值、事件处理函数等。
- 支持自定义钩子、泛型组件、联合类型等高级特性。

---

## ✅ 11. 类型发布

最终构建输出包含类型定义文件：
```json
"files": [
  "dist",
  "src",
  "jalali.d.ts",
  "locale.d.ts"
]
```

这样用户在使用 [react-day-picker](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/website/node_modules/react-day-picker) 时可以直接获得类型提示和自动补全。

---

## ✅ 总结

| 功能 | 实现方式 |
|------|----------|
| **TypeScript 版本** | `~5.8.3` |
| **TS 配置文件** | 多个配置文件支持 ESM/CJS/Docs |
| **编译脚本** | `tsc` 命令配合 [tsconfig-*](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig-cjs.json) 文件 |
| **类型定义** | 自动生成 `.d.ts` 文件 |
| **IDE 支持** | VSCode + `.code-workspace` |
| **构建工具集成** | Vite, Docusaurus, Jest, SWC |
| **Linter 支持** | ESLint + TypeScript 插件 |
| **React 支持** | 完整的 `.tsx` 组件 + 类型系统 |
| **类型发布** | NPM 包中包含 `.d.ts` 文件 |

整个 TypeScript 的搭建符合现代前端工程化标准，兼顾类型安全、开发效率与可维护性。
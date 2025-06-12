[tsconfig.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig.json) 是 TypeScript 项目的核心配置文件，用于定义项目的编译选项、路径映射、类型检查规则等内容。它告诉 TypeScript 编译器（`tsc`）如何处理项目中的 `.ts` 和 `.tsx` 文件。

---

### 📁 作用与功能

#### ✅ 1. 指定编译选项
```json
{
  // 继承自基础的 TypeScript 配置文件，以复用通用的配置
  "extends": "./tsconfig-base.json",
  "compilerOptions": {
    // 定义路径映射，用于简化导入语句
    "paths": {
      "@/test": ["./test"],
      "@/test/*": ["./test/*"],
      "react-day-picker": ["./src"],
      "react-day-picker/*": ["./src/*"]
    },
    // 不生成编译输出，主要用于类型检查和开发
    "noEmit": true,
    // 定义可用的类型声明文件，以支持相应的开发环境和库
    "types": ["node", "jest", "@testing-library/jest-dom", "@types/jest"]
  },
  // 包含在构建中的文件或目录，包括测试文件和示例
  "include": ["src", "test", "**/*.test.*", "examples"],
  // 排除在构建之外的文件或目录
  "exclude": ["website"]
}
```
- `noEmit`: 不生成编译后的 `.js` 文件（仅做类型检查）
- `types`: 指定全局类型定义（如 Node.js、Jest 等）
- `paths`: 设置模块导入的别名，简化代码中 import 路径

#### ✅ 2. **控制包含和排除的文件范围**
```json
{
  "include": ["src", "test", "**/*.test.*", "examples"],
  "exclude": ["website"]
}
```
- `include`: 包含哪些目录或文件进行类型检查
- `exclude`: 排除哪些目录（例如 `website` 目录不参与类型检查）

#### ✅ 3. **继承基础配置**
```json
{
  "extends": "./tsconfig-base.json"
}
```
- 通过 `extends` 继承通用配置（如 `tsconfig-base.json`），避免重复配置
- 可以根据不同环境（ESM/CJS/Docs）扩展不同子配置文件（如 `tsconfig-esm.json`, `tsconfig-cjs.json`）

#### ✅ 4. **支持 IDE 自动补全与类型提示**
- VSCode、WebStorm 等编辑器会读取此文件提供智能提示、错误检查等功能

#### ✅ 5. **支持多项目结构管理**
- 配合 `references` 字段可用于大型 monorepo 项目中引用多个子项目（虽然本项目未使用）

---

### 🛠️ **典型应用场景**

| 场景 | 功能 |
|------|------|
| 类型检查 | 在开发时运行 `pnpm run typecheck` 进行类型检查 |
| IDE 支持 | 提供自动补全、跳转定义等能力 |
| 构建流程 | 作为 [tsconfig-esm.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig-esm.json) / [tsconfig-cjs.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig-cjs.json) 的基类 |
| 单元测试 | 配置 Jest + TypeScript 测试环境所需的类型支持 |

---

### 🔗 **与其他配置文件的关系**

- **继承自：** [tsconfig-base.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig-base.json)（基础配置）
- **被扩展为：**
  - [tsconfig-esm.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig-esm.json)（用于 ESM 构建）
  - [tsconfig-cjs.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig-cjs.json)（用于 CJS 构建）
  - [tsconfig-docs.json](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/tsconfig-docs.json)（用于文档网站构建）

---

### 🧪 示例命令

```bash
# 类型检查
pnpm run typecheck

# 类型检查并监听变化
pnpm run typecheck-watch

# 合并与子配置一起使用
tsc --build --clean
```

---

### ✅ 总结

| 功能点 | 描述 |
|--------|------|
| **配置 TypeScript 行为** | 控制编译方式、输出路径、模块系统等 |
| **路径别名设置** | 使用 `@/test`、[react-day-picker](file:///Users/ll/Desktop/learn-notes/frontend/react/3rd/react-day-picker/website/node_modules/react-day-picker) 等路径简化导入 |
| **类型支持** | 包括 Jest、Node.js、DOM 类型等 |
| **集成工具链** | 与 IDE、CI/CD、构建工具（如 Vite、Jest）配合使用 |
| **继承机制** | 通过 `extends` 实现统一的基础配置 |

--- 

如果你需要了解具体字段含义或如何自定义，也可以查阅 [TypeScript 官方文档](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)。
`tsconfig.json` 是 TypeScript 项目的配置文件，它用于*指定 TypeScript 编译器（`tsc`）的编译选项、项目结构*，*帮助开发者更好地控制 TypeScript 代码的编译过程*。

通过合理配置 `tsconfig.json`，开发者可以根据项目的需求，**定制 TypeScript 代码的编译行为，确保编译出符合要求的 JavaScript 代码**。


---

## 基本结构
`tsconfig.json` 文件是一个 JSON 格式的文件，其基本结构如下：

```json
{
  // 1. 编译选项 (核心配置)
  "compilerOptions": { 
    /* 详细配置见下文 */
  },
  
  // 2. 文件范围控制
  // 包含的文件/目录（默认: ["**/*"]）
  "include": ["src/**/*.ts"],  // Glob模式匹配
  // 排除的文件/目录（默认:["node_modules","bower_components"]）
  "exclude": ["node_modules", "dist"],
  "files": ["core.ts", "app.ts"],  // 显式文件列表（优先级最高）
  
  // 3. 扩展配置
  "extends": "./configs/base",  // 继承其他配置文件
  
  // 4. 工程引用 (大型项目)
  "references": [
    { "path": "../shared" },
    { "path": "../utils", "prepend": true }
  ]
}
```

---

## 编译选项 (compilerOptions) 
这是 `tsconfig.json` 中最重要的部分，用于*指定 TypeScript 编译器的各种编译选项*。

### 类型检查

`strict`启用所有严格类型检查选项，包括 `noImplicitAny`、`noImplicitThis` 等，有助于**提高代码的健壮性**。

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

| 选项                             | 默认值                  | 说明                                       |
| ------------------------------ | -------------------- | ---------------------------------------- |
| **`strict`**                   | `false`              | 启用所有严格检查的元选项（*建议始终设为* `true`）            |
| `noImplicitAny`                | `strict` 开启时为 `true` | 禁止隐式 `any` 类型                            |
| `strictNullChecks`             | `strict` 开启时为 `true` | 强制空值检查（避免 `undefined is not a function`） |
| `strictFunctionTypes`          | `strict` 开启时为 `true` | 函数参数逆变检查                                 |
| `strictBindCallApply`          | `strict` 开启时为 `true` | 严格检查 `bind/call/apply` 参数                |
| `strictPropertyInitialization` | `strict` 开启时为 `true` | 强制类属性初始化                                 |
| `useUnknownInCatchVariables`   | `strict` 开启时为 `true` | 将 `catch` 变量转为 `unknown` 类型              |

### 模块系统配置


| 选项              | 默认值 | 说明                                                                                          |
| --------------- | --- | ------------------------------------------------------------------------------------------- |
| module          |     | 指定生成代码的模块系统，如 `commonjs`、`amd`、`esnext` 等。                                                  |
| esModuleInterop |     | *解决 CommonJS/ESM 互操作问题*，支持 CommonJS 和 ES 模块之间的互操作性，*解决导入导出的兼容性问题*。`"esModuleInterop": true` |
|                 |     |                                                                                             |
|                 |     |                                                                                             |


```json
{
  "module": "ES2020",               // 模块输出格式: CommonJS/ES6/ES2020...
  "moduleResolution": "NodeNext",   // 模块解析策略: Node/Classic/Node16/NodeNext
  "baseUrl": "./",                  // 非相对导入的基准路径
  "paths": {                        // 路径映射 (需配合打包工具)
    "@components/*": ["src/components/*"]
  },
  "resolveJsonModule": true,        // 允许导入 JSON
  "esModuleInterop": true,          // 解决 CommonJS/ESM 互操作问题
  "allowSyntheticDefaultImports": true  // 允许 `import React from 'react'`
}
```

### 输出控制

| 选项                | 说明                                                                                      |
| ----------------- | --------------------------------------------------------------------------------------- |
| **`target`**      | 输出 JS 版本 (ES3 到 ESNext)，指定*编译后的 JavaScript 版本*，如 `ES3`、`ES5`、`ES6` 等。                   |
| **`outDir`**      | 输出目录 (默认同源文件位置)，指定*编译后文件的输出目录*。`"outDir": "./dist"`                                     |
| `outFile`         | 合并输出为单文件 (仅 AMD/System 模块可用)                                                            |
| `rootDir`         | 源文件根目录 (自动计算，*建议显式声明*)，指定 TypeScript 文件的根目录，编译器会*根据这个目录来组织输出文件的结构*。`"rootDir": "./src"` |
| `sourceMap`       | 生成 `.map` 文件                                                                            |
| `inlineSourceMap` | 将 SourceMap 嵌入 JS 文件                                                                    |
| `declaration`     | 生成 `.d.ts` 类型声明                                                                         |
| `declarationMap`  | 为声明文件生成 SourceMap                                                                       |
| `noEmit`          | 只进行类型检查不输出文件                                                                            |

### 高级类型系统
```json
{
  "skipLibCheck": true,            // 跳过库声明文件检查
  "types": ["node", "lodash"],     // 仅包含指定的全局类型
  "typeRoots": ["./typings"],      // 自定义类型声明目录
  "allowUmdGlobalAccess": true,    // 允许从全局访问 UMD 模块
  "emitDecoratorMetadata": true,   // 为装饰器生成元数据 (需要 experimentalDecorators)
  "preserveConstEnums": true       // 保留 const enum 声明
}
```

### JS/JSX 支持

| 选项 | 说明 |
|------|------|
| **`jsx`** | `preserve` (保留 JSX)/`react` (编译为 React.createElement) |
| `jsxFactory` | 自定义 JSX 工厂函数 |
| `jsxFragmentFactory` | 自定义 JSX Fragment 组件 |
| `jsxImportSource` | JSX 运行时导入路径 (React 17+) |
| `allowJs` | 编译 JS 文件 |
| `checkJs` | 在 JS 文件中进行类型检查 |

---

## 文件处理规则（include、exclude、files）


### `include`

*指定需要编译的文件或目录*，支持使用 glob 模式匹配。默认情况下，如果没有指定 `include`，编译器会包含当前目录和子目录下的所有 `.ts`、`.tsx` 和 `.d.ts` 文件。

```json
{
  "include": ["src/**/*.ts"]
}
```

### `exclude`

*指定不需要编译的文件或目录*，同样支持使用 glob 模式匹配。默认情况下，`node_modules`、`bower_components`、`jspm_packages` 和 `<outDir>` 会被排除。

```json
{
  "exclude": ["node_modules", "dist"]
}
```

### `files`

指定需要编译的具体文件列表，与 `include` 和 `exclude` 不同，`files` 是*一个具体的文件路径数组*。

```json
{
  "files": ["src/main.ts", "src/utils.ts"]
}
```


### 优先级
```
files > include > exclude
```
1. 没有 `files/include` 时：编译目录下所有 `.ts` 文件
2. `exclude` 默认包含：`node_modules/bower_components/jspm_packages/<outDir>`

### Glob 模式示例
```json
{
  "include": [
    "src/**/*",            // 递归所有子目录
    "generated/*.d.ts"     // 特定类型文件
  ],
  "exclude": [
    "**/__tests__",        // 忽略测试目录
    "**/*.spec.ts"         // 忽略测试文件
  ]
}
```

---


## 继承配置

可以使用 `extends` 关键字**继承**另一个 `tsconfig.json` 文件的配置，这在多个项目需要**共享相同配置**时非常有用。

```json
{
  "extends": "./base-tsconfig.json",
  "compilerOptions": {
    // 可以覆盖或添加额外的配置
  }
}
```

---

## 工程引用 (Project References)
用于大型项目拆分：
```json
{
  "references": [
    { 
      "path": "../shared",  // 子项目路径
      "prepend": true       // 将依赖输出前置合并
    }
  ],
  "compilerOptions": {
    "composite": true,      // 启用项目编译
    "declaration": true,    // 必须生成声明文件
    "declarationMap": true  // 推荐开启
  }
}
```
**使用方式**：
```bash
tsc --build              # 增量构建所有依赖
tsc --build --clean      # 清理输出
```

---

## 最佳实践配置
### Node.js 项目推荐配置
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "sourceMap": true,
    "inlineSources": true,
    "experimentalDecorators": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### 前端框架 (React/Vue) 扩展配置
```json
{
  "compilerOptions": {
    "jsx": "react-jsx",        // React 17+ 自动导入
    "jsxImportSource": "preact", // 使用 Preact
    "types": ["vite/client"],   // Vite 环境类型
    "allowSyntheticDefaultImports": true,
    "isolatedModules": true     // 确保单文件编译安全
  }
}
```

---

## 高级技巧
### 环境特定配置
   ```bash
   tsc -p tsconfig.prod.json
   ```

### 类型检查加速
   ```json
   {
     "compilerOptions": {
       "incremental": true,       // 启用增量编译
       "tsBuildInfoFile": ".tsbuildinfo"  // 缓存文件位置
     }
   }
   ```

### 解决循环依赖
   ```json
   {
     "compilerOptions": {
       "preserveSymlinks": true  // 保留符号链接路径
     }
   }
   ```

### 自定义转换
   ```json
   {
     "plugins": [
       { "transform": "ts-transformer-keys/transformer" }  // 编译时提取 key
     ]
   }
   ```

---

## 常见问题解决方案
### `Cannot find module` 错误
   - 检查 `moduleResolution`
   - 确保 `types`/`typeRoots` 配置正确
   - 添加 `"type": "module"` 到 `package.json` (ESM 项目)

### 类型扩展技巧
   ```typescript
   // global.d.ts
   declare module '*.svg' {
     const content: string;
     export default content;
   }
   ```

### 构建性能优化
   ```bash
   tsc --build --verbose  # 查看详细构建过程
   tsc --noEmit           # 只做类型检查
   ```

> 通过 `tsc --showConfig` 可查看最终生效的完整配置（包含继承和默认值）
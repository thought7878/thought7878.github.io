# 完整配置

```json
{
  "name": "react-day-picker", // 定义包的名称
  "version": "9.7.0", // 定义包的版本
  "description": "Customizable Date Picker for React", // 包的描述
  "author": "Giampaolo Bellavite <io@gpbl.dev>", // 包的作者信息
  "homepage": "https://daypicker.dev", // 包的主页URL
  "packageManager": "pnpm@9.15.9", // 指定包管理器
  "engines": {
    "node": ">=18" // 要求Node.js的版本至少为18
  },
  "license": "MIT", // 包的许可证类型
  "repository": {
    "type": "git", // 仓库类型
    "url": "https://github.com/gpbl/react-day-picker" // 仓库URL
  },
  "bugs": {
    "url": "https://github.com/gpbl/react-day-picker/issues" // 提交问题的URL
  },

  "main": "./dist/cjs/index.js", // CommonJS的入口文件
  "types": "./dist/cjs/index.d.ts", // TypeScript类型定义文件
  "module": "./dist/esm/index.js", // ES模块的入口文件
  "style": "./src/style.css", // 主样式文件路径
  "type": "module", // 默认模块类型为 ES Module
  "sideEffects": [
    "**/*.css" // 表示 CSS 文件有副作用（不可 tree-shake）
  ],
  
  // 定义不同模块的导出配置，支持不同模块系统的导入方式
  "exports": { 
    ".": {
      "import": {
        "types": "./dist/esm/index.d.ts", // ESM 类型定义
        "default": "./dist/esm/index.js" // ESM 入口文件
      },
      "require": {
        "types": "./dist/cjs/index.d.ts", // CJS 类型定义
        "default": "./dist/cjs/index.js" // CJS 入口文件
      }
    },
    "./jalali": { // Jalali 日历模块导出配置
      "import": {
        "types": "./dist/esm/jalali.d.ts",
        "default": "./dist/esm/jalali.js"
      },
      "require": {
        "types": "./dist/cjs/jalali.d.ts",
        "default": "./dist/cjs/jalali.js"
      }
    },
    "./persian": { // 波斯语模块导出配置
      "import": {
        "types": "./dist/esm/persian.d.ts",
        "default": "./dist/esm/persian.js"
      },
      "require": {
        "types": "./dist/cjs/persian.d.ts",
        "default": "./dist/cjs/persian.js"
      }
    },
    "./locale": { // 国际化模块导出配置
      "import": {
        "types": "./dist/esm/locale.d.ts",
        "default": "./dist/esm/locale.js"
      },
      "require": {
        "types": "./dist/cjs/locale.d.ts",
        "default": "./dist/cjs/locale.js"
      }
    },
    "./style": { // 样式文件导出配置（Sass）
      "sass": "./src/style.css"
    },
    "./style.css": { // style.css 的导出规则
      "style": {
        "default": "./src/style.css"
      },
      "import": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.css"
      },
      "require": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.css"
      }
    },
    "./style.module": { // style.module.css 的 Sass 配置
      "sass": "./src/style.module.css"
    },
    "./style.module.css": { // style.module.css 导出规则
      "style": {
        "default": "./src/style.module.css"
      },
      "import": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.module.css"
      },
      "require": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.module.css"
      }
    },
    "./src/style.css": { // src 目录下的 style.css 导出规则
      "style": {
        "default": "./src/style.css"
      },
      "import": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.css"
      },
      "require": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.css"
      }
    },
    "./src/style.module.css": { // src 目录下的 style.module.css 导出规则
      "style": {
        "default": "./src/style.module.css"
      },
      "import": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.module.css"
      },
      "require": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.module.css"
      }
    },
    "./dist/style.css": { // dist 目录下的 style.css 导出规则
      "style": {
        "default": "./src/style.css"
      },
      "import": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.css"
      },
      "require": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.css"
      }
    },
    "./dist/style.module.css": { // dist 目录下的 style.module.css 导出规则
      "style": {
        "default": "./src/style.module.css"
      },
      "import": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.module.css"
      },
      "require": {
        "types": "./src/style.module.css.d.ts",
        "default": "./src/style.module.css"
      }
    },
    "./package.json": { // package.json 的导出方式
      "import": "./package.json",
      "require": "./package.json",
      "default": "./package.json"
    },
    "./examples": { // 示例模块导出
      "types": "./examples/index.d.ts",
      "import": "./examples/index.ts"
    }
  },
  
  // 脚本命令
  "scripts": { 
    "prepublish": "pnpm build", // 发布前构建
    "build": "pnpm build:cjs && pnpm build:esm && pnpm build:css", // 构建所有模块
    "build:cjs": "tsc --project tsconfig-cjs.json && echo '{ \"type\": \"commonjs\" }' > dist/cjs/package.json", // 构建 CJS 模块
    "build:esm": "tsc --project tsconfig-esm.json", // 构建 ESM 模块
    "build:css": "./scripts/build-css.sh ./src/style.css ./src/style.module.css", // 构建 CSS 文件
    "lint": "eslint .", // ESLint 代码检查
    "test": "jest --selectProjects examples --selectProjects src", // 运行测试
    "test:build": "jest --selectProjects examples/built", // 测试构建后的代码
    "test-watch": "jest --watch", // 启用 watch 模式运行测试
    "typecheck": "tsc --project ./tsconfig.json --noEmit", // 类型检查
    "typecheck-watch": "tsc --project ./tsconfig.json --noEmit --watch" // 类型检查并监听变化
  },
  
  // 包含在 npm 发布中的文件
  "files": [
    "dist",
    "src",
    "jalali.js",
    "jalali.d.ts",
    "persian.js",
    "persian.d.ts",
    "locale.js",
    "locale.d.ts"
  ],
  
  // 生产依赖
  "dependencies": { 
    "@date-fns/tz": "1.2.0", // 时区处理库
    "date-fns": "4.1.0", // 日期处理库
    "date-fns-jalali": "4.1.0-0" // 波斯历支持
  },
  
  // 开发依赖
  "devDependencies": { 
    "@jest/types": "^29.6.3", // Jest 类型定义
    "@radix-ui/react-select": "^2.1.7", // Radix UI 的 Select 组件
    "@swc/core": "^1.11.8", // SWC 编译核心
    "@swc/jest": "^0.2.37", // 在 Jest 中使用 SWC
    "@testing-library/dom": "^10.4.0", // DOM 测试工具
    "@testing-library/jest-dom": "^6.6.3", // Jest DOM 匹配器
    "@testing-library/react": "^16.3.0", // React 测试工具
    "@testing-library/user-event": "^14.6.1", // 用户事件模拟
    "@trivago/prettier-plugin-sort-imports": "^5.2.2", // Prettier 插件：排序 import
    "@types/jest": "^29.5.14", // Jest 类型定义
    "@types/node": "^22.14.1", // Node.js 类型定义
    "@types/react": "^19.1.1", // React 类型定义
    "@types/react-dom": "^19.1.2", // React DOM 类型定义
    "@typescript-eslint/eslint-plugin": "^8.29.1", // TypeScript ESLint 插件
    "@typescript-eslint/parser": "^8.29.1", // TypeScript 解析器
    "eslint": "^8.57.1", // JavaScript/TypeScript Linter
    "eslint-config-prettier": "^10.1.2", // 关闭与 Prettier 冲突的 ESLint 规则
    "eslint-import-resolver-typescript": "^3.10.0", // 支持 TypeScript import 解析
    "eslint-plugin-import": "^2.31.0", // import 语法校验插件
    "eslint-plugin-jest": "^28.11.0", // Jest ESLint 插件
    "eslint-plugin-prettier": "^5.2.6", // 将 Prettier 作为 ESLint 规则运行
    "eslint-plugin-react": "^7.37.5", // React ESLint 插件
    "eslint-plugin-react-hooks": "^5.2.0", // React Hooks ESLint 插件
    "eslint-plugin-require-extensions": "^0.1.3", // 强制 require 带扩展名
    "eslint-plugin-testing-library": "^7.1.1", // Testing Library ESLint 插件
    "html-validate": "^9.5.2", // HTML 校验工具
    "jest": "^29.7.0", // 单元测试框架
    "jest-environment-jsdom": "^29.7.0", // Jest JS-DOM 环境
    "jest-transform-css": "^6.0.2", // Jest 支持 CSS 转换
    "mockdate": "^3.0.5", // 模拟当前时间
    "prettier": "^3.5.3", // 代码格式化工具
    "prettier-plugin-jsdoc": "^1.3.2", // JSDoc Prettier 插件
    "react": "^19.1.0", // React 依赖
    "react-dom": "^19.1.0", // React DOM 依赖
    "ts-node": "^10.9.2", // 在 Node.js 中直接运行 TypeScript
    "tslib": "^2.8.1", // TypeScript 工具库，用于减少重复代码。
    "typescript": "~5.8.3", // TypeScript 版本
    "typescript-css-modules": "^1.0.4" // 支持 CSS Modules 类型定义
  },
  
  // 对等依赖
  "peerDependencies": { 
    "react": ">=16.8.0" // 最低 React 版本要求
  },
  
  // 资助信息
  "funding": { 
    "type": "individual",
    "url": "https://github.com/sponsors/gpbl"
  }
}
```

# scripts


## build 

以下是 package.json 中 `build` 脚本的详细解释：

---
### 1. build 命令详解

```json
"build": "pnpm build:cjs && pnpm build:esm && pnpm build:css"
```

通过此命令，项目能同时支持：
- **Node.js**（CJS）
- **现代浏览器**（ESM）
- **样式直接导入**（CSS 文件）

#### 1. 构建顺序

该命令*通过 `&&` 按顺序执行*以下三个子命令：

1. `pnpm build:cjs`
2. `pnpm build:esm`
3. `pnpm build:css`

**含义：**
- `pnpm`：使用 pnpm 包管理器运行脚本。
- `&&`：表示*顺序执行*（前一个命令成功完成后才执行下一个）。参考 `&` 的含义：[[_posts/libraries/shadcn/component/react-day-picker/源码解析/package.json#5. 潜在优化点|package.json]]

**构建顺序的意义：**
- **顺序执行**：确保所有构建步骤按预期完成。例如，*CSS 构建可能依赖 ESM/CJS 构建完成后的目录结构*。
- **错误中断**：若任意步骤失败（如 TypeScript 编译错误），*后续步骤不会执行*（符合 `&&` 行为）。

---

#### 2. 构建目的

该命令*完整实现了项目的多目标输出：*

1. **兼容性**：生成 CommonJS（`dist/cjs`）和 ESM（`dist/esm`）双版本，适配不同环境。
2. **样式支持**：将 CSS 文件复制到构建目录，确保样式可直接通过包导入。
3. **模块类型声明**：为 CommonJS 目录生成 package.json，显式声明 `"type": "commonjs"`。

---

#### 3. 实际使用

```bash
# 执行完整构建流程 
pnpm build
```

**输出结果：**

```
dist/
├── cjs/          # CommonJS 模块（Node.js 环境使用）
│   ├── index.js
│   └── package.json
├── esm/          # ESM 模块（浏览器环境使用）
│   └── index.js
├── style.css     # 全局样式文件
└── style.module.css  # CSS Modules 样式文件
```
---

### 2. 子命令详解
参考：[[tsc]]

#### 1 `pnpm build:cjs`

```json
// package.json 中的定义 
"build:cjs": "tsc --project tsconfig-cjs.json && echo '{ \"type\": \"commonjs\" }' > dist/cjs/package.json"
```


- **作用**：
    - 使用 tsconfig-cjs.json 配置文件编译 TypeScript 代码为 **CommonJS 模块**（适用于 Node.js 环境）。
    - 在 `dist/cjs` 目录下生成编译后的 `.js` 文件。
    - *创建 dist/cjs/package.json 文件并指定* `"type": "commonjs"`，确保 Node.js 正确识别模块类型。
	    - `echo '{ "type": "commonjs" }'`：将 JSON 字符串 "{ "type": "commonjs" }" 输出到标准输出（终端）。
	    - `>`：重定向操作符，表示*将左侧命令的输出写入右侧文件*。若文件已存在，则覆盖内容；若不存在，则创建新文件。
	    - `dist/cjs/package.json`：目标文件路径，表示将 JSON 写入 `dist/cjs` 目录下的 package.json 文件。

#### 2 `pnpm build:esm`

```json
// package.json 中的定义 
"build:esm": "tsc --project tsconfig-esm.json"
```

- **作用**：
    - *使用 tsconfig-esm.json 配置文件编译 TypeScript 代码*为 **ES Module (ESM)**（适用于现代浏览器或支持 ESM 的环境）。
    - 输出到 `dist/esm` 目录。

#### 3 `pnpm build:css`

```json
// package.json 中的定义 
"build:css": "./scripts/build-css.sh ./src/style.css ./src/style.module.css"
```

- **作用**：
    - *运行 scripts/build-css.sh 脚本*，将 ./src/style.css 和 ./src/style.module.css *复制到 `dist/` 目录*。
    - 该脚本通常用于将样式文件打包供用户直接导入（例如 `import 'react-day-picker/style.css'`）。

#### 4 类型检查

```json
"typecheck": "tsc --project ./tsconfig.json --noEmit",

"typecheck-watch": "tsc --project ./tsconfig.json --noEmit --watch"
```

---

### 3. 配置文件关联

各子命令*依赖的配置文件：*

|子命令|配置文件|输出目录|
|---|---|---|
|`build:cjs`|tsconfig-cjs.json|`dist/cjs`|
|`build:esm`|tsconfig-esm.json|`dist/esm`|
|`build:css`|scripts/build-css.sh|`dist/`|

---

### 4. build 命令的对比

|方式|特点|适用场景|
|---|---|---|
|`pnpm build:cjs`|生成 CommonJS|Node.js 项目或旧版环境|
|`pnpm build:esm`|生成 ESM|现代浏览器或支持 ESM 的环境|
|`pnpm build:css`|复制样式文件|样式文件分发|
|`pnpm build`|完整构建流程|发布 npm 包前的最终构建|

---

### 5. 潜在优化点

- **并行化构建**：若构建步骤无依赖关系，可*通过 `&` 并行执行（但当前顺序可能有隐含依赖）*。
- **清理缓存**：添加 `rimraf dist/*` 清理旧构建产物。
- **验证输出**：在构建后添加 `typecheck` 或 `lint` 步骤确保质量。

---

## **prepublish**

```json
"prepublish": "pnpm build",
```

以下是 package.json 中 `"prepublish": "pnpm build"` 的详细解释：

---

### 1. 命令含义

```json
"prepublish": "pnpm build"
```

- **`prepublish`** 是 npm 的*生命周期钩子*（hook），**在以下场景自动触发：**
    - 执行 `npm publish` 发布包到 npm 之前。
    - 执行 `pnpm publish`（项目实际使用的包管理器）。
- **`pnpm build`** 是调用项目的构建命令，定义如下：
```json
"build": "pnpm build:cjs && pnpm build:esm && pnpm build:css"
```

---

### 2. 完整构建流程

*当执行 `pnpm publish` 或 `npm publish` 时，会按顺序执行：*

1. **触发 `prepublish` 钩子** → 运行 `pnpm build`
2. **执行构建命令**：
    - **`build:cjs`**：使用 tsconfig-cjs.json 编译 TypeScript 为 CommonJS 模块，输出到 `dist/cjs`，并生成 dist/cjs/package.json 声明模块类型。
    - **`build:esm`**：使用 tsconfig-esm.json 编译 TypeScript 为 ESM 模块，输出到 `dist/esm`。
    - **`build:css`**：执行 scripts/build-css.sh 脚本，将样式文件复制到 `dist/` 目录。

---

### 3. 为何需要 `prepublish`？

#### (1) 确保发布前的完整性

- **自动构建**：*防止开发者忘记手动运行* `pnpm build`，避免发布未构建的代码。
- **兼容性保障**：确保 `dist/` 目录包含：    
```
dist/
├── cjs/          # CommonJS 模块（Node.js 使用）
│   ├── index.js
│   └── package.json
├── esm/          # ESM 模块（现代浏览器使用）
│   └── index.js
├── style.css     # 全局样式
└── style.module.css  # CSS Modules 样式
```


#### (2) 模块类型声明

- **CommonJS 兼容性**：通过 dist/cjs/package.json 显式声明 `"type": "commonjs"`，确保 Node.js 正确解析模块。
- **ESM 支持**：`dist/esm` 输出现代模块格式，适配浏览器环境。

#### (3) 样式文件分发

- 通过 `build:css` 确保样式文件（如 style.css）可被用户直接导入：
```ts
import 'react-day-picker/style.css'; // 用户使用方式
```

---

### 4. 技术背景：npm 生命周期钩子

- **生命周期钩子机制**：npm/pnpm 在执行某些操作（如 `install`、`publish`）时，*会自动运行对应的脚本*。
- **`prepublish` 的作用**：
    - 在发布前执行清理、构建、测试等操作。
    - 确保发布的包是最新且完整的版本。

**对比其他钩子：**

|钩子|触发时机|用途|
|---|---|---|
|`prepublish`|发布前|构建、验证|
|`postinstall`|安装后|自动化配置|
|`prestart`|启动前|构建生产环境代码|

---

### 5. 实际使用场景

#### (1) 发布 npm 包

```bash
# 发布前自动运行 pnpm build 
pnpm publish
```

- **输出验证**：确保 `dist/` 目录包含所有构建产物。
- **错误中断**：若构建失败（如 TypeScript 编译错误），发布流程终止。

#### (2) 开发者本地测试

```bash
# 手动执行构建（通常用于调试） 
pnpm build
```

- **验证构建结果**：
    - 检查 `dist/cjs` 和 `dist/esm` 是否存在。
    - 验证样式文件是否正确复制。

---

### 6. 潜在问题与解决方案

#### (1) 构建缓存问题

- **现象**：旧文件残留导致发布内容不一致。
- **解决**：发布前手动清理 `dist/` 目录：
    
```json
    "prepublish": "rimraf dist/* && pnpm build"
```
    

#### (2) 权限不足

- **现象**：dist/cjs/package.json 写入失败。
- **解决**：确保目录权限正确：
    
```bash
    chmod 755 dist dist/cjs
```
    

#### (3) 依赖未安装

- **现象**：构建时提示 `tsc` 或 build-css.sh 不存在。
- **解决**：发布前确保依赖已安装：
    
```bash
    pnpm install
```
    

---

### 7. 对比其他构建方式

| 方式                                 | 特点       | 适用场景       |
| ---------------------------------- | -------- | ---------- |
| `pnpm prepublish`                  | 发布前自动构建  | *发布 npm 包* |
| `pnpm build`                       | 手动执行完整构建 | 本地调试、CI/CD |
| `pnpm build:cjs && pnpm build:esm` | 仅构建代码    | 快速验证模块输出   |
| `sh scripts/build-css.sh`          | *仅构建样式*  | 调试 CSS 修改  |

---

### 8. 最佳实践建议

- **显式声明构建顺序**：保持 `build` 脚本的清晰顺序（CJS → ESM → CSS）。
- **添加类型检查**：在发布前运行 `pnpm typecheck` 确保类型安全：
```json
    "prepublish": "pnpm typecheck && pnpm build"
```
    
- **自动化测试**：结合 `pnpm test` 确保发布代码的稳定性：
```json
    "prepublish": "pnpm test && pnpm build"
```

---

### 9. 验证构建结果

```bash
# 查看 dist/ 目录结构 
ls -R dist/
```

**预期输出**：

```
dist/
├── cjs/
│   ├── index.js
│   └── package.json
├── esm/
│   └── index.js
├── style.css
└── style.module.css
```
---

### 10. 总结

- **核心作用**：在*发布 npm 包前*确保代码和样式已正确构建。
- **技术价值**：通过自动化流程*避免人为疏漏*，保证发布的包符合多模块兼容性要求（CJS + ESM + CSS）。
- **项目意义**：react-day-picker 作为多环境兼容的组件库，依赖此脚本确保不同用户（Node.js、浏览器）获取正确的构建产物。

通过此配置，项目实现了从源码到可发布包的无缝衔接，是标准化发布流程的关键步骤。

---

## lint
ESLint 代码检查

```json
"lint": "eslint .",
```

以下是 `"lint": "eslint ."` 的详细解释：

---

### 1. **命令含义**

json

`"lint": "eslint ."`

- **eslint**：调用 ESLint 工具，用于 JavaScript/TypeScript 代码的静态分析和规范检查。
- **.**：表示检查当前目录下的所有文件（递归检查子目录）。

---

### 2. **ESLint 的作用**

- **代码规范**：确保代码符合项目定义的编码标准（如缩进、命名、类型安全等）。
- **错误检测**：发现潜在问题（如未使用的变量、语法错误、逻辑缺陷）。
- **风格统一**：与 Prettier 配合使用，统一代码格式（如引号、分号、空格）。
- **安全性检查**：避免常见安全漏洞（如 React 的 key 缺失、无障碍属性缺失）。

---

### 3. **项目中的具体行为**

#### (1) **检查范围**

- **递归扫描目录**：
    
    `. ├── src/          # 核心组件与 Hook ├── examples/     # 示例代码 ├── test/         # 测试文件 ├── website/      # 文档站点代码 └── scripts/      # 构建脚本`
    
- **检查文件类型**：
    - `.ts`、`.tsx`（TypeScript 文件）
    - `.js`、`.jsx`（JavaScript 文件）
    - `.mjs`、`.cjs`（ESM/CJS 模块文件）

#### (2) **依赖的配置文件**

- **`.eslintrc.js` 或 `.eslintrc.json`**：定义规则集、插件和解析器配置。
- **`.eslintignore`**：指定忽略检查的目录或文件（如 `dist/`、`.git/`）。

#### (3) **规则来源**

- **项目配置**：可能继承以下配置：
    
    js
    
    `// .eslintrc.js 示例片段 {   "extends": [     "eslint:recommended",     "plugin:@typescript-eslint/recommended",     "plugin:react/recommended",     "plugin:prettier/recommended"  // 与 Prettier 集/no-explicit-any成   ]`
    

`：禁止使用` any` 类型。

- `react/prop }

``- **关键规则**： - `@typescript-eslint-types`：确保 React 组件的 props 正确定义。 - `jest/no-disabled-tests`：禁止禁用测试用例。 - `prettier/prettier`：与 Prettier 协作确保代码格式统一。  ---  ### 4. **实际使用场景** #### (1) **手动运行** ```bash # 执行代码规范检查 pnpm lint``

- **输出示例**：
    
    `✖ 2 problems ✓ 0 problems`
    
- **问题级别**：
    - `Error`：强制修复（如类型错误）。
    - `Warning`：建议优化（如未使用的导入）。

#### (2) **集成开发流程**

- **IDE 插件**：VS Code 中安装 ESLint 插件，实时高亮问题。
- **Git 提交钩子**：通过 `husky` 在提交代码前自动运行：
    
    json
    
    `// package.json "scripts": {   "precommit": "pnpm lint" }`
    

#### (3) **修复可纠正问题**

bash

`# 自动修复可解决的问题（如格式错误） pnpm lint --fix`

---

### 5. **技术背景：ESLint 的工作原理**

1. **解析器**：使用 TypeScript 解析器（如 @typescript-eslint/parser）处理 `.ts`/`.tsx` 文件。
2. **插件**：加载项目所需的规则插件（如 @typescript-eslint/eslint-plugin、eslint-plugin-react）。
3. **规则匹配**：对每个文件应用 `.eslintrc` 中定义的规则。
4. **报告生成**：输出问题列表（可配置为 JSON、Stylish 等格式）。

---

### 6. **常见问题与解决方案**

#### (1) **未预期的错误**

- **现象**：ESLint 报错，但代码逻辑正确。
- **解决**：调整 `.eslintrc` 中的规则（如关闭特定规则）：
    
    js
    
    `{   "rules": {     "no-console": ["warn"]  // 将错误降级为警告   } }`
    

#### (2) **性能问题**

- **现象**：大规模项目检查速度慢。
- **解决**：添加 `.eslintignore` 忽略非必要目录：
    
    bash
    
    `# .eslintignore dist/ node_modules/ .docusaurus/`
    

#### (3) **Prettier 冲突**

- **现象**：ESLint 和 Prettier 的格式规则冲突。
- **解决**：使用 eslint-config-prettier 禁用冲突规则：
    
    bash
    
    `pnpm add -D eslint-config-prettier prettier`
    
    js
    
    `// .eslintrc.js {   "extends": ["eslint:recommended", "prettier"] }`
    

---

### 7. **对比其他代码检查方式**

|工具|特点|适用场景|
|---|---|---|
|**ESLint**|可扩展性强，支持 TypeScript 和 React|主流代码规范|
|**TSLint**|旧版 TypeScript 专用工具（已弃用）|迁移遗留项目|
|**Prettier**|专注代码格式化（无语义检查）|与 ESLint 配合使用|
|**TypeScript 编译器**|检查类型安全|辅助 ESLint 的静态分析|

---

### 8. **典型输出示例**

bash

`✓ 0 problems`

或

bash

`✖ 2 problems example.tsx   10:1  error  Unexpected console statement  no-console   15:2  error  Missing JSDoc comment       require-jsdoc test/example.test.tsx   5:1  warning  Avoid using 'describe' without 'it'  jest/no-test-callback-without-await`

---

### 9. **项目中的最佳实践**

- **严格模式**：启用 `"strict": true` 确保类型安全。
- **测试工具集成**：使用 `eslint-plugin-jest` 检查测试代码。
- **无障碍检查**：通过 `eslint-plugin-jsx-a11y` 确保符合 WCAG 2.1 AA 标准。
- **依赖检查**：`eslint-import-resolver-typescript` 确保模块导入正确。

---

### 10. **总结**

- **核心作用**：通过 ESLint 静态检查代码，确保质量、可维护性和一致性。
- **技术价值**：作为多模块项目（CJS + ESM + CSS）的重要质量保障工具。
- **项目意义**：react-day-picker 作为开源组件库，依赖此脚本保证代码风格统一，减少贡献者之间的冲突。

通过此命令，开发者可以快速发现并修复代码中的问题，是标准化开发流程的关键步骤。

---

## test

```json
"test": "jest --selectProjects examples --selectProjects src",

"test:build": "jest --selectProjects examples/built",

"test-watch": "jest --watch",
```


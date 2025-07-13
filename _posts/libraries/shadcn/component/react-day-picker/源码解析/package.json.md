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


## build 命令

以下是 package.json 中 `build` 脚本的详细解释：

```json
"build": "pnpm build:cjs && pnpm build:esm && pnpm build:css"
```

---

### 1. 构建顺序解析

该命令*通过 `&&` 按顺序执行*以下三个子命令：

1. `pnpm build:cjs`
2. `pnpm build:esm`
3. `pnpm build:css`

**含义：**

- `pnpm`：使用 pnpm 包管理器运行脚本。
- `&&`：表示*顺序执行*（前一个命令成功完成后才执行下一个）。

---

### 2. 子命令详解

#### (1) `pnpm build:cjs`

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

#### (2) `pnpm build:esm`

```json
// package.json 中的定义 
"build:esm": "tsc --project tsconfig-esm.json"
```

- **作用**：
    - 使用 tsconfig-esm.json 配置文件编译 TypeScript 代码为 **ES Module (ESM)**（适用于现代浏览器或支持 ESM 的环境）。
    - 输出到 `dist/esm` 目录。

#### (3) `pnpm build:css`

json

`// package.json 中的定义 "build:css": "./scripts/build-css.sh ./src/style.css ./src/style.module.css"`

- **作用**：
    - 运行 scripts/build-css.sh 脚本，将 ./src/style.css 和 ./src/style.module.css 复制到 `dist/` 目录。
    - 该脚本通常用于将样式文件打包供用户直接导入（例如 `import 'react-day-picker/style.css'`）。

---

### 3. **构建流程目的**

该命令完整实现了项目的多目标输出：

1. **兼容性**：生成 CommonJS（`dist/cjs`）和 ESM（`dist/esm`）双版本，适配不同环境。
2. **样式支持**：将 CSS 文件复制到构建目录，确保样式可直接通过包导入。
3. **模块类型声明**：为 CommonJS 目录生成 package.json，显式声明 `"type": "commonjs"`。

---

### 4. **配置文件关联**

各子命令依赖的配置文件：

|子命令|配置文件|输出目录|
|---|---|---|
|`build:cjs`|tsconfig-cjs.json|`dist/cjs`|
|`build:esm`|tsconfig-esm.json|`dist/esm`|
|`build:css`|scripts/build-css.sh|`dist/`|

---

### 5. **构建顺序的意义**

- **顺序执行**：确保所有构建步骤按预期完成。例如，CSS 构建可能依赖 ESM/CJS 构建完成后的目录结构。
- **错误中断**：若任意步骤失败（如 TypeScript 编译错误），后续步骤不会执行（符合 `&&` 行为）。

---

### 6. **实际使用场景**

bash

`# 执行完整构建流程 pnpm build`

**输出结果：**

`dist/ ├── cjs/          # CommonJS 模块（Node.js 环境使用） │   ├── index.js │   └── package.json ├── esm/          # ESM 模块（浏览器环境使用） │   └── index.js ├── style.css     # 全局样式文件 └── style.module.css  # CSS Modules 样式文件`

---

### 7. **对比其他构建方式**

|方式|特点|适用场景|
|---|---|---|
|`pnpm build:cjs`|生成 CommonJS|Node.js 项目或旧版环境|
|`pnpm build:esm`|生成 ESM|现代浏览器或支持 ESM 的环境|
|`pnpm build:css`|复制样式文件|样式文件分发|
|`pnpm build`|完整构建流程|发布 npm 包前的最终构建|

---

### 8. **潜在优化点**

- **并行化构建**：若构建步骤无依赖关系，可通过 `&` 并行执行（但当前顺序可能有隐含依赖）。
- **清理缓存**：添加 `rimraf dist/*` 清理旧构建产物。
- **验证输出**：在构建后添加 `typecheck` 或 `lint` 步骤确保质量。

---

通过此命令，项目能同时支持：

- **Node.js**（CJS）
- **现代浏览器**（ESM）
- **样式直接导入**（CSS 文件）

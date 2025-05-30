`package.json` 是 Node.js 项目的核心配置文件，用于*描述项目的元信息、依赖关系、脚本命令*等。它是所有基于 npm、yarn 或 pnpm 的前端和后端项目（如 React、Vue、Node.js）的基础。

---

## 一、基本结构

一个典型的 `package.json` 文件如下：

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "description": "A simple project example",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "build": "webpack --mode production"
  },
  "keywords": ["example", "project"],
  "author": "John Doe",
  "license": "MIT",
  "dependencies": {
    "react": "^18.2.0",
    "lodash": "~4.17.19"
  },
  "devDependencies": {
    "eslint": "^8.0.0"
  }
}
```

---

## 二、字段详解

### 1. 基本信息

| 字段名           | 含义                   |
| ------------- | -------------------- |
| `name`        | 项目名称（必须），通常小写、用连字符连接 |
| `version`     | 项目版本号（语义化版本：主.次.修订）  |
| `description` | 项目描述                 |
| `keywords`    | 关键词数组，便于搜索           |
| `author`      | 作者信息（字符串或对象）         |
| `license`     | 开源协议，默认为 MIT         |

---

### 2. 入口与执行

| 字段名        | 含义 |
|---------------|------|
| `main`        | 指定模块的入口文件（用于 Node.js 模块） |
| `module`      | ES Module 入口文件（用于支持 tree-shaking） |
| `browser`     | 前端入口文件（用于浏览器环境） |
| `bin`         | 可执行命令的路径（用于 CLI 工具） |

示例：
```json
"bin": {
  "my-cli": "./cli.js"
}
```

---

### 3. 脚本命令（scripts）

用于定义可运行的命令，使用 `npm run <script-name>` 或 `yarn <script-name>` 执行。

```json
"scripts": {
  "start": "node index.js",
  "build": "webpack --mode production",
  "lint": "eslint .",
  "test": "jest"
}
```

> 注意：某些工具会自动识别默认脚本，例如 `start` 和 `test`。

---

### 4. 依赖管理

| 字段名           | 含义 |
|------------------|------|
| `dependencies`   | 生产环境依赖（上线需要） |
| `devDependencies`| 开发环境依赖（构建/测试工具） |
| `peerDependencies`| 对等依赖（插件类包要求宿主已安装） |
| `optionalDependencies`| 可选依赖（安装失败也不会报错） |
| `bundledDependencies`| 指定打包时一起发布的依赖 |

#### 版本号语法说明：
参考：[[项目版本号（语义化版本：主.次.修订）]]

- `^1.2.3`：允许补丁和次版本更新（如 1.2.4、1.3.0）
- `~1.2.3`：仅允许补丁更新（如 1.2.4）
- `1.2.3`：固定版本
- `*`：任意版本
- `>=1.2.3` / `<=1.2.3`：版本范围
- `workspace:*`：pnpm workspace 本地依赖

---

### 5. 其他常见字段

| 字段名             | 含义 |
|--------------------|------|
| `private`          | 设置为 `true` 表示私有项目，防止意外发布到 npm |
| `engines`          | 指定 node/npm/yarn 等版本要求 |
| `repository`       | 仓库地址（GitHub/Gitee 等） |
| `bugs`             | 报告 bug 的 URL |
| `homepage`         | 项目主页链接 |
| `files`            | 发布 npm 包时包含的文件列表 |
| `config`           | 配置参数，供脚本使用（通过 `npm_package_config_*` 获取） |

示例：
```json
"engines": {
  "node": ">=16.0.0",
  "npm": ">=8.0.0"
}
```

---

## 三、常用操作命令

| 命令 | 说明 |
|------|------|
| `npm init` / `yarn init` | 初始化 package.json |
| `npm install <pkg>` / `yarn add <pkg>` | 安装依赖，默认添加到 dependencies |
| `npm install <pkg> -D` / `yarn add -D <pkg>` | 添加到 devDependencies |
| `npm install <pkg> -g` / `yarn global add <pkg>` | 全局安装 |
| `npm uninstall <pkg>` / `yarn remove <pkg>` | 卸载依赖 |
| `npm update <pkg>` / `yarn upgrade <pkg>` | 更新依赖 |
| `npm run <script>` / `yarn <script>` | 运行脚本命令 |

---

## 四、最佳实践建议

1. **保持依赖精简**：只安装必要的依赖；
2. **合理使用 devDependencies**：开发工具类依赖不要放在生产依赖中；
3. **版本控制谨慎**：避免使用 `^` 导致意外升级导致兼容问题；
4. **设置 engines 字段**：确保团队成员使用一致的 node/npm 版本；
5. **使用 scripts 统一命令**：简化构建、测试、部署流程；
6. **私有项目加 private: true**：防止误发布到 npm；
7. **配合 .npmignore 使用 files 字段**：控制发布包内容；

---

## 五、完整示例

```json
{
  "name": "awesome-app",
  "version": "2.1.0",
  "description": "A full-stack application with React and Node.js",
  "main": "server/index.js",
  "module": "src/index.js",
  "scripts": {
    "start": "node server/index.js",
    "build": "webpack --mode production",
    "lint": "eslint .",
    "test": "jest",
    "format": "prettier --write src/**/*.js"
  },
  "keywords": ["react", "node", "fullstack"],
  "author": "Jane Doe <jane@example.com>",
  "license": "MIT",
  "private": true,
  "engines": {
    "node": ">=16.0.0",
    "npm": ">=8.0.0"
  },
  "dependencies": {
    "react": "^18.2.0",
    "express": "^4.18.2"
  },
  "devDependencies": {
    "eslint": "^8.30.0",
    "jest": "^29.0.0",
    "prettier": "^2.8.0"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/janedoe/awesome-app.git"
  },
  "bugs": {
    "url": "https://github.com/janedoe/awesome-app/issues"
  },
  "homepage": "https://janedoe.github.io/awesome-app"
}
```

---

如果你正在创建一个新的项目，或者维护多个子项目（Monorepo），也可以结合 `pnpm-workspace.yaml` 来统一管理多个 `package.json` 文件。

---



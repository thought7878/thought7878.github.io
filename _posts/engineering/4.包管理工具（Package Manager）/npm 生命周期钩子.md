`npm` 生命周期钩子（Lifecycle Scripts）是 `package.json` 中用于定义项目*在不同阶段自动执行脚本*的机制。这些钩子使得开发者可以*在安装、构建、测试、发布等关键操作前后*插入自定义逻辑，*从而实现自动化流程*。

---

## 一、什么是 npm 生命周期钩子？

npm 提供了一系列**预定义的生命周期事件（lifecycle events）**，你可以在 `package.json` 的 `scripts` 字段中定义这些事件对应的脚本命令，npm 会在相应阶段自动调用它们。

---

## 二、常见的 npm 生命周期钩子

| 钩子名称             | 触发时机                         | 描述                      |
| ---------------- | ---------------------------- | ----------------------- |
| `prepublish`     | 发布前或本地运行 `npm install` 时（旧版） | *已被弃用*，请使用 `prepare`    |
| `prepare`        | 发布前 或 执行 `git clone` 后       | 替代 `prepublish`，常用于构建   |
| `prepublishOnly` | **仅在发布前**触发                  | 不会在本地 `npm install` 时触发 |
| `postpublish`    | 发布完成后触发                      | 可用于通知或清理                |
| `preinstall`     | 安装包之前执行                      | 通常用于权限检查或配置准备           |
| `install`        | 安装包完成后执行                     | 常用于初始化工作                |
| `postinstall`    | 安装完成后执行                      | 常用于构建、链接依赖等操作           |
| `preuninstall`   | 卸载前执行                        | 可用于清理资源                 |
| `uninstall`      | 卸载过程中执行                      |                         |
| `postuninstall`  | 卸载后执行                        |                         |
| `preversion`     | 在运行 `npm version` 前执行        | 可用于检查是否通过测试             |
| `version`        | 在修改版本号后执行                    |                         |
| `postversion`    | 修改版本并提交 Git 后执行              | 常用于推送代码                 |
| `test`           | 运行 `npm test` 时执行            | 推荐编写单元/集成测试             |
| `start`          | 运行 `npm start` 时执行           | 默认值为 `node app.js`      |
| `restart`        | 运行 `npm restart` 时执行         | 等价于 stop + start        |
| `stop`           | 运行 `npm stop` 时执行            | 停止服务                    |

---

## 三、钩子执行顺序示例

当你运行：

```bash
npm install
```

会依次执行：
```
preinstall → install → postinstall
```

当你运行：

```bash
npm publish
```

会依次执行：
```
prepublishOnly → prepare → prepublish → publish → postpublish
```

---

## 四、常用场景与示例

### ✅ 场景 1：发布前构建

```json
{
  "scripts": {
    "build": "webpack --mode production",
    "prepare": "npm run build"
  }
}
```

> 注意：`prepare` 在 `npm install` 时也会执行，因此如果只想在发布时构建，请使用 `prepublishOnly`。

---

### ✅ 场景 2：发布前检查测试和 lint

```json
{
  "scripts": {
    "lint": "eslint .",
    "test": "jest",
    "prepublishOnly": "npm run lint && npm run test"
  }
}
```

---

### ✅ 场景 3：安装后自动构建本地依赖

适用于本地开发的库：

```json
{
  "scripts": {
    "build": "tsc",
    "postinstall": "npm run build"
  }
}
```

---

### ✅ 场景 4：版本更新后自动推送 Git

```json
{
  "scripts": {
    "preversion": "npm test",
    "postversion": "git push && git push --tags"
  }
}
```

---

## 五、注意事项

- ❗ `prepare` 和 `prepublish` 的区别：
  - `prepare` 在 `npm install` 和 `npm publish` 时都会运行。
  - `prepublishOnly` 只在 `npm publish` 时运行。

- ⚠️ 避免在 `prepare` 中进行耗时操作，因为它会影响本地 `npm install` 的速度。

- 🧠 `npm version` 会自动提交 Git commit 并打 tag。

- 🔐 如果你的模块需要编译或构建才能使用，建议使用 `prepublishOnly` 而不是 `prepare`，避免污染用户环境。

---

## 六、npm vs yarn 生命周期差异

| 操作 | npm | yarn |
|------|-----|------|
| 安装依赖 | `npm install` | `yarn install` |
| 生命周期支持 | 支持完整钩子 | 也支持大部分钩子 |
| 特殊行为 | `prepare` 在 `install` 时运行 | `prepare` 默认不运行（除非设置了 `enableScripts: true`） |

如果你使用的是 Yarn，注意某些钩子可能不会默认执行。

---

## 七、总结：推荐实践

| 目标 | 推荐使用的钩子 |
|------|----------------|
| 发布前构建 | `prepublishOnly` |
| 本地开发自动构建 | `postinstall` |
| 发布前检查 | `prepublishOnly` |
| 版本升级后处理 | `postversion` |
| 测试 / Lint | `test`, `lint` |
| 自动化部署 | `postpublish` |

---

如果你想结合 CI/CD 流程（如 GitHub Actions）、Monorepo（如 Nx、Lerna、pnpm workspace）或 TypeScript 项目来定制 npm 生命周期脚本，我也可以提供更具体的方案。欢迎继续提问！
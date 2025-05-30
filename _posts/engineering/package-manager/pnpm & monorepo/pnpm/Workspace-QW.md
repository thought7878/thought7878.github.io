`pnpm` 的 **Workspace** 功能是其原生支持的 **Monorepo（单体仓库）管理机制**，允许你在同一个仓库中管理多个项目或模块，并实现本地依赖共享、跨项目引用等功能。

---

## 一、基本概念

### 1. 什么是 Workspace？

Workspace 是 `pnpm` 提供的一种机制，用于将多个子项目放在一个统一的仓库中进行管理。这些子项目可以是应用、库、工具等，它们可以相互依赖并共享代码。

例如：

```
my-workspace/
├── package.json
├── pnpm-workspace.yaml
├── apps/
│   └── web-app/
│       └── package.json
├── packages/
│   └── shared-utils/
│       └── package.json
```

在这个结构中：
- `apps/web-app` 是一个应用；
- `packages/shared-utils` 是一个可被多个项目复用的公共包；
- `web-app` 可以直接依赖 `shared-utils`，无需发布到 npm。

---

## 二、启用 Workspace

要启用 workspace 功能，只需要在项目根目录创建一个 `pnpm-workspace.yaml` 文件。

### 示例配置文件内容：

```yaml
packages:
  - 'apps/**'
  - 'packages/**'
```

这表示 `pnpm` 将会把 `apps/` 和 `packages/` 目录下的所有子目录视为独立的包（package），并允许它们之间互相引用。

---

## 三、使用本地依赖

假设你有两个项目：

- `packages/shared`：公共库；
- `apps/web-app`：前端应用。

### 在 `apps/web-app/package.json` 中添加依赖：

```json
{
  "dependencies": {
    "shared": "workspace:*"
  }
}
```

这样就可以在 `web-app` 中直接引用 `shared` 模块中的代码，而不需要将其发布到 npm 或手动链接。

---

## 四、常用命令

| 命令 | 说明 |
|------|------|
| `pnpm install` | 安装所有依赖，包括本地 workspace 包 |
| `pnpm add <pkg> -w` | 在根 `package.json` 中安装依赖（适用于所有子项目） |
| `pnpm add <pkg> -r` | 在整个 workspace 中安装依赖 |
| `pnpm add <pkg> --filter <project-name>` | 在指定子项目中安装依赖 |
| `pnpm run <script> --recursive` | 在所有子项目中运行脚本 |
| `pnpm ls --depth 10` | 查看所有 workspace 子项目及其依赖关系 |

---

## 五、优势

| 优势 | 描述 |
|------|------|
| 零成本本地依赖 | 子项目之间可以直接引用，无需发布或软链 |
| 快速构建与测试 | 支持并行执行命令，提升开发效率 |
| 共享配置 | 可在根目录设置统一的 lint、test、build 脚本 |
| 简洁易维护 | 不需要额外工具如 Lerna，开箱即用 |
| 磁盘空间优化 | 结合 pnpm 的硬链接机制，节省重复安装的空间 |

---

## 六、典型应用场景

- 多个前端项目共享组件库；
- 后端服务 + SDK + 工具包统一管理；
- 微前端架构下多个子应用共存；
- 开发和维护私有库时实时调试；
- 构建企业级 Monorepo 结构。

---

## 七、注意事项

- 所有子项目的 `package.json` 必须有唯一的 `name` 字段；
- 使用 `workspace:*` 作为版本号时，`pnpm` 会自动识别为本地依赖；
- 如果你之前使用过 `npm link` 或 `yarn link`，pnpm 的 workspace 是更稳定、更安全的替代方案；
- 不建议将 workspace 项目提交到多个 Git 仓库中，因为路径依赖可能出错。

---

## 八、完整示例流程

### 步骤 1：初始化根目录

```bash
mkdir my-workspace
cd my-workspace
npm init -y
```

### 步骤 2：创建 workspace 配置文件

```bash
echo 'packages:
  - "apps/**"
  - "packages/**"' > pnpm-workspace.yaml
```

### 步骤 3：创建子项目

```bash
mkdir -p packages/shared apps/web-app
```

#### `packages/shared/package.json`

```json
{
  "name": "shared",
  "version": "1.0.0"
}
```

#### `apps/web-app/package.json`

```json
{
  "name": "web-app",
  "version": "1.0.0",
  "dependencies": {
    "shared": "workspace:*"
  }
}
```

### 步骤 4：安装依赖

```bash
pnpm install
```

此时 `web-app` 会自动识别并链接 `shared` 模块。

---

## 九、总结

`pnpm` 的 Workspace 是一个轻量但功能强大的 Monorepo 解决方案，适合以下场景：

- 多个项目共享代码；
- 统一依赖管理；
- 快速搭建多模块项目；
- 替代 `npm link` / `yarn link` 方案。

如果你正在使用 monorepo 结构，但不想引入 Lerna、Nx 等复杂工具，pnpm workspace 是一个非常理想的首选方案。

如需根据你的具体项目结构生成完整的 workspace 配置，请告诉我你的目录结构和目标。
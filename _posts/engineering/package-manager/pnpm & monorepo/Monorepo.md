`Monorepo（单一仓库）` 是一种软件开发和*代码管理*的*架构模式*，指的是*将多个项目、模块、包*放在一个*统一的版本控制仓库*中进行管理。这种做法在*大型团队、多项目协作、组件库开发*等场景下非常常见。

**Monorepo** 是一个英文术语，由两个词组成：
- **Mono** ：源自希腊语 monos ，意为“单一” 或 “独自”；
- **Repo** ：是 _repository_ （仓库）的缩写。

---

## 一、什么是 Monorepo？

传统的开发方式是 `多仓库（Multi-repo）`：*每个项目都有自己的独立仓库*。而 Monorepo 则是**将多个相关项目放在同一个仓库中**，例如：

```
my-monorepo/
├── packages/
│   ├── common/
│   ├── ui/
│   └── utils/
├── apps/
│   ├── web/
│   └── mobile/
├── pnpm-workspace.yaml
└── package.json
```

在这个例子中，`apps/` 下是不同平台的应用，`packages/` 下是*共享的*库或工具模块。

---

## 二、Monorepo 的优势

| 优势          | 说明                          |
| ----------- | --------------------------- |
| **共享代码更方便** | 多个项目之间可以*轻松引用彼此的代码*，避免重复开发  |
| 统一依赖管理      | 所有*项目的依赖*可以在一个地方管理，减少冲突     |
| 更好的协作体验     | *团队成员*在一个仓库中协作，便于统一提交、测试、发布 |
| 提升构建效率      | 可以利用缓存、增量构建等机制提升 CI/CD 效率   |
| 原子化提交       | 一次提交可以影响多个项目，保证变更的一致性       |

---

## 三、常见的 Monorepo 工具

### 1. Lerna
- 最早流行起来的 Monorepo 管理工具；
- 支持 npm/yarn；
- 提供版本管理和发布流程自动化；
- 配置相对复杂，适合需要严格版本控制的团队。

```bash
npx lerna init
```

### 2. Nx
- 由 Angular 团队推动发展；
- 支持多种框架（React、Vue、Node.js 等）；
- 强大的任务调度、缓存、影响分析等功能；
- 适合大型企业级项目。

```bash
npx create-nx-workspace@latest
```

### 3. Turborepo
- *极速*构建系统，支持并行执行、远程缓存；
- 轻量级，配置简单；
- *适合追求构建性能的现代前端项目*。

```bash
npm install -g turbo
turbo new
```

### 4. pnpm workspace
- pnpm 原生支持的 monorepo 功能；
- 不需要额外安装插件；
- 配置简单，适合轻量级 monorepo；
- 支持跨项目本地依赖。

**示例配置文件 `pnpm-workspace.yaml`：**

```yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

然后在子项目中使用本地依赖：

```json
{
  "dependencies": {
    "shared-utils": "workspace:*"
  }
}
```

---

## 四、Monorepo 的适用场景

| 场景           | 是否适合用 Monorepo |
| ------------ | -------------- |
| 多个项目*共享大量代码* | ✅ 推荐           |
| 一个组织下*多个产品线* | ✅ 推荐           |
| 微前端架构        | ✅ 推荐           |
| 组件库 + 应用     | ✅ 推荐           |
| *完全隔离的*独立项目  | ❌ 不推荐          |
| 不同团队*无协作需求*  | ❌ 不推荐          |

---

## 五、典型目录结构示例

```bash
monorepo/
├── apps/
│   ├── admin/
│   └── dashboard/
├── packages/
│   ├── shared/
│   ├── auth/
│   └── logger/
├── tools/
│   └── scripts/
├── pnpm-workspace.yaml
└── package.json
```

---

## 六、Monorepo 的挑战

| 挑战          | 说明                                        |
| ----------- | ----------------------------------------- |
| 仓库体积大       | 所有项目都在一个仓库里，可能变大，需合理拆分或使用 sparse checkout |
| 权限管理复杂      | 需要精细控制不同人员对子目录的访问权限                       |
| CI/CD 复杂度上升 | 需要根据改动范围决定是否重新构建或部署哪些项目                   |
| 学习成本        | 对新手来说，理解 monorepo 的结构和工具链有一定门槛            |

---

## 七、如何开始一个 Monorepo 项目？

### 方式一：使用 pnpm + workspace（轻量级）

1. 初始化根目录 `package.json`

```bash
npm init -y
```

2. 创建 `pnpm-workspace.yaml` 文件

```yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

3. 在 `packages/` 和 `apps/` 中创建子项目，并设置本地依赖：

```json
{
  "name": "my-app",
  "dependencies": {
    "shared-utils": "workspace:*"
  }
}
```

4. 安装依赖：

```bash
pnpm install
```

---

### 方式二：使用 Lerna

1. 安装 Lerna：

```bash
npm install --global lerna
```

2. 初始化：

```bash
lerna init
```

3. 添加包：

```bash
lerna create shared-utils
lerna create my-app
```

4. 添加依赖：

```bash
lerna add shared-utils packages/my-app
```

---

### 方式三：使用 Nx 或 Turborepo

可参考其官方文档快速搭建，适合中大型项目。

---

## 八、总结

Monorepo 是一种高效的项目管理方式，尤其适合：

- 多项目共享代码；
- 需要统一依赖和版本管理；
- 团队协作紧密的场景。

选择合适的工具（如 pnpm、Lerna、Nx、Turborepo）可以显著提升开发效率和维护便利性。
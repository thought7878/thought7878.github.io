> 我计划学习 pnpm，请帮我列一个详细的学习步骤、学习计划

### DS 的思考

好的，用户想学习 pnpm，并希望我帮他制定一个详细的学习计划。我需要先了解他对 pnpm 的了解程度以及学习的目标是什么。可能他已经使用过 npm 或 yarn，现在想转向 pnpm，或者是在项目中遇到了性能问题，想通过 pnpm 来优化依赖管理。

首先，我应该确认 pnpm 的核心优势，比如快速安装、节省磁盘空间、严格的依赖结构等。然后根据这些特点，设计一个从基础到进阶的学习路径，确保他能逐步掌握 pnpm 的使用。

用户可能需要了解如何安装 pnpm，基本命令与 npm/yarn 的对比，项目迁移步骤，以及高级功能如工作区管理、依赖锁定和 CI/CD 集成。同时，他还可能关心如何解决常见问题，比如依赖冲突或权限问题。

我需要确保学习计划覆盖这些方面，并推荐相关资源，如官方文档、社区工具和实战案例。此外，考虑到用户可能有不同的使用场景，比如个人项目或企业级项目，计划需要具备一定的灵活性，让他可以根据实际需求调整学习重点。

最后，应该提醒用户实践的重要性，通过实际项目应用所学知识，加深理解。同时，提供持续学习的建议，比如关注更新和社区动态，确保他能跟上工具的最新发展。

---

以下是针对 **pnpm** 的系统性学习计划，从基础到高级逐步深入，结合现代前端开发场景，帮助你高效掌握这一工具：

---

### 一、**基础入门（1-3 天）**

#### 1. **前置知识准备**

- **必会**：npm/yarn 基础使用、Node.js 基础、package.json 结构
- **推荐**：了解符号链接（Symlink）、硬链接（Hard link）概念

#### 2. **pnpm 核心优势**

##### 核心特性：

- 依赖存储全局化（节省磁盘空间）
- 依赖隔离（避免幽灵依赖）
- 快速安装（硬链接复用）
- 严格模式（`node_modules` 扁平化可控）

##### 对比传统工具：

| **工具** | 安装速度 | 磁盘占用 | 依赖管理 | 安全性 |
| -------- | -------- | -------- | -------- | ------ |
| **npm**  | 慢       | 高       | 扁平化   | 低     |
| **yarn** | 中       | 中       | 扁平化   | 中     |
| **pnpm** | 快       | 低       | 严格隔离 | 高     |

#### 3. **环境搭建**

- **全局安装**：

  ```bash
  # 使用 npm 安装
  npm install -g pnpm

  # 使用独立脚本安装
  curl -fsSL https://get.pnpm.io/install.sh | sh -
  ```

- **验证安装**：
  ```bash
  pnpm --version
  ```

#### 4. **基础命令迁移（类比 npm/yarn）**

| **功能** | npm 命令            | pnpm 命令            |
| ------ | ----------------- | ------------------ |
| 初始化项目  | `npm init`        | `pnpm init`        |
| 安装依赖   | `npm install`     | `pnpm install`     |
| 添加生产依赖 | `npm add lodash`  | `pnpm add lodash`  |
| 添加开发依赖 | `npm add -D vite` | `pnpm add -D vite` |
| 运行脚本   | `npm run dev`     | `pnpm run dev`     |

---

### 二、**核心功能掌握（3-7 天）**

#### 1. **依赖管理机制**

- **全局存储（Store）**：
  - 默认路径：`~/.pnpm-store`
  - 查看存储位置：
    ```bash
    pnpm store path
    ```
- **硬链接复用**：
  - 所有项目共享同一份依赖文件（通过硬链接引用）
- **依赖隔离**：
  - `node_modules` 结构示例：
    ```
    node_modules/
      .pnpm/          # 所有依赖的硬链接
      lodash@4.17.21/ # 符号链接到 .pnpm 目录
    ```

#### 2. **工作区（Workspace）管理**

- **Monorepo 支持**：
  ```bash
  # 项目根目录配置 pnpm-workspace.yaml
  packages:
    - 'packages/*'
    - 'apps/*'
  ```
- **跨包依赖安装**：
  ```bash
  pnpm add axios --filter @project/core
  ```

#### 3. **高级安装选项**

- **选择性安装**：

  ```bash
  # 仅安装生产依赖
  pnpm install --prod

  # 忽略可选依赖
  pnpm install --ignore-optional
  ```

- **依赖更新**：

  ```bash
  # 交互式更新
  pnpm update -i

  # 更新所有依赖
  pnpm update --latest
  ```

#### 4. **依赖锁定与审计**

- **锁定文件**：
  - `pnpm-lock.yaml`（兼容 `package-lock.json` 格式）
- **安全审计**：
  ```bash
  pnpm audit
  ```

---

### 三、**性能优化与实战（7-14 天）**

#### 1. **安装速度优化**

- **离线模式**：
  ```bash
  pnpm install --offline
  ```
- **网络镜像配置**：
  ```bash
  # .npmrc 配置淘宝镜像
  registry=https://registry.npmmirror.com/
  ```

#### 2. **存储优化**

- **清理旧版本依赖**：
  ```bash
  pnpm store prune
  ```
- **自定义存储路径**：
  ```bash
  pnpm config set store-dir /path/to/store
  ```

#### 3. **Monorepo 实战**

- **常用命令**：

  ```bash
  # 运行所有包的 build 命令
  pnpm -r run build

  # 为所有包安装 lodash
  pnpm -r add lodash
  ```

- **依赖提升（Hoisting）控制**：
  ```bash
  # .npmrc 配置
  public-hoist-pattern[]=*eslint*
  public-hoist-pattern[]=*prettier*
  ```

#### 4. **CI/CD 集成**

- **缓存策略**：
  ```yaml
  # GitHub Actions 示例
  - name: Setup pnpm
    uses: pnpm/action-setup@v2
    with:
      version: 8
  - name: Restore cache
    uses: actions/cache@v3
    with:
      path: ~/.pnpm-store
      key: ${{ runner.os }}-pnpm-store-${{ hashFiles('**/pnpm-lock.yaml') }}
  ```

---

### 四、**高级配置与生态（14 天+）**

#### 1. **配置文件（.npmrc）**

- **常用配置项**：

```ini
     # 禁用自动安装 peerDependencies
     auto-install-peers=false

     # 设置并发数
     child-concurrency=4

     # 全局 bin 目录
     global-bin-dir=~/.pnpm-global
```

#### 2. **插件系统**

- **官方插件**：
  ```bash
  # 安装插件
  pnpm plugin import <plugin-name>
  ```
- **常用插件**：
  - `@pnpm/plugin-commands-patching`（依赖补丁管理）
  - `@pnpm/plugin-commands-audit`（增强安全审计）

#### 3. **问题排查与调试**

- **日志级别控制**：
  ```bash
  pnpm install --loglevel debug
  ```
- **依赖树分析**：
  ```bash
  pnpm list --depth=2
  ```

---

### **学习资源推荐**

1. **官方文档**：[pnpm 中文文档](https://pnpm.io/zh/)
2. **实战课程**：
   - Udemy: [Monorepo Management with pnpm](https://www.udemy.com/course/monorepo-management-with-pnpm/)
3. **开源项目参考**：
   - [Vue 3 源码](https://github.com/vuejs/core)（使用 pnpm Workspace）
   - [Nuxt 3 官方示例](https://github.com/nuxt/starter/tree/v3-pnpm)

---

### **学习计划时间表**

| **阶段**       | **每日学习时间** | **目标成果**                        |
| -------------- | ---------------- | ----------------------------------- |
| 基础入门       | 2 小时 × 3 天    | 完成项目初始化与基础命令迁移        |
| 核心功能掌握   | 2 小时 × 5 天    | 掌握 Workspace 和依赖管理机制       |
| 性能优化与实战 | 3 小时 × 7 天    | 实现 Monorepo 项目优化与 CI/CD 集成 |
| 高级配置       | 2 小时 × 持续    | 熟练使用插件系统与深度配置          |

---

### **常见问题与解决方案**

1. **幽灵依赖（Phantom Dependency）**

   - **现象**：代码中引用了未声明的依赖
   - **解决**：启用严格模式（`nodeLinker: pnpm`）或使用 `pnpm check` 扫描

2. **依赖冲突**

   - **现象**：同一依赖多个版本导致问题
   - **解决**：使用 `pnpm overrides` 强制指定版本：
     ```json
     {
       "pnpm": {
         "overrides": {
           "lodash": "4.17.21"
         }
       }
     }
     ```

3. **权限问题**
   - **现象**：`EPERM` 或 `EACCES` 错误
   - **解决**：使用 `pnpm setup` 修复权限或配置存储目录为用户可写路径

---

通过此计划，你可以在 **3-4 周** 内系统掌握 pnpm，并能在实际项目中显著提升依赖管理效率和团队协作体验。

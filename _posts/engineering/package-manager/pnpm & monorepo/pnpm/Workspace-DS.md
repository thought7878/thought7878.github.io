pnpm 的 **Workspace（工作区）** 是一个强大的 **Monorepo（单体仓库）管理工具**，它允许你在单个代码仓库中管理多个相互关联的包（package），同时高效处理依赖关系，节省磁盘空间并提升开发效率。

---

### 核心概念与优势
1. **依赖共享与符号链接**  
   - 所有子包的依赖统一安装在根目录的 `node_modules` 中（通过 `pnpm` 的 **硬链接** 机制）。
   - 子包之间的本地依赖通过**符号链接（symlink）** 指向工作区内的包，避免重复安装。
   - **节省磁盘空间**（尤其适用于大型项目）。

2. **统一管理**  
   - 通过根目录的 `pnpm-workspace.yaml` 定义工作区结构。
   - 支持在根目录执行命令操作所有子包（如安装依赖、运行脚本）。

3. **隔离性**  
   - 子包的依赖不会相互污染（每个子包只能访问自己声明的依赖）。

---

### 快速上手
#### 1. 初始化工作区
```bash
mkdir my-monorepo && cd my-monorepo
pnpm init -y  # 初始化根目录 package.json
```

#### 2. 创建 `pnpm-workspace.yaml`
```yaml
# pnpm-workspace.yaml
packages:
  - "packages/**"     # 包含 packages 目录下所有包
  - "apps/*"          # 包含 apps 目录下所有应用
  - "!**/test/**"     # 排除 test 目录
```

#### 3. 创建子包
```text
my-monorepo/
├── packages/
│   ├── utils/        # 子包1
│   │   └── package.json (name: @my/utils)
│   └── ui/           # 子包2
│       └── package.json (name: @my/ui)
├── apps/
│   └── web/          # 应用
│       └── package.json (name: web)
```

#### 4. 添加依赖
- **为子包添加外部依赖**  
  ```bash
  pnpm add lodash --filter @my/utils  # 只为 utils 安装 lodash
  ```

- **子包之间相互引用**  
  在 `apps/web/package.json` 中引用本地包：
  ```json
  {
    "dependencies": {
      "@my/ui": "workspace:*",   # 使用 workspace 协议
      "@my/utils": "workspace:^"
    }
  }
  ```
  运行 `pnpm install` 后，pnpm 会自动创建符号链接指向本地包。

---

### 常用命令
| 命令 | 作用 |
|------|------|
| `pnpm install` | 安装所有子包依赖 |
| `pnpm add <pkg> -F <子包名>` | 为指定子包添加依赖 |
| `pnpm run --filter <子包名> <script>` | 运行指定子包的脚本 |
| `pnpm run -r <script>` | 在所有子包中运行脚本 (`-r` = `--recursive`) |
| `pnpm update -r` | 递归更新所有子包依赖 |

---

### 进阶技巧
1. **依赖提升**  
   通过 `.npmrc` 配置提升公共依赖到根目录：
   ```ini
   # .npmrc
   shamefully-hoist = true
   ```

2. **过滤操作**  
   ```bash
   # 同时操作多个子包
   pnpm --filter "@my/*" run build
   
   # 操作依赖特定包的子包
   pnpm --filter "...{取决于@my/ui}" test
   ```

3. **版本发布**  
   使用 `changesets` 工具管理版本和发布：
   ```bash
   pnpm add -Dw @changesets/cli  # 在根目录安装
   pnpm changeset               # 生成版本变更
   pnpm changeset version       # 更新版本号
   pnpm changeset publish       # 发布
   ```

---

### 典型目录结构
```text
monorepo/
├── pnpm-workspace.yaml
├── package.json
├── packages/
│   ├── shared/     # 共享工具库
│   ├── core/       # 核心逻辑
│   └── config/     # 配置包
├── apps/
│   ├── web/        # 前端应用
│   └── mobile/     # 移动端应用
└── docs/           # 文档（非包目录）
```

---

### 注意事项
- **禁止直接修改子包 `node_modules`**：依赖统一由根目录管理。
- **工作区协议**：子包相互引用时使用 `"workspace:*"` 或 `"workspace:^"` 确保本地链接。
- **根目录 `package.json`**：通常设为 `"private": true`，避免被误发布。

通过 pnpm Workspace，你可以高效管理复杂项目结构，尤其适合微前端、多平台应用或组件库开发场景。
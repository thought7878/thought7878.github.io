`nvm`（Node Version Manager）是一个用于**管理多个 Node.js 版本**的命令行工具。它允许你在同一台机器上**安装、切换和使用不同版本的 Node.js**，非常适合前端/全栈开发人员在不同项目中使用不同 Node 版本的场景。

---

### 为什么使用 nvm？

- **多版本共存**：项目 A 用 Node 18，项目 B 用 Node 20？没问题！
- **快速切换**：一条命令即可切换版本。
- **独立隔离**：每个 Node 版本的全局 `npm` 包互不影响。
- **LTS 支持**：轻松安装最新 LTS（长期支持）版本。
- **跨平台**：支持 macOS、Linux（Windows 需 WSL）。

> ❗ **注意**：nvm **不适用于 Windows 原生系统**（可用 [nvm-windows](https://github.com/coreybutler/nvm-windows) 替代）。

---

### 安装 nvm（macOS 推荐方式）

> **不要用 Homebrew 安装 nvm**！请使用官方脚本：

```bash
# 下载并运行安装脚本
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

该脚本会：
- 将 nvm 安装到 `~/.nvm`
- 自动在 `~/.zshrc`（或 `~/.bash_profile`）中添加初始化代码

然后重载配置：
```bash
source ~/.zshrc   # macOS 12 默认使用 zsh
```

验证安装：
```bash
nvm --version     # 应输出 0.39.7
```

---

### 常用命令

#### 基础命令

- **查看帮助**: `nvm --help` 1
- **查看nvm版本**: `nvm --version` 7

#### 版本查看

- **列出已安装的Node.js版本**: `nvm list` 或 `nvm ls` 10
- **列出远程可用的Node.js版本**: `nvm ls-remote` 8
- **查看最新LTS版本**: `nvm ls-remote --lts` 3

#### 安装版本

- **安装指定版本**: `nvm install <version>` 3
- **安装最新版本**: `nvm install node` 或 `nvm install latest` 6
- **安装最新LTS版本**: `nvm install --lts` 6
- **安装指定主版本的最新版**: `nvm install <major_version>` 7

#### 版本切换

- **使用指定版本**: `nvm use <version>` 5
- **使用最新版本**: `nvm use node` 6
- **使用默认版本**: `nvm use default` 3

#### 版本管理

- **卸载指定版本**: `nvm uninstall <version>` 10
- **设置默认版本**: `nvm alias default <version>` 8
- **创建版本别名**: `nvm alias <name> <version>` 8
- **删除版本别名**: `nvm unalias <name>` 8

#### 高级命令

- **迁移全局包**: `nvm migrate` - 将全局包从一个Node.js版本迁移到另一个版本 9
- **清理缓存**: `nvm cache clear` 3

---

### 项目级自动切换（推荐！）

在项目根目录创建 `.nvmrc` 文件，写入所需 Node 版本，例如：
```bash
echo "18" > .nvmrc
```

进入项目目录时，运行：
```bash
nvm use
```
nvm 会自动读取 `.nvmrc` 并切换版本。

> 💡 可配合 shell 插件（如 `zsh-nvm`）实现**自动切换**，无需手动执行 `nvm use`。

---

### 常见问题

#### 1. **`nvm: command not found`**
- 原因：shell 配置未加载。
- 解决：检查 `~/.zshrc` 是否包含 nvm 初始化代码，或运行 `source ~/.zshrc`。

#### 2. **`npm` 全局包找不到**
- nvm 的每个 Node 版本有独立的全局模块路径。
- 切换版本后，需重新安装全局工具（如 `npm install -g pnpm`）。

#### 3. **与 Homebrew 安装的 Node 冲突**
- 如果之前用 `brew install node`，请先卸载：
  ```bash
  brew uninstall node
  ```
- 确保 `which node` 指向 `~/.nvm/...` 而非 `/opt/homebrew/bin/node`。

---

### nvm vs fnm vs n

| 工具 | 语言 | 速度 | 自动切换 | 推荐场景 |
|------|------|------|--------|--------|
| **nvm** | Shell | 中等 | ✅（需 `.nvmrc`） | 通用，社区标准 |
| **fnm** | Rust | ⚡ 快 | ✅ | 追求性能，现代化 |
| **n** | Node.js | 快 | ❌ | 简单场景，需 sudo |

> 对于 macOS 用户，**nvm 是最稳妥的选择**。

---

### 总结

- **安装**：用官方脚本，**不要用 Homebrew**。
- **使用**：`nvm install` + `nvm use` + `.nvmrc`。
- **管理**：每个项目可锁定 Node 版本，避免“在我机器上能跑”。

如果你需要，我可以提供：
- 自动切换版本的 zsh 配置
- 国内镜像加速安装（如淘宝源）
- 迁移现有全局 npm 包到 nvm 环境

欢迎继续提问！
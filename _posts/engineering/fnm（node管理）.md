`fnm`（**Fast Node Manager**）是一个用 Rust 编写的 **快速、跨平台的 Node.js 版本管理工具**，用于在你的系统上轻松安装、切换和管理多个 Node.js 版本。

它的主要目标是替代像 `nvm`（Node Version Manager）这样的传统工具，但**性能更高、安装更简单、兼容性更好**。

---

### fnm 的核心特点

| 特性                                         | 说明                                                      |
| ------------------------------------------ | ------------------------------------------------------- |
| ⚡ **速度快**                                  | 用 Rust 编写，启动和切换版本比 `nvm` 快很多（尤其实用在 CI/CD 或频繁切换项目时）      |
| 🧩 **自动版本切换**                              | 支持通过项目根目录的 `.node-version` 或 `.nvmrc` 文件自动切换 Node.js 版本 |
| 🌐 **跨平台**                                 | 支持 macOS、Linux、Windows（包括 WSL）                          |
| 📦 **自动安装 Node.js**                        | 可直接安装指定版本，无需手动下载                                        |
| 🔌 **与主流 shell 兼容**                        | 支持 Bash、Zsh、Fish、PowerShell 等                           |
| 🧪 **支持 `corepack` 和 `npm`/`yarn`/`pnpm`** | 安装 Node.js 时会自带 npm，也可配合现代包管理器使用                        |

---

### 安装 fnm（macOS 示例）

#### 方法 1：使用 Homebrew（推荐）
```bash
brew install fnm
```

#### 方法 2：使用官方安装脚本
```bash
curl -fsSL https://fnm.vercel.app/install | bash
```

#### 方法 3：使用 `npm`（不推荐，因为可能还没 Node.js）
> 一般不建议，因为 fnm 本身就是为管理 Node.js 而生的。

---

### 配置 Shell（以 Zsh 为例）

安装后需在 shell 配置文件中初始化 `fnm`。

编辑 `~/.zshrc`（或 `~/.bashrc`）：

```bash
echo 'eval "$(fnm env --use-on-cd)"' >> ~/.zshrc
```

然后重载配置：

```bash
source ~/.zshrc
```

> `--use-on-cd` 表示：**当你 `cd` 进入某个目录时，如果该目录有 `.node-version` 或 `.nvmrc`，自动切换 Node.js 版本**。

---

### 常用命令

| 命令                    | 说明                   |
| --------------------- | -------------------- |
| `fnm list-remote`     | 查看所有可安装的 Node.js 版本  |
| `fnm install 20`      | 安装最新的 LTS 版本（如 20.x） |
| `fnm install 22.11.0` | 安装具体版本               |
| `fnm use 20`          | 临时使用 Node.js 20      |
| `fnm default 20`      | 设置 20 为默认版本          |
| `fnm list`            | 查看已安装的版本             |
| `fnm current`         | 查看当前使用的版本            |

---

### 项目自动切换示例

在项目根目录创建 `.node-version` 文件：

```txt
20.18.0
```

或 `.nvmrc`（兼容 `nvm`）：

```txt
lts/iron
```

当你 `cd` 进该项目目录时，`fnm` 会**自动切换到指定版本**（前提是已安装）。

---

### 为什么用 fnm 而不是 nvm？

| 对比项 | `fnm` | `nvm` |
|--------|-------|-------|
| 语言 | Rust（编译型）| Bash（脚本）|
| 速度 | ⚡ 极快 | 🐢 较慢（每次 shell 启动都加载）|
| 安装 | 简单（单个二进制）| 需要 source 脚本 |
| 跨平台 | ✅ 原生支持 Windows | ❌ Windows 支持差（需用 nvm-windows）|
| 自动切换 | ✅ 内置支持 | ✅ 但需额外配置 |

---

### 卸载 fnm

- 删除配置行（从 `~/.zshrc` 等文件中移除 `eval "$(fnm env ...)"`）
- 删除 fnm 本身：
  ```bash
  # 如果用 Homebrew 安装
  brew uninstall fnm

  # 如果用脚本安装
  rm -rf ~/.fnm
  ```

---

### 官方资源
- GitHub: https://github.com/Schniz/fnm  
- 官网: https://fnm.vercel.app/

> 💡 如果你经常在不同 Node.js 版本的项目之间切换，**强烈推荐使用 `fnm`** —— 它是现代前端/全栈开发者的高效工具。

需要帮你配置或对比其他工具（如 `volta`、`n`）吗？
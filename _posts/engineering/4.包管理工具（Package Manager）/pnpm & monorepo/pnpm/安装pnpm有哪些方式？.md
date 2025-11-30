安装 **pnpm**（Performant npm）有多种方式，适用于不同操作系统和使用场景。以下是主流的安装方法，按推荐程度排序：

---

### 1. 使用独立脚本安装（推荐）
这是官方最推荐的方式，适用于所有平台（Windows、macOS、Linux）。

```sh
# 使用 curl
curl -fsSL https://get.pnpm.io/install.sh | sh -

# 或使用 wget（Linux/macOS）
wget -qO- https://get.pnpm.io/install.sh | sh -
```

> 优点：自动安装最新稳定版，不依赖 Node.js 版本管理器，独立于 npm。  
> 📁 安装位置：通常在 `~/.local/share/pnpm`（Linux/macOS）或 `%APPDATA%\pnpm`（Windows）。

安装后确保 `~/.local/share/pnpm`（或对应路径）已加入 `PATH`。大多数情况下脚本会自动处理。

---

### 2. 通过 npm 安装（如果你已有 npm）

```sh
npm install -g pnpm
```

> ⚠️ 注意：这种方式依赖你当前的 Node.js 环境。如果你使用 **fnm / nvm / volta** 等工具管理 Node，切换 Node 版本时可能需要重新安装 pnpm。
> 
> 适合：临时使用或已有 npm 环境的用户。

---

### 3. 通过包管理器安装

#### 🍏 macOS（使用 Homebrew）
```sh
brew install pnpm
```

#### 🐧 Linux（Debian/Ubuntu）
```sh
# 使用官方脚本更可靠，但也可通过 apt（非官方源可能滞后）
# 推荐仍用方式 1
```

#### 🪟 Windows（使用 Scoop 或 Chocolatey）
```sh
# Scoop
scoop install pnpm

# Chocolatey
choco install pnpm
```

> 优点：便于更新和管理；  
> ❌ 缺点：版本可能略滞后于官方最新版。

---

### 4. **使用 Corepack**（Node.js 内置，Node.js ≥ v16.13）

Corepack 是 Node.js 官方提供的包管理器管理工具（包含对 pnpm、yarn 的支持）。

```sh
# 启用 Corepack（Node.js ≥ 16.13 内置，但默认禁用）
corepack enable

# 安装/使用指定版本的 pnpm
corepack prepare pnpm@latest --activate
```

> 优点：无需额外安装脚本，由 Node.js 官方维护，版本切换方便；  
> 🔁 适合：使用现代 Node.js 版本（如 v18+、v20+）的开发者。

> 💡 提示：如果你用 **fnm / nvm** 管理 Node.js，建议优先使用 Corepack，因为它是“随 Node 环境”绑定的。

---

### 5. 手动下载二进制文件（高级用户）

从 [GitHub Releases](https://github.com/pnpm/pnpm/releases) 下载对应平台的二进制文件，放入 `PATH` 中。

---

### 验证安装

无论用哪种方式安装，验证是否成功：

```sh
pnpm --version
```

如果输出版本号（如 `9.12.0`），说明安装成功。

---

### **推荐组合（最佳实践）**

- **macOS/Linux** + **fnm/nvm**：  
  👉 使用 **Corepack**（`corepack enable`）  
  或  
  👉 使用 **独立脚本安装**（与 Node 版本解耦）

- **Windows**：  
  👉 使用 **Scoop** 或 **独立脚本**

- 避免长期使用 `npm install -g pnpm`，除非你清楚 Node 环境不会频繁切换。

---

如有特定环境（如 CI/CD、Docker、企业内网），也可以使用离线安装或指定 registry。欢迎继续提问！
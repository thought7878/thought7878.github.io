以下是 **Homebrew** 的常用命令总结，涵盖安装、卸载、更新、搜索、管理等核心操作，适合 macOS 和 Linux 用户快速查阅。

---

### 一、基础概念

- **Homebrew** 是 macOS/Linux 上的开源包管理工具，简化软件安装和依赖管理。
- **Formula**：软件包的安装脚本（如 `wget`）。
- **Cask**：用于安装 macOS 图形界面应用（如 `Visual Studio Code`）。
- **Tap**：第三方仓库（如 `homebrew/cask`）。

---

### 二、安装与初始化

```bash
# 安装 Homebrew（macOS）
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 安装 Homebrew（Linux）
sh -c "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/install/master/install.sh)"

# 初始化环境变量（Linux 用户需手动添加到 ~/.bashrc 或 ~/.zshrc）
export PATH="/home/linuxbrew/.linuxbrew/bin:$PATH"
export MANPATH="/home/linuxbrew/.linuxbrew/share/man:$MANPATH"
export INFOPATH="/home/linuxbrew/.linuxbrew/share/info:$INFOPATH"
```

---

### 三、核心命令

#### 1. 更新与升级

```bash
brew update               # 更新 Homebrew 自身和公式库
brew upgrade              # 升级所有已安装的包
brew upgrade <package>    # 升级指定包
brew outdated             # 查看可升级的包
```

#### 2. 安装与卸载

```bash
brew install <package>    # 安装包（如 brew install wget）
brew install --cask <package>  # 安装 Cask 应用（如 brew install --cask firefox）
brew uninstall <package>  # 卸载包
brew uninstall --cask <package>  # 卸载 Cask 应用
```

#### 3. 搜索与信息

```bash
brew search <keyword>     # 搜索包（如 brew search python）
brew info <package>       # 查看包信息（版本、依赖等）
brew list                 # 列出已安装的包
brew list --cask          # 列出已安装的 Cask 应用
```

#### 4. 依赖管理

```bash
brew deps <package>       # 查看包的依赖关系
brew deps --tree <package> # 以树状图显示依赖
brew autoremove           # 删除不再需要的依赖
brew cleanup              # 清理旧版本安装包
```

#### 5. 服务管理（brew services）

```bash
brew services list        # 查看托管服务状态
brew services run <service>  # 启动服务（仅当前会话）
brew services start <service> # 启动服务（后台运行）
brew services stop <service>  # 停止服务
brew services restart <service> # 重启服务
```

#### 6. 版本控制（brew switch）

```bash
brew install <package@x.x>  # 安装特定版本（如 brew install python@3.9）
brew switch <package> <version>  # 切换默认版本（如 brew switch python 3.9）
```

#### 7. Tap 管理

```bash
brew tap <repo>           # 添加第三方仓库（如 brew tap homebrew/cask）
brew tap                 # 列出已添加的 Tap
brew untap <repo>        # 移除 Tap（如 brew untap homebrew/cask）
```

#### 8. 其他实用命令

```bash
brew doctor              # 检查环境问题（推荐首次使用时运行）
brew missing             # 检查丢失的依赖
brew pin <package>       # 锁定包版本（防止升级）
brew unpin <package>     # 解锁包版本
brew edit <package>      # 编辑包的 Formula（高级用法）
brew command <command>   # 扩展命令（需安装插件）
```

---

### 四、Homebrew Cask 常用命令

```bash
brew install --cask <app>     # 安装图形应用（如 Visual Studio Code）
brew uninstall --cask <app>   # 卸载图形应用
brew list --cask              # 列出所有已安装的 Cask 应用
brew search --cask <keyword>  # 搜索 Cask 应用
brew info --cask <app>        # 查看 Cask 应用信息
```

---

### 五、Homebrew Bundle（批量管理）

```bash
brew bundle dump           # 导出已安装的包列表到 Brewfile
brew bundle install          # 从 Brewfile 安装所有依赖
brew bundle check          # 检查 Brewfile 中的包是否已安装
```

---

### 六、常见问题解决

1. **权限问题**  
   确保 Homebrew 安装目录权限正确：

   ```bash
   sudo chown -R $(whoami) /usr/local/var/homebrew
   ```

2. **无法连接 GitHub**  
   设置代理（国内用户可能需要）：

   ```bash
   export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
   export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
   ```

3. **修复问题**  
   运行 `brew doctor` 并根据提示修复问题。

---

### 七、示例场景

#### 1. 安装 Python 和依赖

```bash
brew install python
brew install pipenv  # 安装虚拟环境工具
```

#### 2. 安装图形应用（如 Chrome）

```bash
brew install --cask google-chrome
```

#### 3. 管理 MySQL 服务

```bash
brew install mysql
brew services start mysql  # 启动 MySQL 服务
brew services stop mysql   # 停止 MySQL 服务
```

#### 4. 导出/导入包列表

```bash
brew bundle dump   # 生成 Brewfile
brew bundle install # 在新机器上恢复环境
```

---

### 八、注意事项

1. **不要使用 `sudo`**：Homebrew 默认以当前用户权限运行。
2. **定期清理**：使用 `brew cleanup` 避免磁盘占用过高。
3. **避免混用系统包**：优先使用 Homebrew 而非 `pip`/`npm` 安装全局依赖。

如果需要更具体的命令或场景示例（如安装开发工具链、配置代理等），请进一步说明！

# 软件恢复

Homebrew 提供了强大的备份和恢复功能，主要通过 `brew bundle` 命令实现。以下是完整的恢复方法：

## 1. 使用 Brewfile 恢复软件包

### 基本恢复命令
```bash
brew bundle
```
这个命令会从当前目录的 `Brewfile` 文件中读取配置，自动恢复所有定义的软件包。

### 指定特定的 Brewfile
```bash
brew bundle --file=/path/to/your/Brewfile
```
如果备份文件不在当前目录，可以使用 `--file` 参数指定路径。

## 2. 恢复内容范围

Brewfile 可以恢复多种类型的软件：
- **brew tap** 中的软件库 
- **brew 安装的命令行工具** 
- **brew cask 安装的图形应用** 
- **Mac App Store 安装的应用** 

## 3. 完整恢复流程

### 步骤1：安装 Homebrew（如果尚未安装）
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### 步骤2：安装 bundle 扩展
```bash
brew tap homebrew/bundle
```

### 步骤3：恢复软件包
```bash
brew bundle --file=/path/to/backup/Brewfile
```

## 4. 高级恢复选项

### 从标准位置恢复
默认情况下，brew bundle 会从 `~/Brewfile` 位置恢复配置。

### 从 stdin 恢复
```bash
cat Brewfile | brew bundle --file=-
```
使用 `--file=-` 参数可以从标准输入读取配置。

## 5. 备份建议

为了能够有效恢复，建议先创建备份：
```bash
brew bundle dump --file=Brewfile.backup
```
这个命令会导出当前所有已安装的包、cask 应用以及 tap 仓库到指定文件。

通过这种方式，你可以在新系统或重装系统后，快速恢复完整的 Homebrew 开发环境，无需手动重新安装每个软件包。
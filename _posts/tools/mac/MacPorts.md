
MacPorts 的常用命令遵循如下格式：

```bash
[sudo] port [command] [options] [portname]
```

以下是日常使用中最常见的一些命令分类与示例：

---

### 一、基础维护命令

- **更新 MacPorts 自身及 ports 树**  
  ```bash
  sudo port selfupdate
  ```
  此命令会升级 MacPorts 系统工具，并同步最新的软件包定义（即 ports tree）。

- **仅同步 ports 树（不升级 MacPorts）**  
  ```bash
  sudo port sync
  ```

---

### 二、搜索与查询

- **搜索软件包**  
  ```bash
  port search keyword
  ```
  例如：`port search git`

- **列出所有可用 ports**（慎用，结果非常长）  
  ```bash
  port list
  ```

- **查看某个软件包的详细信息**  
  ```bash
  port info portname
  ```

- **查看软件包的依赖关系**  
  ```bash
  port deps portname
  ```

- **查看软件包的可选变体（variants）**  
  ```bash
  port variants portname
  ```
  某些软件提供不同编译选项（如 +x11, +universal 等）。

---

### 三、安装与卸载

- **安装软件包**  
  ```bash
  sudo port install portname
  ```
  例如：`sudo port install wget`。MacPorts 会自动解决并安装所有依赖。

- **带变体安装**  
  ```bash
  sudo port install portname +variant1 -variant2
  ```

- **卸载软件包**  
  ```bash
  sudo port uninstall portname
  ```

- **卸载所有未被其他软件依赖的已安装 ports（清理孤立包）**  
  ```bash
  sudo port uninstall leaves
  ```

---

### 四、清理与维护

- **清理某软件编译产生的临时文件**  
  ```bash
  sudo port clean portname
  ```

- **清理所有软件的临时文件**  
  ```bash
  sudo port clean --all all
  ```

- **列出已安装的 ports**  
  ```bash
  port installed
  ```

- **升级所有已安装的 ports**  
  ```bash
  sudo port upgrade outdated
  ```

---

### 五、帮助

- **查看帮助**  
  ```bash
  port help
  ```
  或查看具体子命令帮助，例如：
  ```bash
  port help install
  ```

---

这些命令足以覆盖 MacPorts 的日常使用。更多高级用法可参考官方文档或使用 `man port` 查看手册。
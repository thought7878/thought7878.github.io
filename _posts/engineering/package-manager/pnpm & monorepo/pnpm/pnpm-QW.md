`pnpm` 是一个快速、节省磁盘空间的 *Node.js 包管理工具*，与 `npm` 和 `yarn` 类似，但其独特的 **硬链接 + 内容寻址存储（store）** 机制使其在性能和磁盘使用方面表现更优。

---

## 一、基本概念

### 1. pnpm 的优势

- **速度快**：安装依赖时比 npm/yarn 快很多；
- **节省磁盘空间**：多个项目共享相同的依赖版本，不会重复下载；
- **支持 workspace 功能**：适合 monorepo 项目管理；
- **兼容 npm registry**：可以使用所有 npm 上的包；
- **确定性安装**：保证不同机器上安装的依赖结构一致。

---

## 二、安装 pnpm

**使用 npm 安装：**

```bash
npm install -g pnpm
```

**检查是否安装成功：**

```bash
pnpm -v
```

---

## 三、常用命令

### 初始化项目

```bash
pnpm init
```

等价于 `npm init`，生成 `package.json` 文件。

---

### 安装依赖

#### 安装所有依赖
根据 package.json

```bash
pnpm install
```

或简写为：

```bash
pnpm i
```

#### 安装指定包
默认为 dependencies

```bash
pnpm add <package-name>
```

例如：

```bash
pnpm add react
```

#### 安装开发依赖
devDependencies

```bash
pnpm add -D <package-name>
```

例如：

```bash
pnpm add -D eslint
```

#### 安装全局包

```bash
pnpm add -g <package-name>
```

例如：

```bash
pnpm add -g typescript
```

---

### 卸载依赖

```bash
pnpm remove <package-name>
```

例如：

```bash
pnpm remove react
```

卸载开发依赖：

```bash
pnpm remove -D eslint
```

---

### 更新依赖

更新单个包：

```bash
pnpm update <package-name>
```

更新所有依赖：

```bash
pnpm update
```

更新并指定 devDependencies：

```bash
pnpm update -D
```

---

### 查看依赖树

```bash
pnpm ls
```

查看某个包的依赖树：

```bash
pnpm ls <package-name>
```

---

### 脚本执行

运行 `package.json` 中定义的脚本：

```bash
pnpm run <script-name>
```

例如：

```bash
pnpm run start
```

可*简化*为（如果脚本名不冲突）：

```bash
pnpm start
```

---

### 清理缓存

```bash
pnpm store prune
```

清理不再使用的包缓存。

---

## 四、Monorepo 支持（Workspace）

pnpm 原生支持 Monorepo 结构，可以通过 `pnpm-workspace.yaml` 配置多个子项目共享依赖。

### 启用 workspace：

在项目根目录创建文件：

```yaml
# pnpm-workspace.yaml
packages:
  - 'packages/*'
```

然后你可以在 `packages/` 目录下建立多个子项目，并通过 `workspace:*` 在各项目之间互相引用。

例如：

```json
{
  "dependencies": {
    "my-utils": "workspace:*"
  }
}
```

---

## 五、pnpm 与 npm / yarn 命令对照表

| npm 命令               | yarn 命令           | pnpm 命令             |
|------------------------|---------------------|-----------------------|
| npm init               | yarn init           | pnpm init             |
| npm install            | yarn install        | pnpm install          |
| npm install react      | yarn add react      | pnpm add react        |
| npm install -D eslint  | yarn add -D eslint  | pnpm add -D eslint    |
| npm uninstall react    | yarn remove react   | pnpm remove react     |
| npm run start          | yarn start          | pnpm run start        |
| npm update             | yarn upgrade        | pnpm update           |

---

## 六、常见问题

### 1. 如何查看已安装的包？

```bash
pnpm list
```

### 2. 如何查看当前使用的 node_modules 结构？

```bash
ls node_modules/.pnpm
```

### 3. 如何迁移现有项目到 pnpm？

- 删除 `node_modules` 和 `package-lock.json` 或 `yarn.lock`；
- 执行 `pnpm install`；
- 后续使用 `pnpm add/remove/run` 等命令即可。

---

## 七、总结

`pnpm` 是现代前端项目中非常推荐使用的包管理工具，尤其适合：

- 大型项目或 monorepo 架构；
- 对磁盘空间和安装速度有要求的团队；
- 需要稳定依赖管理的 CI/CD 环境。

如果你正在使用 npm 或 yarn，迁移到 pnpm 几乎没有成本，只需替换安装命令即可。

如需针对你的具体项目或需求写出完整的 pnpm 使用流程，请告诉我你的项目结构或目标。
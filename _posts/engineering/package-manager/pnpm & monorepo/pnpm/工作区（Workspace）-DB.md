pnpm 的工作区（Workspace）是一个强大的功能，它允许你**在一个仓库中管理多个相互关联的包**，*也就是实现 monorepo（单仓库多项目）的管理*。以下是关于 pnpm 工作区的详细介绍：

### 1. 工作区的作用

- **简化依赖管理**：在一个工作区中，各个子项目可以*共享依赖*，*避免重复安装相同的包*，节省磁盘空间和安装时间。
- **方便项目间协作**：子项目之间可以*轻松地相互引用和依赖*，开发过程中修改一个子项目的代码，其他依赖该项目的子项目可以及时响应，提高开发效率。
- **统一管理脚本**：可以在工作区根目录统一管理所有子项目的脚本，方便执行批量操作，如构建、测试等。

### 2. 配置工作区

#### 2.1 创建工作区

要启用 pnpm 工作区，需要在项目根目录创建一个 `pnpm-workspace.yaml` 文件，该文件用于*定义工作区的范围*。示例如下：

```yaml
packages:
  - "packages/**"
  - "apps/**"
```

上述配置*表示 `packages` 目录和 `apps` 目录下的所有子目录都属于工作区*。你可以根据项目结构调整路径配置。

#### 2.2 初始化项目

在工作区根目录执行 `pnpm init` 初始化 `package.json` 文件。同时，建议在 `package.json` 中添加 `"private": true`，以确保工作区根目录不会被发布到 npm 上。示例：

```json
{
  "name": "my-monorepo",
  "private": true,
  "version": "1.0.0"
}
```

### 3. 在工作区中安装依赖

#### 3.1 安装全局依赖

在工作区根目录执行 `pnpm install <package-name>` 可以将依赖安装到工作区的根目录，供所有子项目共享。例如：

```bash
pnpm install typescript
```

#### 3.2 安装子项目特定依赖

如果某个子项目需要特定的依赖，可以**进入该子项目目录**，然后执行 `pnpm install <package-name>`。例如，进入 `packages/my-package` 目录安装依赖：

```bash
cd packages/my-package
pnpm install react
```

### 4. 子项目间的相互依赖

在工作区中，子项目可以方便地相互依赖。假设 `packages/my-package` 依赖 `packages/another-package`，可以在 `packages/my-package` 的 `package.json` 中添加依赖：

```json
{
  "name": "my-package",
  "dependencies": {
    "another-package": "workspace:*"
  }
}
```

`workspace:*` *表示使用工作区内 `another-package` 的最新版本*。

*添加依赖后，在 `packages/my-package` 目录执行 `pnpm install` 即可完成安装*。

### 5. 执行脚本

#### 5.1 单个子项目脚本

可以在子项目的 `package.json` 中定义脚本，然后**进入该子项目目录执行**。例如，在 `packages/my-package` 的 `package.json` 中定义一个 `build` 脚本：

```json
{
  "name": "my-package",
  "scripts": {
    "build": "tsc"
  }
}
```

在 `packages/my-package` 目录执行 `pnpm run build` 即可运行该脚本。

#### 5.2 批量执行脚本

pnpm 支持*在工作区根目录，批量执行所有子项目的脚本*。例如，要在所有子项目中执行 `build` 脚本，可以在工作区根目录执行：

```bash
pnpm -r run build
```

`-r` 表示递归执行，即对工作区内的所有子项目执行指定脚本。

### 6. 工作区的优势总结

- **性能优化**：通过共享依赖和并行执行脚本，提高了安装速度和构建效率。
- **代码复用**：方便子项目之间共享代码和功能，减少重复开发。
- **一致性**：确保所有子项目使用相同版本的依赖，避免版本冲突。
- **可维护性**：集中管理多个相关项目，简化了项目结构和开发流程。

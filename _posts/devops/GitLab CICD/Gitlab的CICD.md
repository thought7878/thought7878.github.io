GitLab CI/CD 是 GitLab 内置的持续集成、持续交付和持续部署工具。它与 GitLab 代码仓库深度集成，提供了从代码提交、自动化构建、测试到最终部署的完整自动化生命周期管理。

以下是对 GitLab CI/CD 的详细总结，分为**核心概念、工作原理、关键特性、高级功能及最佳实践**五个部分。

---

## 一、 核心概念与组件

理解 GitLab CI/CD 需要掌握以下几个核心概念：

### 1. 配置文件 (`.gitlab-ci.yml`)
这是 GitLab CI/CD 的“大脑”，*存放在项目根目录*。它使用 YAML 语法**定义了整个流水线的结构、阶段、任务、触发条件等**。

### 2. Pipeline（流水线）
流水线是 CI/CD 过程的完整实例。当代码被推送或触发合并请求（MR）时，GitLab 会创建一个 Pipeline。**它包含了构建、测试和部署代码所需的所有操作**。

### 3. Stage（阶段）
`Stage` 是 *Pipeline 的逻辑分组*（例如：`build`, `test`, `deploy`）。
* **执行顺序**：同一个 Pipeline 中的 Stage 是**按顺序串行**执行的。
* **状态传递**：只有当上一个 Stage 中的所有 Job 都成功时，下一个 Stage 才会开始。

### 4. Job（作业）
Job 是 Stage 中的具体执行单元（例如：`compile_code`, `run_unit_tests`）。
* **执行方式**：同一个 Stage 中的 Job 是**并行**执行的。
* **执行者**：Job 本身不执行代码，而是由 **Runner** 来执行。

### 5. Runner（运行器）
Runner 是实际执行 Job 的代理程序（Agent）。它轮询 GitLab 服务器，获取待执行的 Job 并运行。
* **Runner 类型**：
  * **Shared Runners**：GitLab 官方或管理员提供，所有项目共享。
  * **Group/Instance Runners**：在组或实例级别配置，供其下的所有项目使用。
  * **Specific Runners**：专门分配给特定项目的 Runner。
* **Executor（执行器）**：Runner 执行 Job 的环境，支持 `shell`, `docker`, `docker+machine`, `kubernetes` 等。推荐使用 `docker` 以保证环境隔离和一致性。

### 6. Artifacts（制品）与 Cache（缓存）
* **Artifacts**：用于在 Job 之间**传递构建结果**（如编译后的 jar 包、测试报告）。它们会被上传到 GitLab 服务器，可以在 UI 中下载，并传递给下游 Job。
* **Cache**：用于**加速构建过程**（如 `node_modules`, `.m2` 依赖）。它不保证跨 Job 传递，主要用于避免重复下载依赖。

---

## 二、 工作原理与流程

1. **触发**：开发者 Push 代码或创建/更新 Merge Request。
2. **解析**：GitLab Server 检测到 `.gitlab-ci.yml`，解析并验证配置。
3. **创建 Pipeline**：生成一个 Pipeline 实例，将其拆分为多个 Stages 和 Jobs。
4. **调度**：GitLab 将处于 `pending` 状态的 Job 分配给带有匹配 `tags` 且空闲的 Runner。
5. **执行**：Runner 拉取代码，配置环境（如拉取 Docker 镜像），执行 `script` 中的命令。
6. **反馈**：Runner 将执行日志和状态实时反馈给 GitLab Server。
7. **流转**：当前 Stage 的所有 Job 完成后，触发下一个 Stage。
8. **完成**：所有 Stage 执行完毕，Pipeline 标记为成功或失败，并触发后续动作（如发送通知、触发部署）。

---

## 三、 关键配置语法与特性

在 `.gitlab-ci.yml` 中，有几个非常关键的关键字：

### 1. 控制 Job 的执行时机 (`rules`)
官方推荐使用 `rules`（替代了已废弃的 `only/except`）来精确控制 Job 何时运行：
```yaml
job_name:
  script: echo "Hello"
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"' # 仅在 MR 时运行
    - if: '$CI_COMMIT_BRANCH == "main"' # 仅在 main 分支运行
    - changes:
        - "src/**/*.js" # 仅在特定文件修改时运行
```

### 2. 变量管理 (`variables`)
* **CI/CD 设置中的变量**：在 GitLab UI 中配置，支持 **Masked**（隐藏日志中的值）和 **Protected**（仅在受保护分支/标签运行）。
* **YML 中的变量**：分为全局变量和 Job 级别变量。
* **预定义变量**：GitLab 自动注入的变量，如 `$CI_COMMIT_SHA`, `$CI_PROJECT_DIR`, `$CI_PIPELINE_ID` 等。

### 3. 配置复用 (`include`)
避免在每个项目中重复编写相同的 CI 配置。支持引入本地文件、其他项目文件、远程 URL 或 GitLab 官方模板：
```yaml
include:
  - local: '/templates/.docker-build.yml'
  - project: 'my-group/shared-templates'
    file: '/security-scan.yml'
  - template: 'Security/SAST.gitlab-ci.yml' # 官方安全模板
```

---

## 四、 高级功能

### 1. 环境与部署 (Environments & Deployments)
* 定义部署目标环境（如 `development`, `staging`, `production`）。
* 支持**自动/手动部署**、**环境回滚**。
* 在 GitLab UI 中提供部署历史、环境监控（可集成 Prometheus/Grafana）和 Review Apps（为每个 MR 自动创建临时预览环境）。

### 2. 矩阵构建 (Matrix Builds)
允许在一个 Job 定义中运行多个变体，常用于跨平台/跨版本测试：
```yaml
test:
  script: run_tests.sh
  parallel:
    matrix:
      - DB: [postgres, mysql]
        NODE_VERSION: [14, 16, 18]
```
*这会生成 6 个并行的 Job。*

### 3. 多项目流水线 (Multi-project Pipelines)
适用于微服务架构或 Monorepo。
* **Parent-Child Pipelines（父子流水线）**：在一个项目中触发另一个 `.yml` 文件作为子流水线。
* **Downstream/Upstream Pipelines（上下游流水线）**：跨 GitLab 项目触发流水线，支持传递变量和等待下游完成。

### 4. GitLab Auto DevOps
开箱即用的 CI/CD 方案。只需开启，GitLab 会自动检测代码语言，自动构建、测试（使用 Herokuish 或 Buildpacks）、进行安全扫描，并部署到 Kubernetes 集群。

### 5. 安全与合规 (DevSecOps)
GitLab 将安全扫描深度集成到 Pipeline 中（主要在 Ultimate 版本，部分在 Premium/Free）：
* **SAST**：静态应用安全测试。
* **DAST**：动态应用安全测试。
* **Dependency Scanning**：依赖项漏洞扫描。
* **Container Scanning**：容器镜像漏洞扫描。
* **License Compliance**：开源许可证合规检查。

---

## 五、 最佳实践

1. **区分 Cache 和 Artifacts**：
   * 需要传给下一个 Job 或供人下载的（如编译产物、测试报告） -> 用 **Artifacts**。
   * 仅仅是为了下次构建更快的（如 npm 缓存、maven 依赖） -> 用 **Cache**。
2. **使用 Docker Executor**：尽量使用 Docker 作为 Runner 的执行器，确保构建环境的绝对一致性和隔离性，避免宿主机环境脏乱。
3. **保持 Job 的原子性**：一个 Job 只做一件事。如果 `build_and_test_and_deploy` 失败，很难排查。拆分为 `build`, `test`, `deploy` 更利于并行和定位问题。
4. **利用 `rules` 优化流水线速度**：不要每次都运行所有 Job。利用 `changes` 关键字，只在相关代码文件修改时才运行对应的测试或构建 Job。
5. **安全管理凭证**：绝对不要在 `.gitlab-ci.yml` 中硬编码密码或 Token。使用 GitLab CI/CD Variables，并开启 `Masked` 和 `Protected`。
6. **使用 CI/CD Catalog (组件化)**：将常用的 CI 逻辑封装成 GitLab CI 组件（Components），通过 `include: component` 引入，实现企业级的配置标准化和版本控制。
7. **设置超时和重试**：为不稳定的 Job（如需要调用外部 API 的测试）设置 `retry`，为所有 Job 设置合理的 `timeout` 防止僵尸任务。

## 总结

GitLab CI/CD 的最大优势在于 **“单一应用（Single Application）”** 理念。开发者不需要在代码仓库（Git）、CI 工具（Jenkins）、制品库（Nexus/Artifactory）和部署工具之间来回切换。它提供了从代码规划、编写、审查、构建、测试、安全扫描到部署监控的**一站式 DevOps 平台**，极大地降低了工具链集成的成本，提升了研发效能。
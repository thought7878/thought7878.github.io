

为你整理了一份**面向初学者的 Docker 知识体系图谱**。按学习逻辑分为 12 个模块，每个知识点仅用一句话解释，方便快速建立全局认知。建议按顺序边学边动手。

## 一、基础概念与架构
参考：[[Docker的概念|Docker的概念]]、[[Docker核心概念总结]]

- **Docker**：开源容器引擎，实现“一次构建，到处运行”，比虚拟机更轻量。
- **镜像（Image）**：只读模板，打包了应用代码、依赖库、环境变量和配置。
- **容器（Container）**：镜像的运行实例，是一个轻量级、隔离的进程空间。
- **仓库（Registry）**：集中存储和分发镜像的服务器（如 Docker Hub、阿里云）。
- **守护进程（Docker Daemon）**：后台服务 `dockerd`，负责管理镜像、容器、网络等核心对象。
- **客户端（Docker CLI）**：命令行工具 `docker`，用户通过它向 Daemon 发送指令。
- **容器运行时**：底层执行容器生命周期管理的组件（现代 Docker 默认使用 `containerd` + `runc`）。

## 二、安装与环境配置
- **系统支持**：Linux 原生运行；macOS/Windows 通过虚拟机或 WSL2 提供 Linux 内核。
- **Docker Desktop**：官方图形化套件，内置 Compose、K8s 单机版、可视化管理面板。
- **免 sudo 配置**：将当前用户加入 `docker` 用户组，避免每次命令加 `sudo`。
- **镜像加速器**：配置国内源（阿里云/腾讯云/中科大）大幅提升 `pull` 速度。
- **版本查看**：`docker version` 查客户端/服务端版本，`docker info` 查系统配置与状态。

## 三、镜像管理
- `docker pull`：从远程仓库下载镜像到本地。
- `docker images`：列出本地已下载的镜像及大小、创建时间。
- `docker rmi`：删除本地指定镜像（需先删除依赖它的容器）。
- `docker tag`：为镜像添加新标签（常用于版本命名或推送到私有仓库）。
- `docker save / load`：将镜像导出为 `.tar` 文件或从文件导入，适合离线传输。
- **镜像分层**：基于联合文件系统（UnionFS），每层只读，相同基础层可共享节省空间。
- **镜像 ID 与 Digest**：ID 为本地短标识，Digest 为全局唯一内容哈希，保证镜像完整性。

## 四、容器管理
- `docker run`：创建并启动容器（支持端口映射、挂载、环境变量等参数）。
- `docker ps`：查看运行中容器（加 `-a` 查看所有含已停止的容器）。
- `docker stop / start / restart`：优雅停止、启动或重启容器。
- `docker rm`：删除已停止容器（加 `-f` 可强制删除运行中容器）。
- `docker exec`：在运行中的容器内执行命令（如 `exec -it <id> bash` 进入终端）。
- `docker logs`：查看容器标准输出日志（加 `-f` 实时跟踪，`--tail` 查末尾）。
- **容器状态**：created → running → paused → stopped → removed，状态决定可执行的操作。

## 五、网络管理
- `docker network ls / inspect`：列出或查看网络详细配置。
- **bridge 网络**：默认桥接模式，容器通过虚拟网桥通信，需端口映射供外部访问。
- **host 网络**：容器直接使用宿主机网络栈，性能最高但端口易冲突。
- **none 网络**：完全隔离网络，仅保留回环接口，适合安全隔离场景。
- **自定义网络**：支持容器间通过服务名 DNS 解析互通，推荐多容器应用使用。
- **端口映射（-p）**：`-p 宿主机端口:容器端口`，实现外部流量转发到容器。
- **网络驱动扩展**：overlay（跨主机通信）、macvlan（直连物理网络）、ipvlan 等。

## 六、存储与数据卷
- **数据卷（Volume）**：Docker 管理的持久化目录，独立于容器生命周期，推荐首选。
- **绑定挂载（Bind Mount）**：将宿主机指定目录映射进容器，路径必须为绝对路径。
- **tmpfs 挂载**：数据仅保存在宿主机内存中，容器停止即清空，适合临时敏感数据。
- **卷共享**：多个容器挂载同一卷可实现数据读写共享或配置同步。
- `docker volume ls / create / rm`：查看、创建或删除数据卷。
- **数据备份**：通过临时容器挂载卷 + `tar` 命令实现卷内容打包与恢复。
- **存储驱动**：overlay2（默认）、aufs、btrfs 等，决定镜像层与容器层的文件系统实现。

## 七、Dockerfile 与镜像构建
- `FROM`：指定基础镜像（构建起点，通常选官方精简版）。
- `RUN`：在构建阶段执行命令（如安装软件），每层生成新镜像层。
- `COPY / ADD`：将本地文件复制到镜像中（`ADD` 额外支持自动解压和 URL 下载）。
- `WORKDIR`：设置后续指令的工作目录，不存在会自动创建。
- `EXPOSE`：声明容器运行时监听的端口（仅文档提示，实际需 `-p` 映射）。
- `CMD / ENTRYPOINT`：定义容器启动命令（`ENTRYPOINT` 为主命令，`CMD` 为默认参数）。
- `ENV`：设置环境变量，构建和运行时均可读取。
- `docker build`：根据 Dockerfile 构建镜像（`-t` 指定名称标签，默认启用 BuildKit）。
- **构建缓存**：命令顺序决定缓存命中率，频繁变更的指令尽量放在文件末尾。

## 八、Docker Compose 多容器编排
- **作用**：通过 YAML 文件一键定义、启动和管理多容器应用。
- `docker-compose.yml`：核心配置文件，包含服务、网络、卷、环境变量定义。
- `docker compose up`：创建并启动所有服务（加 `-d` 后台运行，`--build` 强制重构建）。
- `docker compose down`：停止并删除容器、网络、默认卷。
- `docker compose ps / logs / exec`：查看服务状态、聚合日志、进入指定服务容器。
- `depends_on`：声明启动依赖顺序（仅控制启动先后，不等待服务内部就绪）。
- **环境变量注入**：支持 `.env` 文件、`environment` 字段或 `env_file` 外部文件。
- **版本说明**：现代 Compose 已移除 `version` 字段，直接写配置即可。

## 九、安全与权限管理
- **非 root 运行**：容器内默认 root，Dockerfile 使用 `USER` 降权提升安全性。
- **镜像扫描**：使用 Trivy、Grype 等工具检测基础镜像与依赖漏洞。
- **资源限制**：`--memory`、`--cpus` 防止单个容器耗尽宿主机资源。
- **只读根文件系统**：`--read-only` 启动，配合卷写入日志/数据，防篡改。
- **Linux 能力（Capabilities）**：`--cap-drop ALL` 后按需 `--cap-add`，遵循最小权限原则。
- **密钥管理**：避免硬编码密码，生产环境使用 Docker Secrets 或 Vault/云 KMS。
- **安全基线**：定期更新基础镜像、禁用特权模式、限制网络暴露面。

## 十、日志、监控与调试
- **日志驱动**：默认 `json-file`，支持 `syslog`、`journald`、`fluentd`、`loki` 等转发。
- **日志轮转**：在 `daemon.json` 配置 `log-opts.max-size` 防止磁盘占满。
- **健康检查**：Dockerfile 中 `HEALTHCHECK` 定期探测服务状态，失败可自动重启。
- `docker stats`：实时查看容器 CPU、内存、网络、磁盘 IO 使用量。
- **监控生态**：cAdvisor 采集指标 → Prometheus 存储 → Grafana 可视化。
- **调试技巧**：`--privileged`（临时提权调试，生产禁用）、`nsenter` 进入容器命名空间。
- **核心转储**：容器崩溃时配置 `--oom-kill-disable` 或调整 `ulimit` 排查内存问题。

## 十一、高级特性与生态衔接
- **Docker Swarm**：Docker 原生集群方案，支持服务发现、负载均衡、滚动更新。
- **多阶段构建**：一个 Dockerfile 分阶段编译和打包，大幅减小最终镜像体积。
- **BuildKit**：新一代构建引擎，支持并行构建、安全缓存、Secret 挂载、语法高亮。
- **插件系统**：通过 Volume/Network/Log 插件扩展第三方存储或网络方案。
- **与 Kubernetes**：Docker 作为运行时已弃用，但镜像格式完全兼容，K8s 直接拉取使用。
- **容器运行时接口（CRI）**：K8s 通过 CRI 调用 containerd/CRI-O，Docker CLI 仅作为管理入口。
- **跨平台构建**：`docker buildx` 支持一次构建多架构镜像（amd64/arm64 等）。

## 十二、最佳实践与常见问题
- **镜像瘦身**：优先 Alpine/Debian-slim，清理 apt/yum 缓存，合并 RUN 指令。
- **避免容器内持久化数据**：数据库、日志、配置文件一律使用卷或绑定挂载。
- **标签规范**：生产环境禁用 `latest`，使用语义化版本（如 `1.2.3`）保证可追溯。
- **一层一事**：每个 `RUN` 专注单一任务，利用 `&&` 和 `\` 减少层数。
- **僵尸进程**：容器 PID 1 需能接管信号，建议使用 `tini` 或 Alpine 内置 init。
- **常见错误**：端口冲突、权限拒绝（SELinux/AppArmor）、网络不通、磁盘空间不足、构建缓存失效。
- **清理命令**：`docker system prune -a --volumes` 一键清理无用镜像/容器/网络/卷（慎用）。

## 管理Docker工具
管理Docker工具：Portainer & LazyDocker


---
💡 **初学者学习路线建议**：
1. 先掌握 `run / ps / exec / logs / pull / images` 等基础命令（第 3、4 模块）
2. 动手写 3 个简单 Dockerfile（Nginx、Python、Node），理解分层与缓存（第 7 模块）
3. 用 `docker compose` 跑一个 Web+DB 项目，熟悉多容器协作（第 8 模块）
4. 重点实践数据卷挂载与端口映射，理解“容器无状态，数据有状态”理念（第 5、6 模块）
5. 遇到报错先看 `docker logs` 和 `docker inspect`，90% 问题可自主定位

>Dockerfile 详解（初学者友好版）

`Dockerfile` 是**构建 Docker 镜像的蓝图**。*通过编写纯文本指令，定义镜像的每一层内容*。掌握 Dockerfile 是成为 Docker 熟练使用者的核心门槛。

---

## 一、核心概念
| 概念 | 说明 |
|------|------|
| **上下文（Build Context）** | 执行 `docker build .` 时，`.` 目录下的所有文件会被打包发送给 Docker 引擎（受 `.dockerignore` 过滤） |
| **分层构建（Layer）** | 每条指令生成一个只读层，相同层可跨镜像共享，节省磁盘与下载时间 |
| **构建命令** | `docker build -t 镜像名:标签 .`（`.` 表示使用当前目录作为上下文） |
| **声明式** | 只描述“最终状态”，不关心执行过程，保证可重复、可版本控制 |

---

## 二、指令分类详解

### 🔹 1. 基础与环境配置
| 指令 | 作用 | 关键提示 |
|------|------|----------|
| `FROM` | 指定基础镜像（必须放在第一行） | 推荐 `alpine`、`slim`、`distroless` 等精简版 |
| `WORKDIR` | 设置后续指令的工作目录 | 不存在会自动创建，**推荐用绝对路径** |
| `ARG` | 定义**构建期**变量 | 构建完成后消失，可用 `--build-arg` 传入 |
| `ENV` | 定义**构建期+运行期**环境变量 | 会持久化在镜像中，容器启动时可被覆盖 |
| `USER` | 切换容器内执行后续指令的用户 | 生产环境**必须降权**（避免 root 运行应用） |
| `SHELL` | 更改默认 shell（如 `["/bin/bash", "-c"]`） | 仅影响 `RUN`/`CMD`/`ENTRYPOINT` 的 shell 模式 |

### 🔹 2. 文件操作
| 指令 | 作用 | 关键提示 |
|------|------|----------|
| `COPY` | 将宿主机文件复制到镜像 | **优先使用**，支持通配符 `COPY *.jar /app/` |
| `ADD` | 功能同 COPY，但额外支持自动解压 tar、下载 URL | 行为复杂且可能引入安全风险，**不推荐日常使用** |
| `VOLUME` | 声明容器内挂载点（仅文档提示） | 实际挂载推荐在 `docker run` 或 Compose 中指定 |

### 🔹 3. 命令执行
| 指令 | 作用 | 关键提示 |
|------|------|----------|
| `RUN` | **构建期**执行命令（如安装依赖、编译） | 每执行一次生成新镜像层，**合并指令可瘦身** |
| `CMD` | **容器启动时**默认执行的命令 | 可被 `docker run` 覆盖，仅最后一条生效 |
| `ENTRYPOINT` | **容器启动时**固定执行的主程序 | 不易被覆盖，适合封装为可执行命令 |

> 💡 `CMD` 与 `ENTRYPOINT` 配合示例：
> ```dockerfile
> ENTRYPOINT ["java", "-jar"]
> CMD ["app.jar"]
> # docker run myapp → 执行 java -jar app.jar
> # docker run myapp v2.jar → 执行 java -jar v2.jar（覆盖 CMD）
> ```

### 🔹 4. 元数据与健康检查
| 指令 | 作用 | 关键提示 |
|------|------|----------|
| `EXPOSE` | 声明容器监听端口（仅文档作用） | **不会自动映射**，仍需 `docker run -p` |
| `HEALTHCHECK` | 定期探测容器是否健康 | 配合 Compose `depends_on: condition: service_healthy` 使用 |
| `LABEL` | 添加镜像元数据（作者、版本、许可证等） | 便于仓库管理与 CI/CD 追踪 |
| `STOPSIGNAL` | 指定停止容器时发送的信号 | 默认 `SIGTERM`，可改为 `SIGINT` 等 |
| `ONBUILD` | 当前镜像作为基础镜像时，触发后续指令 | 适合做“脚手架镜像”，日常开发少用 |

---

## 三、构建原理与缓存机制（重点）
Docker 构建是**自上而下逐层执行**的，每层是否命中缓存取决于：
1. 当前指令文本是否变化
2. 该指令依赖的上下文文件是否变化（如 `COPY` 的文件）

📌 **缓存优化铁律**：
- ✅ 把**不常变**的指令放前面（安装系统依赖、下载基础包）
- ✅ 把**常变**的指令放最后（复制业务代码、生成配置文件）
- ✅ 使用 `.dockerignore` 排除 `node_modules`、`.git`、`env` 文件，大幅加快构建

---

## 四、最佳实践（生产环境必看）

| 目标 | 做法 | 示例 |
|------|------|------|
| **镜像瘦身** | 多阶段构建 + 精简基础镜像 | 见下方模板，体积可降 70%~90% |
| **减少层数** | 合并 `RUN`，用 `&&` 和 `\` | `RUN apt update && apt install -y xxx && rm -rf /var/lib/apt/lists/*` |
| **安全运行** | 创建非 root 用户并切换 | `RUN addgroup -S app && adduser -S app -G app`<br>`USER app` |
| **版本固定** | 锁定基础镜像与依赖版本 | `FROM python:3.11.4-slim@sha256:abc...` |
| **清理缓存** | 在同一 `RUN` 层内清理 | `pip install --no-cache-dir -r requirements.txt` |
| **排除干扰** | 编写 `.dockerignore` | 类似 `.gitignore`，排除临时文件、IDE 配置等 |

---

## 五、生产级模板（Node.js 多阶段示例）
```dockerfile
# 阶段1：依赖安装与构建
FROM node:20-alpine AS builder
WORKDIR /build
COPY package*.json ./
RUN npm ci --only=production          # 仅安装生产依赖，利用缓存
COPY . .
RUN npm run build                     # 编译前端/TypeScript

# 阶段2：运行环境（最终镜像仅含运行时必要文件）
FROM node:20-alpine
WORKDIR /app
# 复制阶段1的构建产物与依赖
COPY --from=builder /build/package*.json ./
COPY --from=builder /build/node_modules ./node_modules
COPY --from=builder /build/dist ./dist

# 安全与元数据
RUN addgroup -S appuser && adduser -S appuser -G appuser
USER appuser
EXPOSE 3000
LABEL maintainer="you@example.com" version="1.0.0"

# 健康检查（可选但推荐）
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

# 启动命令（Exec 形式，确保 PID 1 正确接收信号）
CMD ["node", "dist/server.js"]
```
构建命令：`docker build -t myapp:1.0.0 .`

---

## 六、常见坑与调试指南

| 坑点 | 原因 | 解决 |
|------|------|------|
| 容器收不到 `Ctrl+C` 信号 | 使用了 Shell 形式 `CMD node app.js` | 改为 Exec 形式 `CMD ["node", "app.js"]` |
| 构建极慢 | 上下文过大或未用缓存 | 添加 `.dockerignore`，调整指令顺序 |
| 权限拒绝 | 以非 root 用户运行但卷挂载文件属主为 root | 运行时指定 `--user $(id -u):$(id -g)` 或 `chown` |
| 环境变量未生效 | 混淆 `ARG` 与 `ENV`，或在 `RUN` 中使用 `$VAR` 未 `export` | 构建期用 `ARG`，运行期用 `ENV` |
| 端口映射不生效 | 误以为 `EXPOSE` 会自动映射 | `EXPOSE` 仅声明，必须 `docker run -p 宿主:容器` |

🔍 **调试技巧**：
```bash
# 1. 禁用缓存重新构建（排查缓存污染）
docker build --no-cache -t debug:v1 .

# 2. 进入镜像临时调试
docker run --rm -it --entrypoint sh myapp:v1

# 3. 查看镜像分层与大小
docker history myapp:v1

# 4. 验证构建上下文大小（应 < 50MB）
du -sh .
```

---

## 📎 Dockerfile 指令速查表
| 指令 | 执行时机 | 是否生成新层 | 常用场景 |
|------|----------|--------------|----------|
| `FROM` | 构建开始 | 否 | 指定基础环境 |
| `RUN` | 构建期 | ✅ | 安装软件、编译代码 |
| `COPY/ADD` | 构建期 | ✅ | 复制文件到镜像 |
| `CMD` | 容器启动 | ❌ | 默认启动命令 |
| `ENTRYPOINT` | 容器启动 | ❌ | 固定主程序 |
| `ENV/ARG` | 构建/运行 | `ENV`✅ / `ARG`❌ | 传参与环境变量 |
| `EXPOSE` | 构建期 | ❌ | 文档声明端口 |
| `HEALTHCHECK` | 构建期 | ❌ | 健康探针 |
| `USER/WORKDIR` | 构建期 | ❌ | 目录与权限切换 |

---

✅ **Dockerfile 核心口诀**：  
🔹 顺序决定缓存，常变放最后，稳定放前面  
🔹 `COPY` 优于 `ADD`，`CMD` 必用 Exec 形式  
🔹 多阶段构建是瘦身王牌，非 root 运行是安全底线  
🔹 构建前必写 `.dockerignore`，排查必用 `--no-cache`  

需要我提供 **Python/Java/Go/Nginx 的官方最佳实践模板**，或详细讲解 **BuildKit 高级特性（缓存挂载、Secret 注入）**，随时告诉我！ 🛠️
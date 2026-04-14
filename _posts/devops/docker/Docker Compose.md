>Docker Compose 详解（初学者友好版）

Docker Compose 是 Docker 官方提供的**多容器编排工具**。*通过一个 YAML 文件定义应用所需的全部服务、网络、存储，实现“一键启动、一键销毁”*。

---

## 一、为什么需要 Compose？
| 场景 | 手动 `docker run` | `docker compose` |
|------|------------------|------------------|
| 启动 3 个服务（Web+DB+Cache） | 敲 3 长串命令，配网络/卷/环境变量 | 1 个 YAML + `docker compose up -d` |
| 服务间通信 | 需手动建网络、记 IP | 默认同网络，**服务名即主机名**自动 DNS 解析 |
| 环境隔离 | 易冲突、难复用 | 按目录隔离，天然支持 `dev/staging/prod` |
| 状态管理 | 需逐个 `stop/rm` | `docker compose down` 一键清理 |

> ✅ 适用：本地开发、测试环境、小型生产部署  
> ❌ 不适用：跨主机集群、自动扩缩容（需 Kubernetes / Docker Swarm）

---

## 二、核心概念与 YAML 骨架
Compose 文件默认命名 `compose.yml`（推荐）或 `docker-compose.yml`，严格使用 **2 空格缩进**。

```yaml
# 核心三大块
services:   # 定义容器（必须）
networks:   # 定义网络（可选）
volumes:    # 定义数据卷（可选）
```

> ⚠️ 重要变化：**Compose V2 已废弃 `version` 字段**，直接写配置即可，无需再写 `version: "3.8"`。

---

## 三、常用命令速查表
| 命令 | 作用 | 常用参数 |
|------|------|----------|
| `docker compose up -d` | 后台创建并启动所有服务 | `--build`（强制重构建）、`--no-deps`（不启动依赖） |
| `docker compose down` | 停止并删除容器、网络、默认卷 | `-v`（同时删除命名卷）、`--rmi all`（删除相关镜像） |
| `docker compose ps` | 查看服务状态与端口映射 | `-a`（含已停止）、`--format`（自定义输出） |
| `docker compose logs -f` | 实时查看所有服务日志 | `service_name`（指定服务）、`--tail=100` |
| `docker compose exec` | 进入运行中的容器 | `-it service_name sh` |
| `docker compose config` | 校验 YAML 并打印完整配置 | **排错神器**，自动展开变量与默认值 |
| `docker compose restart / stop` | 重启/停止指定服务 | 支持多服务名空格分隔 |

---

## 四、核心配置项详解（附示例）
```yaml
services:
  web:
    build: .                     # 使用当前目录 Dockerfile 构建
    image: myapp:v1              # 或直接用已有镜像（build/image 二选一）
    ports:
      - "8080:80"                # 宿主机8080 → 容器80
    volumes:
      - ./html:/usr/share/nginx/html  # 绑定挂载（开发热更新）
      - app-data:/data           # 命名卷（持久化数据）
    environment:
      DB_HOST: db                # 服务名自动解析为 IP
      DB_PASS: ${DB_PASSWORD}    # 从 .env 文件读取
    env_file:
      - .env                     # 批量注入环境变量
    depends_on:
      db:
        condition: service_healthy  # ✅ 等待健康检查通过后再启动
      redis:
        condition: service_started
    restart: unless-stopped      # 崩溃或重启后自动拉起
    networks:
      - frontend
      - backend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/"]
      interval: 10s
      timeout: 3s
      retries: 3
    deploy:
      resources:
        limits:
          cpus: "0.5"
          memory: 256M

networks:
  frontend:
  backend:
    driver: bridge               # 默认桥接，可换 overlay/macvlan

volumes:
  app-data:
    driver: local
```

### 🔑 关键语法说明
| 配置项 | 注意事项 |
|--------|----------|
| `ports` | 格式 `"宿主机:容器"`，宿主机端口冲突会启动失败 |
| `volumes` | `./path` 为相对路径（相对于 compose 文件所在目录） |
| `environment` | 支持 `${VAR}`、`${VAR:-default}`、`$$` 转义 `$` |
| `depends_on` | 仅控制启动顺序，**不保证服务内部就绪**！必须配 `healthcheck` 或应用层重试 |
| `restart` | `no`/`always`/`on-failure[:max-retries]`/`unless-stopped`（推荐） |
| `networks` | 同网络内服务可通过 `服务名` 直接访问，无需暴露端口到宿主机 |

---

## 五、实战：一键部署 Web + MySQL + Redis
### 1️⃣ 目录结构
```
myproject/
├── compose.yml
├── .env
├── app/
│   └── Dockerfile
└── init.sql
```

### 2️⃣ `compose.yml`
```yaml
services:
  app:
    build: ./app
    ports: ["3000:3000"]
    environment:
      DB_HOST: mysql
      REDIS_HOST: redis
      DB_PASS: ${MYSQL_ROOT_PASSWORD}
    depends_on:
      mysql:
        condition: service_healthy
      redis:
        condition: service_started
    restart: unless-stopped
    networks: [app-net]

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
    volumes:
      - mysql-data:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 5s
      timeout: 3s
      retries: 5
    networks: [app-net]

  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis-data:/data
    networks: [app-net]

networks:
  app-net:
    driver: bridge

volumes:
  mysql-data:
  redis-data:
```

### 3️⃣ `.env`（与 compose.yml 同级）
```env
MYSQL_ROOT_PASSWORD=mysecret123
COMPOSE_PROJECT_NAME=myapp  # 自定义项目名，影响资源前缀
```

### 4️⃣ 启动 & 验证
```bash
# 启动
docker compose up -d

# 查看状态
docker compose ps

# 进入应用容器测试连通性
docker compose exec app ping mysql
docker compose exec app ping redis

# 查看数据库日志
docker compose logs -f mysql
```

---

## 六、最佳实践与避坑指南
| 场景 | 推荐做法 | 常见错误 |
|------|----------|----------|
| 环境变量 | 用 `.env` + `env_file`，敏感信息不入代码 | 硬编码密码到 YAML |
| 服务就绪 | `depends_on` + `condition: service_healthy` | 以为 `depends_on` 会等数据库启动完成 |
| 数据持久化 | 显式声明 `volumes`，避免 Compose 自动创建隐藏卷 | 直接写 `./data:/var/lib/mysql` 导致权限问题 |
| 网络隔离 | 按功能划分网络（frontend/backend），最小化暴露 | 所有服务挤在默认网络 |
| 日志管理 | 配置 `logging: { driver: json-file, options: { max-size: "10m", max-file: "3" } }` | 日志占满磁盘 |
| 多环境 | 用 `-f docker-compose.prod.yml` 覆盖基础配置 | 为每个环境复制完整 YAML |
| 清理资源 | `docker compose down -v` 慎用！会删除数据卷 | 误删生产数据库 |

---

## 七、Compose V2 重要变化（必读）
| 特性 | V1（已淘汰） | V2（当前标准） |
|------|--------------|----------------|
| 命令格式 | `docker-compose`（独立 Python 包） | `docker compose`（Docker CLI 子命令） |
| `version` 字段 | 必须写 `version: "3"` | **已废弃**，直接写配置 |
| 性能 | 较慢，解析慢 | Go 重写，性能提升 10 倍+ |
| 新功能 | 无 | 支持 `watch`（热重载）、`include`、`profiles`、`secrets`、`buildx` 集成 |
| 安装 | `pip install docker-compose` | Docker Desktop / 包管理器自带，无需单独装 |

> 🔍 验证版本：`docker compose version`  
> 💡 旧项目迁移：删除 `version:` 行，将 `docker-compose` 替换为 `docker compose` 即可。

---

## 🎯 初学者实操小任务
```bash
# 1️⃣ 创建测试目录并写最小 compose.yml
mkdir compose-test && cd compose-test
echo 'services: { nginx: { image: nginx, ports: ["8080:80"] } }' > compose.yml

# 2️⃣ 启动并访问
docker compose up -d
curl localhost:8080  # 应返回 Nginx 欢迎页

# 3️⃣ 热更新配置
echo 'services: { nginx: { image: nginx, ports: ["8080:80"], volumes: ["./html:/usr/share/nginx/html"] } }' > compose.yml
mkdir html && echo "Hello Compose!" > html/index.html
docker compose up -d  # 自动重启并生效

# 4️⃣ 清理
docker compose down
rm -rf compose-test
```

---

✅ **Compose 核心口诀**：  
🔹 服务名即主机名，同网络直连无需 IP  
🔹 `depends_on` 只管启动顺序，就绪靠 `healthcheck`  
🔹 敏感信息用 `.env`，生产禁用 `latest` 标签  
🔹 显式声明网络/卷，避免隐藏资源失控  
🔹 排错三步：`config` 查语法 → `logs` 看日志 → `exec` 进容器  

需要我提供 **多环境配置模板（dev/test/prod）**、**Compose 与 CI/CD 集成示例**，或详细讲解 `docker compose watch`（热重载）用法，随时告诉我！ 🚀
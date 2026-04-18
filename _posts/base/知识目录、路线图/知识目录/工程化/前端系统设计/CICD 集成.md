
>对于典型的React的单页应用，如何 CI/CD 集成？如何将代码提交自动触发 lint、test、build、deploy，质量门禁前置？

>React SPA 的 CI/CD 集成实战指南（初学者友好版）

将 React 单页应用的 **lint → test → build → deploy** 自动化，并实现“质量门禁前置”，核心是**用配置代替人工检查、用流水线代替本地验证**。下面以 **GitHub Actions + Vite + pnpm** 为例，给出可直接落地的方案。

---
## 📐 一、流水线架构设计
| 阶段                | 职责              | 执行时机                            | 失败策略           |
| ----------------- | --------------- | ------------------------------- | -------------- |
| **Lint & Format** | 代码规范与格式化检查      | 每次 push/PR                      | 阻断合并，提示具体行号    |
| **Type Check**    | TypeScript 类型校验 | 依赖安装后                           | 阻断构建，避免运行时类型错误 |
| **Test**          | 单元/集成测试 + 覆盖率   | 与 Lint 并行执行                     | 低于阈值或失败则阻断     |
| **Build**         | 编译、压缩、生成静态资源    | 全部检查通过后                         | 失败则中止，不触发部署    |
| **Deploy**        | 推送至 CDN/静态托管平台  | Build 成功且目标分支为 `main`/`release` | 失败触发告警，支持一键回滚  |

✅ **设计原则**：检查前置、失败快返、缓存提速、环境隔离。

---
## 📜 二、完整 GitHub Actions 配置示例
创建 `.github/workflows/ci-cd.yml`，粘贴以下内容：
```yaml
name: React CI/CD

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

# 并发控制：同分支新提交自动取消旧流水线
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  ci-check:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [20]
    steps:
      - name: 📥 Checkout
        uses: actions/checkout@v4

      - name: 🟢 Setup pnpm & Node.js
        uses: pnpm/action-setup@v3
        with:
          version: 9
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'pnpm'

      - name: 📦 Install Dependencies
        run: pnpm install --frozen-lockfile

      - name: 🔍 Lint & Type Check
        run: pnpm run lint && pnpm run typecheck

      - name: 🧪 Run Tests
        run: pnpm run test --coverage
        env:
          CI: true

      - name: 🏗️ Build Production
        run: pnpm run build
        env:
          VITE_API_BASE: ${{ secrets.VITE_API_BASE }}

      - name: 📤 Upload Build Artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  cd-deploy:
    needs: ci-check
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: 🌐 Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```
> 💡 说明：  
> - `frozen-lockfile` 保证依赖版本绝对一致  
> - `CI: true` 让测试以非交互模式运行  
> - `needs: ci-check` 确保只有全部检查通过才部署  
> - 环境变量通过 `secrets` 注入，**绝不硬编码**

---
## 🔒 三、质量门禁前置策略（核心）
| 门禁层级 | 实现方式 | 拦截时机 |
|----------|----------|----------|
| **提交前** | `husky` + `lint-staged` 本地 pre-commit hook | 代码未 commit 时拦截，避免污染仓库 |
| **PR 合并前** | GitHub Branch Protection 规则：`Require status checks to pass` | 未通过 CI 的 PR 无法点击 Merge |
| **测试覆盖** | `vitest.config.ts` 配置 `coverage.threshold`（如 lines: 80%） | 覆盖率不足则测试失败，流水线阻断 |
| **构建产物** | `build` 阶段失败自动中止，不触发 `deploy` job | 避免将编译错误的代码推至线上 |
| **安全扫描** | 集成 `npm audit` / `trivy` / `github/dependabot` | 高危依赖漏洞阻断合并 |

📌 **配置分支保护路径**：`Settings → Branches → main → Protect matching branches`  
✅ 勾选：`Require pull request reviews before merging` + `Require status checks`

---
## 🚀 四、部署与回滚机制
| 场景 | 推荐方案 | 优势 |
|------|----------|------|
| **零配置部署** | Vercel / Netlify 绑定 GitHub 仓库 | 自动识别框架、Preview 环境、边缘 CDN |
| **自建部署** | GitHub Pages / AWS S3 + CloudFront / Nginx | 可控性强，适合企业内网或定制域名 |
| **环境隔离** | `dev` 分支 → Preview 环境，`main` → 生产环境 | 避免未验证代码影响线上用户 |
| **快速回滚** | 静态托管天然支持“保留历史构建”，一键切换上一个版本 | 无需重新编译，MTTR < 1 分钟 |
| **缓存失效** | Vite 自动为文件名添加 Hash（`index.[hash].js`） | 配合长效缓存策略，更新无感知 |

---
## 📊 五、监控与反馈闭环
| 环节 | 实践 | 价值 |
|------|------|------|
| **流水线通知** | 集成 Slack/钉钉/飞书 Webhook，失败时 @负责人 | 缩短故障响应时间 |
| **错误追踪** | 构建时注入 `SENTRY_DSN`，配合 `@sentry/react` 上报运行时异常 | 精准定位线上崩溃 |
| **性能门禁** | 接入 `Lighthouse CI`，PR 中自动对比 FCP/LCP/CLS 变化 | 防止性能退化随代码合并 |
| **Preview 部署** | Vercel/Netlify 为每个 PR 生成独立预览链接 | 产品/设计可提前验收，减少返工 |

---
## 💡 六、初学者避坑指南
| 坑点 | 原因 | 解决 |
|------|------|------|
| 流水线每次跑 10+ 分钟 | 未缓存 `node_modules`，重复安装依赖 | 使用 `actions/setup-node@v4` 内置 cache |
| 本地能跑，CI 报错 | 本地 Node 版本与 CI 不一致，或 `.gitignore` 遗漏 | 锁定 `.nvmrc` + `engines` 字段 |
| 部署后 404（SPA 路由） | Nginx 未配置 `try_files $uri /index.html` | 静态托管平台自动处理，自建需手动配置 |
| 环境变量泄露 | 将 `.env` 提交至仓库或公开在 PR 中 | 仅提交 `.env.example`，敏感值走平台 Secrets |
| 测试误报/漏报 | 未设置 `CI=true` 导致 Watch 模式挂起 | 在 CI 脚本中显式导出 `CI: true` |

---
## ✅ 核心口诀（便于记忆）
🔹 **本地拦截靠 Husky，云端拦截靠 CI**  
🔹 **Lint/Test 并行跑，缓存提速不能少**  
🔹 **Build 成功才 Deploy，失败快返不硬上**  
🔹 **环境变量走 Secrets，分支保护卡合并**  
🔹 **Preview 验收提效率，静态托管易回滚**

> 💡 给初学者的实践建议：  
> 1. 先跑通 `ci-check` job，确保 lint/test/build 全绿  
> 2. 再配置 Branch Protection，体验“门禁前置”效果  
> 3. 最后接入 Deploy，用 Vercel/Netlify 零配置验证全链路  
> 4. 每优化一步，记录流水线耗时，目标是 `< 3 分钟`

需要我提供 **`husky + lint-staged` 一键初始化脚本**，或针对 **私有化部署（Nginx/Docker）** 给出完整流水线配置，随时告诉我！ 🛠️
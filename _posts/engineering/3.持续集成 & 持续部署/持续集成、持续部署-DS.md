以下是关于**持续集成（Continuous Integration, CI）**和**持续部署（Continuous Deployment, CD）**的完整指南，涵盖核心概念、工具链、配置示例及最佳实践：

---

### **核心概念**
1. **持续集成（CI）**  
   - **定义**：频繁将代码变更合并到主分支，自动运行测试和构建。  
   - **目标**：快速发现集成错误，确保代码质量。  
   - **关键步骤**：  
     - 代码提交后触发自动化流程（测试、代码风格检查等）。  
     - 失败时阻止合并，成功时生成构建产物。

2. **持续部署（CD）**  
   - **定义**：自动化将通过CI的代码部署到生产环境。  
   - **目标**：快速、可靠地交付用户价值。  
   - **关键步骤**：  
     - 自动化部署到测试/预发布环境。  
     - 人工审核（可选）后自动发布到生产环境。

---

### **CI/CD工具链**
| 工具            | 用途                          | 示例平台       |
|-----------------|-------------------------------|---------------|
| **GitHub Actions** | 内置于GitHub的CI/CD流水线      | GitHub        |
| **GitLab CI**     | GitLab原生集成                | GitLab        |
| **Jenkins**       | 自托管、高度可定制            | 本地/云服务器  |
| **CircleCI**      | 云原生CI/CD服务               | CircleCI      |
| **Travis CI**     | 开源友好                      | Travis CI     |

---

### **CI/CD流程示例**
#### **React项目的典型流程**
```plaintext
1. 代码提交到仓库（如GitHub）
2. 触发CI流程：
   - 安装依赖（npm install）
   - 运行单元测试（React Testing Library）
   - 运行端到端测试（Cypress）
   - 构建生产包（npm run build）
3. CI通过后：
   - 部署到测试环境（如Vercel Preview）
   - 人工验收（可选）
4. 自动/手动触发CD部署到生产环境
```

---

### **配置示例**
#### **GitHub Actions（React项目）**
1. 创建文件 `.github/workflows/ci.yml`：
```yaml
name: CI Pipeline

on: [push, pull_request]

jobs:
  test-and-build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20.x

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test -- --watchAll=false

      - name: Build project
        run: npm run build

  deploy:
    needs: test-and-build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' # 仅主分支触发部署
    steps:
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
```

---

### **关键配置项**
1. **环境变量管理**：  
   - 敏感信息（API Key、Token）通过仓库的 **Secrets** 存储。  
   - 在GitHub中：`Settings > Secrets and variables > Actions`。

2. **依赖缓存**（加速流程）：  
   ```yaml
   - name: Cache node_modules
     uses: actions/cache@v3
     with:
       path: node_modules
       key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}
   ```

3. **并行任务**：  
   ```yaml
   jobs:
     test:
       runs-on: ubuntu-latest
     lint:
       runs-on: ubuntu-latest
   ```

---

### **最佳实践**
1. **快速反馈**：  
   - 将耗时长的任务（如端到端测试）拆分为独立Job，避免阻塞主流程。

2. **失败优先**：  
   - 优先运行关键路径测试（如单元测试），快速失败以节省资源。

3. **环境一致性**：  
   - 使用容器（Docker）确保CI环境与生产环境一致。

4. **部署策略**：  
   - **蓝绿部署**：同时运行新旧版本，逐步切换流量。  
   - **金丝雀发布**：向小部分用户发布新版本，监控稳定性。

5. **监控与回滚**：  
   - 集成Sentry/Datadog等工具监控生产环境。  
   - 自动化回滚机制（如通过Git标签触发旧版本部署）。

---

### **高级场景**
#### **多环境部署**
```yaml
- name: Deploy to Staging
  if: github.ref == 'refs/heads/dev'
  run: npm run deploy:staging

- name: Deploy to Production
  if: github.ref == 'refs/heads/main'
  run: npm run deploy:prod
```

#### **人工审批（GitHub Actions）**
```yaml
deploy-prod:
  needs: test-and-build
  environment: production
  runs-on: ubuntu-latest
  steps:
    - name: Wait for approval
      uses: trstringer/manual-approval@v1
      with:
        secret: ${{ secrets.APPROVAL_TOKEN }}
    - name: Deploy
      run: npm run deploy:prod
```

---

### **常见问题**
1. **依赖安装慢**：  
   - 使用缓存或换用国内镜像源（如`npmmirror.com`）。  

2. **测试覆盖率报告**：  
   - 集成`jest-coverage` + `Codecov`：  
     ```yaml
     - name: Upload coverage
       uses: codecov/codecov-action@v3
     ```

3. **多分支管理**：  
   - 通过分支规则限制部署范围（如仅`main`分支部署生产环境）。

---

通过CI/CD，团队可以实现从代码提交到生产发布的完全自动化，结合React Testing Library的测试覆盖，确保高质量交付。
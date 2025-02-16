**GitHub Actions** 是 GitHub 提供的*一种持续集成和持续部署（CI/CD）工具*，允许开发者*在代码仓库中直接定义自动化工作流*。通过 GitHub Actions，可以*实现从代码提交到构建、测试、部署的全流程自动化*，从而提高开发效率和交付速度。

以下是关于 **GitHub Actions** 的核心概念、使用方法和最佳实践：

---

### **1. 核心概念**
#### **(1) 工作流（Workflow）**
- **定义**：  
  工作流是一个自动化流程，定义了一系列任务（如构建、测试、部署），并由事件触发。
- **配置文件**：  
  工作流通过 YAML 文件定义，通常存放在 `.github/workflows` 目录下。
- **示例文件名**：
  ```plaintext
  .github/workflows/ci.yml
  ```

#### **(2) 事件（Event）**
- **定义**：  
  触发工作流的事件，例如代码推送（`push`）、拉取请求（`pull_request`）或定时任务（`schedule`）。
- **常见事件**：
  - `push`：当代码推送到仓库时触发。
  - `pull_request`：当创建或更新拉取请求时触发。
  - `workflow_dispatch`：手动触发工作流。

#### **(3) 作业（Job）**
- **定义**：  
  工作流中的一个独立任务，通常运行在一个虚拟环境中。
- **特点**：
  - 每个作业可以运行在不同的操作系统（如 Ubuntu、Windows、macOS）。
  - 作业之间可以通过依赖关系进行协调。

#### **(4) 步骤（Step）**
- **定义**：  
  作业中的具体操作步骤，可以是运行脚本、调用操作（Action）或设置环境变量。
- **常见步骤**：
  - 检出代码：`actions/checkout`
  - 安装依赖：`npm install` 或 `pip install`
  - 运行测试：`npm test`

#### **(5) 操作（Action）**
- **定义**：  
  *可重用的代码片段*，用于*完成特定任务*（如检出代码、发布包）。
- **官方操作**：
  - `actions/checkout`：检出代码。
  - `actions/setup-node`：设置 Node.js 环境。
- **自定义操作**：  
  开发者可以创建自己的操作，并发布到 GitHub Marketplace。

---

### **2. 配置文件结构**
GitHub Actions 的工作流文件采用 YAML 格式，以下是一个完整的示例：
```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '16'

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: echo "Deploying to production..."
```

---

### **3. 常见功能**
#### **(1) 构建与测试**
- 使用 GitHub Actions 自动化构建和测试流程：
  ```yaml
  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Set up Node.js
          uses: actions/setup-node@v3
          with:
            node-version: '16'
        - name: Install dependencies
          run: npm install
        - name: Run tests
          run: npm test
  ```

#### **(2) 部署**
- 将应用部署到云平台（如 AWS、Azure）或容器化平台（如 Kubernetes）：
  ```yaml
  jobs:
    deploy:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v3
        - name: Deploy to AWS
          run: |
            aws s3 cp ./build s3://my-bucket --recursive
  ```

#### **(3) 定时任务**
- 使用 `cron` 表达式运行定时任务：
  ```yaml
  on:
    schedule:
      - cron: '0 0 * * *' # 每天午夜运行
  jobs:
    nightly-build:
      runs-on: ubuntu-latest
      steps:
        - name: Run nightly task
          run: echo "Running nightly task..."
  ```

#### **(4) 手动触发**
- 允许手动触发工作流：
  ```yaml
  on:
    workflow_dispatch:
  jobs:
    manual-job:
      runs-on: ubuntu-latest
      steps:
        - name: Manual task
          run: echo "This job was triggered manually."
  ```

---

### **4. 常见操作（Actions）**
#### **(1) 检出代码**
参考：[[actions checkout]]
- 使用 `actions/checkout` 操作检出代码：
  ```yaml
  - uses: actions/checkout@v3
  ```

#### **(2) 设置环境**
- 设置语言环境（如 Node.js、Python、Java）：
  ```yaml
  - uses: actions/setup-node@v3
    with:
      node-version: '16'
  ```

#### **(3) 发布包**
- 发布到 npm 或 PyPI：
  ```yaml
  - name: Publish to npm
    run: npm publish
    env:
      NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
  ```

#### **(4) 部署到云**
- 部署到 AWS S3：
  ```yaml
  - name: Deploy to S3
    run: |
      aws s3 cp ./build s3://my-bucket --recursive
    env:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  ```

---

### **5. 优势**
#### **(1) 无缝集成**
- GitHub Actions 与 GitHub 仓库深度集成，无需额外配置即可使用。

#### **(2) 多样化支持**
- 支持多种编程语言、框架和平台。

#### **(3) 社区生态**
- GitHub Marketplace 提供了大量现成的操作，满足各种需求。

#### **(4) 灵活性**
- 可以根据项目需求自定义工作流。

---

### **6. 最佳实践**
#### **(1) 分离关注点**
- 将构建、测试和部署分成不同的作业，便于维护和扩展。

#### **(2) 使用缓存**
- 缓存依赖项以加速构建过程：
  ```yaml
  - name: Cache dependencies
    uses: actions/cache@v3
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
  ```

#### **(3) 保护敏感信息**
- 使用 GitHub Secrets 存储敏感数据（如 API 密钥、密码）：
  ```yaml
  env:
    API_KEY: ${{ secrets.API_KEY }}
  ```

#### **(4) 定期清理日志**
- 删除旧的工作流运行记录，避免占用存储空间。

---

### **7. 示例场景**
#### **(1) React 应用的 CI/CD**
```yaml
name: React CI/CD

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '16'
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
      - name: Build application
        run: npm run build

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to Netlify
        run: |
          npm install -g netlify-cli
          netlify deploy --prod
        env:
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
```

#### **(2) Python 包的发布**
```yaml
name: Publish Python Package

on:
  release:
    types: [created]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install setuptools wheel twine
      - name: Build package
        run: python setup.py sdist bdist_wheel
      - name: Publish to PyPI
        run: twine upload dist/*
        env:
          TWINE_USERNAME: ${{ secrets.PYPI_USERNAME }}
          TWINE_PASSWORD: ${{ secrets.PYPI_PASSWORD }}
```

---

### **8. 总结**
GitHub Actions 是一种强大且灵活的 CI/CD 工具，能够帮助开发者轻松实现自动化构建、测试和部署流程。它与 GitHub 仓库无缝集成，提供了丰富的社区生态和多样化的功能支持。通过合理配置和优化，GitHub Actions 可以显著提高开发效率，缩短交付周期，并为团队提供可靠的自动化解决方案。
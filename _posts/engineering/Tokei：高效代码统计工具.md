# Tokei：高效代码统计工具完全指南

`tokei` 是一款**用 Rust 编写的超高速代码统计工具**，专为开发者设计，用于快速分析项目代码规模。相比传统工具（如 cloc），它在**速度、准确性和用户体验**上都有显著优势，特别适合前端/Webpack 项目分析。

---

## 为什么选择 Tokei？（vs cloc）

| 特性                | Tokei                          | cloc                           |
|---------------------|--------------------------------|--------------------------------|
| **速度**            | ⚡ **快 3-5 倍** (Rust 编写)    | 较慢 (Perl 编写)               |
| **语言支持**        | ✅ **200+ 语言** (含现代前端)   | ❌ 仅 150+ 语言                |
| **输出格式**        | 🎨 **彩色终端输出 + JSON/CSV**  | 黑白文本为主                   |
| **忽略规则**        | 🧠 **智能忽略** (自动识别)      | 需手动配置                     |
| **内存占用**        | 📉 **更低** (流式处理)         | 较高                           |
| **前端友好度**      | ✅ **完美支持 TSX/JSX/Sass**    | ❌ 部分前端语法识别不佳        |

> 💡 **关键优势**：对现代前端项目（React/Vue + TypeScript + Webpack）的统计**更精准**，且**无需复杂配置**即可自动忽略 `node_modules` 等目录。

---

## 安装 Tokei（30秒完成）

### macOS / Linux
```bash
# 通过 Cargo (Rust 包管理器)
cargo install tokei

# 或通过 Homebrew (macOS)
brew install tokei

# 或通过 Snap (Linux)
sudo snap install tokei
```

### Windows
```powershell
# 通过 Scoop
scoop install tokei

# 或通过 Chocolatey
choco install tokei
```

### 验证安装
```bash
tokei -V
# 输出: tokei 12.1.2
```

> ✅ **安装成功标志**：终端输入 `tokei` 后显示帮助信息

---

## 基础用法（前端项目实操）

### 1. 快速统计当前项目（自动忽略标准目录）
```bash
tokei
```
**输出示例**：
```
-------------------------------------------------------------------------------
 Language            Files        Lines         Code     Comments       Blanks
-------------------------------------------------------------------------------
 TypeScript             42          8,200        6,500          900          800
 JavaScript             15          3,100        2,400          400          300
 JSON                   10            500          500            0            0
 CSS                     8            900          700          100          100
 Markdown                5            400            0          300          100
-------------------------------------------------------------------------------
 Total                 79         13,100       10,100        1,700        1,300
-------------------------------------------------------------------------------
```

### 2. 仅统计核心源码目录（Webpack 项目必备）
```bash
# 专注分析 src 目录（排除测试/配置文件）
tokei src/ --exclude test,__tests__,utils
```

### 3. 生成机器可读报告（用于 CI/监控）
```bash
# 输出 JSON 格式（可被脚本解析）
tokei --output json > report.json

# 输出 CSV 格式（Excel 可打开）
tokei --output csv > report.csv
```

---

## 高级技巧（Webpack 项目优化必备）

### 1. 精准定位大文件（优化切入点）
```bash
tokei --sort lines --format markdown
```

**输出关键信息**：

| Language   | File Path               | Lines | Code  | Comments | Blanks |
|------------|-------------------------|-------|-------|----------|--------|
| TypeScript | src/core/compiler.ts    | 2,450 | 1,980 | 320      | 150    |
| JavaScript | src/polyfills/index.js  | 1,870 | 1,520 | 200      | 150    |

> 💡 **优化建议**：`compiler.ts` 是核心瓶颈，考虑代码分割或算法优化

### 2. 对比不同分支的代码变化（性能优化验证）
```bash
# 在优化前分支
tokei --output json > before.json

# 切换到优化后分支
git checkout perf-optimization
tokei --output json > after.json

# 使用 diff 工具对比
json-diff before.json after.json
```

### 3. 自定义忽略规则（Webpack 项目特供）
```bash
# 忽略 Webpack 生成的文件 + 前端特定目录
tokei . \
  --exclude "dist,build,.cache,__snapshots__,stories" \
  --no-ignore \
  --vcs git
```
- `--no-ignore`：禁用默认忽略规则（需手动控制）
- `--vcs git`：尊重 `.gitignore` 规则

### 4. 监控代码健康度（CI 集成示例）
```yaml
# .github/workflows/quality.yml
jobs:
  quality:
    steps:
      - name: Install Tokei
        run: cargo install tokei
        
      - name: Run Tokei
        run: |
          tokei --output json > tokei.json
          echo "Total LOC: $(cat tokei.json | jq '.Total.code')"
          
      - name: Check LOC Threshold
        run: |
          LOC=$(cat tokei.json | jq '.Total.code')
          if [ $LOC -gt 15000 ]; then
            echo "❌ Codebase exceeded 15k LOC limit!"
            exit 1
          fi
```

---

## 实际案例：分析 Webpack 项目

### 场景：优化一个 React + TypeScript 项目
```bash
# 1. 基准测量
tokei src/ --format json > baseline.json

# 2. 输出关键指标
cat baseline.json | jq '.Total | {code, lines}'
# {
#   "code": 8421,
#   "lines": 11023
# }

# 3. 优化后重新测量
npm run optimize-webpack-config
tokei src/ --format json > optimized.json

# 4. 对比差异
json-diff baseline.json optimized.json | jq '.Total.code'
# -8421
# +8100  # 减少 321 行（通过 Tree Shaking 移除未用代码）
```

### 发现的问题与优化：
1. **问题**：`node_modules` 中的 `lodash` 被完整引入（2000+行）
   ```bash
   tokei node_modules/lodash
   ```
2. **解决方案**：改用 `lodash-es` + Webpack 的 `sideEffects: false`
3. **验证**：
   ```bash
   # 优化前
   tokei node_modules/lodash | grep -A 2 JavaScript
   # JavaScript  1  5750  4800  750  200
   
   # 优化后（仅引入需要的模块）
   tokei node_modules/lodash-es | grep -A 2 JavaScript
   # JavaScript  3  300  250  30  20
   ```

---

## 📊 Tokei vs Webpack 构建分析（双重验证）

| 分析维度       | Tokei (源码层)                | Webpack Bundle Analyzer (产物层) |
|----------------|-------------------------------|----------------------------------|
| **统计对象**   | 开发时源代码                  | 构建后打包产物                   |
| **关键指标**   | 有效代码行数 (LOC)            | Chunk 体积 / 模块依赖关系        |
| **优化方向**   | 减少冗余代码 / 逻辑复杂度     | 优化打包策略 / 资源加载          |
| **执行时机**   | 开发阶段 / PR 审查            | 构建阶段                         |
| **典型命令**   | `tokei src/`                  | `ANALYZE=true npm run build`     |

> ✅ **最佳实践**：  
> **Tokei** 告诉你 *源码是否精简* → **Webpack Analyzer** 告诉你 *打包是否高效*  
> 二者结合才能实现**端到端优化**

---

## 🚫 常见问题解决

### Q：统计结果包含 `node_modules`？
**原因**：未正确排除目录  
**修复**：
```bash
# 方法1：使用默认忽略规则（推荐）
tokei

# 方法2：手动指定排除
tokei . --exclude node_modules,dist,build
```

### Q：TypeScript 文件被识别为 JavaScript？
**原因**：Tokei 按文件扩展名识别  
**修复**：
```bash
# 显式指定语言（强制 TSX 识别）
tokei src/ --language TypeScript,TSX
```

### Q：统计速度仍然很慢？
**优化方案**：
```bash
# 1. 限制深度
tokei src/ --max-depth 3

# 2. 跳过大型文件 (>500KB)
tokei src/ --skip-large 500
```

---

## 进阶：将 Tokei 集成到前端工作流

### 1. 在 `package.json` 中添加质量检查
```json
{
  "scripts": {
    "loc": "tokei src/ --sort lines",
    "loc:ci": "tokei src/ --output json | jq '.Total.code'"
  }
}
```

### 2. 生成可视化报告（配合 GitHub Actions）
```yaml
# .github/workflows/loc.yml
name: Code Metrics
on: [push]

jobs:
  report:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install Tokei
        run: cargo install tokei
        
      - name: Generate Report
        run: |
          tokei --output markdown > LOC.md
          echo "## Code Size Report" > SUMMARY.md
          echo "Generated at $(date)" >> SUMMARY.md
          cat LOC.md >> SUMMARY.md
      
      - name: Publish Report
        uses: stefanzweifel/git-auto-commit-action@v4
        with:
          commit_message: "🤖 Update code metrics report"
```

### 3. 监控技术债务（示例）
```bash
# 检测注释比例过低的文件（潜在技术债务）
tokei src/ --format json | jq '
  .reports[] | 
  select(.stats.total.comments / .stats.total.lines < 0.1) |
  {path, comment_ratio: (.stats.total.comments / .stats.total.lines)}
'
```
**输出**：
```json
{
  "path": "src/core/compiler.ts",
  "comment_ratio": 0.08
}
```
> 💡 **行动建议**：该文件注释率仅 8%，需补充文档

---

## 💡 终极建议：Tokei + Webpack 黄金组合

1. **开发阶段**  
   ```bash
   # 监控新增代码量（避免过度设计）
   tokei src/ --after $(git log -1 --pretty=format:%H)
   ```

2. **PR 审查**  
   ```bash
   # 仅统计本次修改的代码
   git diff main | tokei --diff
   ```

3. **性能优化验证**  
   ```bash
   # 优化前
   tokei src/ > before.loc
   npm run build
   
   # 优化后
   tokei src/ > after.loc
   npm run build:optimized
   
   # 对比报告
   diff before.loc after.loc
   du -sh dist/  # 产物体积对比
   ```

---

## 总结：为什么前端工程师需要 Tokei

| 场景               | Tokei 的价值                              |
| ---------------- | -------------------------------------- |
| **评估项目复杂度**      | 5 秒内知道项目核心代码量（比 GitHub 语言统计准确 10 倍）    |
| **Webpack 优化验证** | 量化 Tree Shaking/代码分割效果（源码层 vs 产物层双重验证） |
| **技术债务管理**       | 识别注释不足/复杂度过高的文件                        |
| **团队协作规范**       | 在 CI 中设置 LOC 门槛，防止代码膨胀                 |
| **性能优化决策**       | 精准定位大文件（如 `compiler.ts` 2450 行 → 优先优化） |

> ✨ **一句话总结**：  
> **`tokei` 是前端工程师的"代码显微镜"——它让你看清源码的真实规模，为 Webpack 优化提供精准靶点。**  
> 安装只需 30 秒，但能持续为你的项目健康度保驾护航！

立即尝试：  
```bash
cargo install tokei && tokei
```
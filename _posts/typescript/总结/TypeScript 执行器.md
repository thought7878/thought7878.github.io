参考：[[5.开发效率加倍：typescript运行时比较]]

# TypeScript 执行器深度对比 (TypeScript Executors)

TypeScript 执行器是指**无需手动编译**，直接在 Node.js 环境（或其他运行时）中运行 `.ts` 文件的工具。它们的核心任务是**转译（Transpile）**，将 TS 语法转换为 JS 以便执行。

> ⚠️ **核心认知**：绝大多数执行器**只做转译，不做类型检查**。类型检查需单独运行 `tsc --noEmit`。

---

## 📊 核心指标对比总览

| 特性 | **tsx** | **ts-node** | **swc-node** | **Bun** | **Deno** |
|------|---------|-------------|--------------|---------|----------|
| **底层引擎** | esbuild (Go) | TypeScript (JS) | SWC (Rust) | Zig/Bun | Rust |
| **启动速度** | 🚀 极快 | 🐢 慢 | 🚀 极快 | 🚀 极快 | 🚀 极快 |
| **类型检查** | ❌ 无 | ✅ 有 (可配置) | ❌ 无 | ❌ 无 | ❌ 无 |
| **ESM 支持** | ✅ 优秀 | ⚠️ 复杂 (需配置) | ✅ 好 | ✅ 原生 | ✅ 原生 |
| **Watch 模式** | ✅ 内置 (`--watch`) | ✅ (需 ts-node-dev) | ❌ 需外部工具 | ✅ 内置 | ✅ 内置 |
| **生产环境** | ⚠️ 不推荐 | ❌ 不推荐 | ⚠️ 特定场景 | ✅ 可部署 | ✅ 可部署 |
| **配置兼容** | 高 (tsconfig) | 最高 (原生) | 高 | 中 | 中 (deno.json) |
| **社区热度** | 🔥 极高 (当前首选) | 🟡 稳定 (经典) | 🟢 高 (框架集成) | 🔥 上升 | 🟢 稳定 |

---

## 🔍 主流工具深度解析

### 1️⃣ tsx (当前社区首选) 🌟

基于 `esbuild` 的现代执行器，旨在替代 `ts-node`。

```bash
# 安装
npm install -g tsx

# 运行
tsx app.ts

# 监听模式 (替代 nodemon + ts-node)
tsx --watch app.ts
```

| 优点 | 缺点 |
|------|------|
| ✅ **速度极快** (esbuild 底层) | ❌ **无类型检查** (仅转译) |
| ✅ **ESM/CJS 无缝支持** | ❌ 某些复杂 TS 特性支持不如 tsc 完美 |
| ✅ **内置 Watch 模式**，无需 nodemon | ❌ 生产环境建议编译为 JS |
| ✅ 零配置，开箱即用 | |

**适用场景**：本地开发、CLI 脚本、测试运行、快速原型。

---

### 2️⃣ ts-node (经典标准)

基于官方 TypeScript 编译器 (`tsc`) 的执行器。

```bash
# 安装
npm install -D ts-node typescript

# 运行
npx ts-node app.ts

# 开启类型检查 (默认开启，但慢)
# 关闭类型检查 (加速)
npx ts-node --transpile-only app.ts
```

| 优点 | 缺点 |
|------|------|
| ✅ **类型检查** (默认开启，安全) | ❌ **速度慢** (尤其是大型项目) |
| ✅ **兼容性最好** (完全遵循 tsc) | ❌ ESM 支持配置复杂 (`--esm`) |
| ✅ 调试体验好 (Source Map 精准) | ❌ 内存占用高 |
| ✅ 生态成熟 (IDE 集成好) | ❌ 监听需配合 `ts-node-dev` (已维护较少) |

**适用场景**：需要严格类型检查的开发环境、调试复杂 TS 特性、老项目维护。

---

### 3️⃣ swc-node (@swc-node/register)

基于 Rust 编写的 SWC 编译器的 Node 绑定。常被框架内部使用。

```bash
# 安装
npm install -D @swc-node/register @swc/core

# 运行
node -r @swc-node/register app.ts
```

| 优点 | 缺点 |
|------|------|
| ✅ **性能优异** (Rust 底层) | ❌ 命令行体验不如 tsx 友好 |
| ✅ **框架集成好** (NestJS, Next.js) | ❌ 配置相对复杂 (需 `.swcrc`) |
| ✅ 内存占用低 | ❌ 单独作为 CLI 工具使用较少 |
| ✅ 支持 JSX/TSX | |

**适用场景**：大型项目、Jest 测试加速、框架内部集成 (如 NestJS)。

---

### 4️⃣ Bun (新一代运行时) 🚀

不仅仅是执行器，是替代 Node.js 的完整运行时，原生支持 TS。

```bash
# 安装
curl -fsSL https://bun.sh/install | bash

# 运行 (无需任何配置)
bun run app.ts
```

| 优点 | 缺点 |
|------|------|
| ✅ **原生支持 TS** (无需配置 loader) | ❌ 生态兼容性 (npm 包可能有坑) |
| ✅ **速度最快** (启动 + 执行) | ❌ 生产环境稳定性待验证 (虽已稳定) |
| ✅ 内置包管理、测试、格式化 | ❌ Windows 支持仍在完善 |
| ✅ 兼容大部分 Node.js API | |

**适用场景**：追求极致性能、全栈新项目、愿意尝试新技术的团队。

---

### 5️⃣ Deno (安全运行时)

Node.js 原作者开发，原生支持 TS，注重安全。

```bash
# 运行
deno run app.ts
```

| 优点 | 缺点 |
|------|------|
| ✅ **原生 TS 支持** | ❌ 生态独立 (不完全兼容 Node.js) |
| ✅ **安全性高** (权限控制) | ❌ 学习曲线 (新 API) |
| ✅ 内置工具链 (lint, fmt, test) | ❌ 迁移成本高 |

**适用场景**：安全敏感项目、边缘计算、新架构探索。

---

## ⚔️ 关键维度深度 PK

### 1. 速度对比 (启动 + 执行)
```
Bun ≈ tsx ≈ swc-node  >>>  ts-node (默认) > ts-node (--transpile-only)
```
> 💡 **数据**：对于中型项目，tsx 启动速度通常比 ts-node 快 5-10 倍。

### 2. 类型检查能力
| 工具 | 类型检查 | 建议方案 |
|------|----------|----------|
| **ts-node** | ✅ 内置 (可关闭) | 开发时开启，CI 中强制 |
| **tsx** | ❌ 无 | 配合 `tsc --noEmit` 在 CI 运行 |
| **swc-node** | ❌ 无 | 配合 `tsc --noEmit` |
| **Bun** | ❌ 无 | 配合 `tsc --noEmit` 或 `bun typecheck` |

> ⚠️ **重要**：生产环境不要依赖执行器的类型检查！应在 CI/CD 流程中单独运行 `tsc --noEmit`。

### 3. ESM (ES Modules) 支持
- **tsx**: 处理得最好，自动处理 `.js` 扩展名问题。
- **ts-node**: 需要复杂配置 (`"esm": true`, `--loader`)，容易踩坑。
- **Bun/Deno**: 原生支持，最省心。

### 4. 生产环境可用性
| 工具 | 生产环境建议 | 理由 |
|------|--------------|------|
| **ts-node** | ❌ 禁止 | 性能太差，内存占用高 |
| **tsx** | ⚠️ 不推荐 | 虽快但非标准，调试困难 |
| **swc-node** | ⚠️ 特定场景 | 如 Serverless 冷启动优化 |
| **Bun** | ✅ 推荐 | 性能优异，但需评估生态兼容性 |
| **编译后 JS** | ✅ 最佳实践 | `tsc` 编译为 JS + Node.js 运行 |

---

## 🧭 选型决策指南

```
                    项目需求？
                         ↓
        ┌────────────────────────────────┐
        ↓                ↓                ↓
   追求开发速度？    需要类型检查？    替换 Node 运行时？
        ↓                ↓                ↓
   tsx (首选)        ts-node (默认)      Bun / Deno
        ↓                ↓                ↓
   配合 tsc 检查      关闭 transpileOnly  评估生态兼容性
```

### 具体场景推荐

| 场景 | 推荐工具 | 理由 |
|------|----------|------|
| **日常开发 (Node.js)** | **tsx** | 速度快，ESM 支持好，内置 watch |
| **调试复杂 TS 问题** | **ts-node** | 类型检查帮助定位问题，Source Map 准 |
| **Jest 测试加速** | **swc-node** | `@swc-node/jest` 比 ts-jest 快得多 |
| **CLI 脚本工具** | **tsx** | 分发方便，用户无需编译 |
| **生产环境 (传统)** | **tsc + Node** | 最稳定，生态兼容最好 |
| **生产环境 (激进)** | **Bun** | 性能极致，全栈一体化 |
| **NestJS 项目** | **ts-node/swc** | 框架官方支持好 |

---

## 🛠️ 最佳实践组合方案

### 方案 1：现代 Node.js 开发 (推荐)
```json
// package.json
{
  "scripts": {
    "dev": "tsx --watch src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "type-check": "tsc --noEmit"
  }
}
```
- **开发**：`tsx` 提供极速体验。
- **类型安全**：CI 中运行 `type-check`。
- **生产**：编译为 JS 运行，保证稳定性。

### 方案 2：全栈 Bun 项目
```json
{
  "scripts": {
    "dev": "bun run --watch src/index.ts",
    "start": "bun run src/index.ts"
  }
}
```
- **开发 + 生产**：直接用 Bun 运行 TS，无需编译步骤。
- **类型检查**：`bun typecheck`。

### 方案 3：大型 enterprise 项目
```json
{
  "scripts": {
    "dev": "node -r @swc-node/register src/index.ts",
    "test": "jest",
    "build": "tsc"
  }
}
```
- **开发**：`swc-node` 平衡性能与兼容性。
- **测试**：配合 SWC 加速 Jest。

---

## ⚠️ 常见陷阱与注意事项

1.  **`.js` 扩展名问题**
    - ESM 中 `import` 必须带 `.js` 后缀，即使源文件是 `.ts`。
    - `tsx` 会自动处理，`ts-node` 可能需要配置。

2.  **类型检查缺失**
    - 用 `tsx` 开发时，代码有类型错误也能运行！
    - **解决**：IDE 会报错，但务必在 CI 中加 `tsc --noEmit`。

3.  **生产环境性能**
    - 不要直接用 `tsx` 跑生产！虽然快，但每次启动都要转译。
    - **解决**：生产环境务必 `tsc` 编译成 `.js`。

4.  **CommonJS 互操作**
    - `tsx` 对 ESM/CJS 互操作支持较好。
    - `ts-node` 在 ESM 模式下配置繁琐 (`"module": "NodeNext"`).

5.  **环境变量加载**
    - `tsx` 自动加载 `.env` 文件。
    - `ts-node` 需配合 `dotenv` 手动加载。

---

## 📌 一分钟速查表

```
┌─────────────────────────────────────────────────────────┐
│  📌 TypeScript 执行器速查                                │
├─────────────────────────────────────────────────────────┤
│  日常开发     → tsx --watch (最快最方便)                │
│  调试检查     → ts-node (带类型检查)                    │
│  测试加速     → swc-node (Jest 配合)                    │
│  生产运行     → tsc 编译 + node 运行 (最稳)             │
│  全新项目     → Bun (一体化体验)                        │
│  类型安全     → CI 中运行 tsc --noEmit (必须！)         │
└─────────────────────────────────────────────────────────┘
```

> 💡 **核心建议**：
> **开发用 tsx，检查用 tsc，生产用 node + js**。
> 不要为了开发方便而牺牲生产环境的稳定性和类型安全性。

如有具体配置问题（如 ESM 配置、Jest 集成），欢迎继续提问！
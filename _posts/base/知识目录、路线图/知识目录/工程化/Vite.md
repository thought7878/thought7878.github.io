>总结 Vite 的全部的知识点，不要介绍，只要知识点
>Vite 知识点总结

## 1. 核心原理
- 原生 ES Modules (Native ESM)
- 按需编译 (On-demand Compilation)
- 开发环境无打包 (No Bundling in Dev)
- 生产环境 Rollup 打包
- 依赖预构建 (Pre-bundling)
- Esbuild 转译 (Go 语言编写)
- 热模块替换 (HMR)
- 冷启动速度 (Cold Start)

## 2. 命令行 CLI
- `vite` (启动开发服务器)
- `vite build` (生产构建)
- `vite preview` (预览构建结果)
- `vite optimize` (强制预构建依赖)
- `--host` (暴露主机)
- `--open` (自动打开浏览器)
- `--port` (指定端口)
- `--mode` (指定模式)
- `--config` (指定配置文件)
- `--debug` (调试模式)
- `--force` (强制忽略缓存)

## 3. 配置文件
- `vite.config.js` / `vite.config.ts`
- `defineConfig` 工具函数
- `loadEnv` 加载环境变量
- 配置项类型定义 (UserConfig)
- 条件配置 (command, mode)
- 配置文件热重载
- 多配置文件策略 (多环境)

## 4. 开发服务器 (Server)
- 端口配置 (port)
- 主机配置 (host: '0.0.0.0')
- 自动打开 (open)
- HTTPS 配置
- 代理配置 (proxy)
- 跨域配置 (cors)
- 热更新配置 (hmr)
- 文件监听 (watch)
- 中间件模式 (middlewareMode)
- 预热文件 (warmup)
- 文件系统限制 (fs.allow)
- 请求头配置 (headers)
- Strict Port (strictPort)
- Origin 配置

## 5. 生产构建 (Build)
- 输出目录 (outDir)
- 资源目录 (assetsDir)
- 静态资源基础路径 (base)
- 源码映射 (sourcemap)
- 清空目录 (emptyOutDir)
- 清单文件 (manifest)
- CSS 代码分割 (cssCodeSplit)
- 最小化压缩 (minify: esbuild/terser)
- 构建目标 (target)
- SSR 构建 (ssr: true)
- 报告压缩大小 (reportCompressedSize)
- Chunk 大小警告限制
- Rollup 配置透传 (rollupOptions)
- 手动 Chunk 分割 (manualChunks)
- 文件命名模板 (entryFileNames, chunkFileNames, assetFileNames)

## 6. 依赖预构建 (OptimizeDeps)
- 启用开关 (enabled)
- 包含依赖 (include)
- 排除依赖 (exclude)
- 强制预构建 (force)
- Esbuild 选项 (esbuildOptions)
- 缓存目录 (node_modules/.vite)
- 依赖扫描 (Scanner)
- CommonJS 互操作
- 深层依赖处理
- 持有延迟 (holdToCrawlDelay)

## 7. 样式处理 (CSS)
- 原生 CSS 支持
- CSS Modules
- 预处理器 (Sass, Less, Stylus)
- PostCSS 配置
- 全局样式注入
- CSS 代码分割
- CSS 源映射 (devSourcemap)
- 预处理器选项 (preprocessorOptions)
- 额外数据注入 (additionalData)
- Lightning CSS (实验性)
- 作用域行为 (scopeBehaviour)
- 类名生成策略 (generateScopedName)

## 8. 静态资源 (Assets)
- Public 目录 (静态拷贝)
- 资源导入 (import url)
- 资源内联限制 (assetsInlineLimit)
- 资源类型包含 (assetsInclude)
- 导入元数据 (import.meta.url)
- 新 URL 构造函数 (new URL(..., import.meta.url))
- 资源命名哈希
- WebAssembly 支持
- 视频/音频支持
- 字体文件支持

## 9. JS/TS/JSX
- TypeScript 支持 (仅转译，无类型检查)
- Esbuild 转译配置
- JSX 支持 (React, Vue, Preact)
- JSX 工厂函数配置
- JSX 导入源 (jsxImportSource)
- Decorators 支持
- 动态导入 (Dynamic Import)
- Import Meta 属性
- Top-level Await
- 自定义 Loader
- 支持文件扩展名 (extensions)

## 10. 插件系统 (Plugins)
- Vite 插件接口
- Rollup 插件兼容
- 插件钩子 (Hooks)
- 插件执行顺序 (enforce: pre/default/post)
- 虚拟模块 (Virtual Modules)
- 转换钩子 (transform)
- 解析钩子 (resolveId)
- 加载钩子 (load)
- 配置钩子 (config)
- 配置解析钩子 (configResolved)
- 构建开始/结束钩子
- 服务器钩子 (configureServer)
- 索引签名插件
- 插件上下文 (PluginContext)

## 11. 服务端渲染 (SSR)
- SSR 构建入口 (ssr.entry)
- 外部化依赖 (ssr.external)
- 非外部化依赖 (ssr.noExternal)
- SSR 目标 (node/browser)
- SSR 源映射
- SSR 加载模块 (ssrLoadModule)
- 预加载指令 (renderPreloadLinks)
- 模块 Runner (experimental.ssr)
- 去耦构建 (Decoupled Build)
- 实例化缓存

## 12. 库模式 (Library Mode)
- 入口文件 (lib.entry)
- 库名称 (lib.name)
- 文件名模板 (lib.fileName)
- 输出格式 (formats: es, umd, cjs, iife)
- 外部依赖 (external)
- 全局变量 (globals)
- 多入口构建
- CSS 内联选项

## 13. 环境变量 (Env)
- .env 文件加载
- 模式特定文件 (.env.development, .env.production)
- 环境变量前缀 (VITE_)
- 客户端暴露 (import.meta.env)
- 服务端暴露 (process.env)
- 环境变量类型定义 (env.d.ts)
- 定义插件 (DefinePlugin)
- 环境变量目录 (envDir)

## 14. 性能优化
- 依赖预构建加速
- 代码分割 (Code Splitting)
- Tree Shaking
- 懒加载 (Lazy Loading)
- 预加载 (Preload)
- 预获取 (Prefetch)
- 缓存策略 (HTTP Cache)
- 构建缓存 (FileSystem Cache)
- 并行构建 (Workers)
- 减少依赖体积
- 图片优化 (压缩/格式转换)
- 移除 Console/Debugger
- 按需加载组件

## 15. 生态集成
- Vue (官方支持)
- React (官方支持)
- Svelte (官方支持)
- Preact
- Solid
- Lit
- PWA (vite-plugin-pwa)
- 测试 (Vitest)
- E2E 测试 (Playwright, Cypress)
- 样式方案 (TailwindCSS, WindiCSS, UnoCSS)
- 文档 (VitePress)
- 移动端 (Taro, Uni-app)

## 16. 底层机制
- Connect 服务器
- Koa 中间件兼容
- Resolver 解析器
- Transformer 转换器
- Scanner 扫描器
- WebSocket 通信 (HMR)
- 模块图 (Module Graph)
- 缓存策略 (Dep Cache, Transform Cache)
- 错误覆盖层 (Error Overlay)
- 源映射链 (Source Map Chain)
- 模块 runner (Module Runner)

## 17. 版本特性 (Vite 2/3/4/5)
- Vite 2: 插件系统稳定，SSR 改进
- Vite 3: ESBuild 依赖预构建，CSS 性能提升
- Vite 4: 移除 CJS SSR 外部化默认，Esbuild 升级
- Vite 5: 移除 CJS 支持 (生产环境 ESM only)，Import Meta Glob 稳定，WebSocket Token
- Vite 6: (前瞻) 更严格的 ESM，性能进一步优化

## 18. 常见问题排查
- 跨域问题 (CORS/Proxy)
- 静态资源 404
- 环境变量不生效
- HMR 不生效
- 构建速度慢
- 依赖预构建失败
- CSS 模块冲突
- TypeScript 类型检查缺失
- 生产环境路径错误
- Node 版本兼容性
- 插件冲突
- 内存溢出 (OOM)
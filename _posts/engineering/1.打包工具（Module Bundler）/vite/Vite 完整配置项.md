>总结vite全部的配置，按功能分类整理，并附带一个包含完整配置项和注释的模板

>Vite 配置完全指南

Vite 的配置系统简洁而强大，基于 **Rollup** 构建，支持 **TypeScript** 原生配置。以下是按功能分类的完整配置总结。

---

## Vite 配置分类总览

| 分类 | 配置项 | 说明 | 环境 |
|------|--------|------|------|
| **基础配置** | `root`, `base`, `mode`, `publicDir` | 项目根目录、基础路径、模式等 | 通用 |
| **服务器配置** | `server` | 开发服务器端口、代理、HMR 等 | 开发 |
| **构建配置** | `build` | 输出目录、压缩、代码分割等 | 生产 |
| **依赖优化** | `optimizeDeps` | 预构建配置、包含/排除 | 开发 |
| **CSS 配置** | `css` | 预处理器、模块、PostCSS 等 | 通用 |
| **资源处理** | `assetsInclude`, `assetsInlineLimit` | 静态资源处理 | 通用 |
| **插件配置** | `plugins` | Vite/Rollup 插件 | 通用 |
| **预处理器** | `esbuild`, `json` | 转译和 JSON 处理 | 通用 |
| **SSR 配置** | `ssr` | 服务端渲染相关 | SSR |
| **其他配置** | `clearScreen`, `logLevel`, `envDir` | 日志、环境变量等 | 通用 |

---

## 完整配置模板 (vite.config.ts)

```typescript
import { defineConfig, loadEnv } from 'vite';
import path from 'path';
import vue from '@vitejs/plugin-vue';
import react from '@vitejs/plugin-react';
import { VitePWA } from 'vite-plugin-pwa';
import viteCompression from 'vite-plugin-compression';
import visualizer from 'rollup-plugin-visualizer';

// https://vitejs.dev/config/
export default defineConfig(({ command, mode }) => {
  // 加载环境变量 (根据 mode 加载 .env.[mode] 文件)
  const env = loadEnv(mode, process.cwd(), '');
  
  return {
    // ============================================
    // 1️⃣ 基础配置 (Base Configuration)
    // ============================================
    
    // 项目根目录 (默认：process.cwd())
    root: '.',
    
    // 基础公共路径 (默认：'/')
    // 部署到子目录时设置，如：'/my-app/'
    base: env.VITE_BASE_PATH || '/',
    
    // 公共目录 (默认：'public')
    // 该目录下的文件会被直接复制到输出目录
    publicDir: 'public',
    
    // 环境变量目录 (默认：'.')
    envDir: './env',
    
    // 环境变量前缀 (默认：'VITE_')
    envPrefix: 'VITE_',
    
    // 工作区模式 (默认：从命令行读取)
    mode: mode,
    
    // 是否清除控制台屏幕 (默认：true)
    clearScreen: true,
    
    // 日志级别：'silent' | 'error' | 'warn' | 'info'
    logLevel: 'info',
    
    // 自定义日志输出
    customLogger: undefined,
    
    // 是否启用详细日志 (默认：false)
    enforce: undefined,
    
    // ============================================
    // 2️⃣ 插件配置 (Plugins)
    // ============================================
    
    plugins: [
      // 框架插件 (根据项目选择)
      // vue(),
      // react(),
      
      // PWA 支持
      VitePWA({
        registerType: 'autoUpdate',
        workbox: {
          globPatterns: ['**/*.{js,css,html,ico,png,svg}']
        }
      }),
      
      // Gzip 压缩
      viteCompression({
        verbose: true,
        disable: false,
        threshold: 10240,
        algorithm: 'gzip',
        ext: '.gz'
      }),
      
      // 构建分析 (生产环境启用)
      ...(mode === 'production' ? [
        visualizer({
          open: true,
          gzipSize: true,
          brotliSize: true
        })
      ] : [])
    ],
    
    // ============================================
    // 3️⃣ 服务器配置 (Server - 开发环境)
    // ============================================
    
    server: {
      // 开发服务器端口 (默认：5173)
      port: 3000,
      
      // 是否自动打开浏览器 (默认：false)
      open: true,
      
      // 是否启用 HTTPS (默认：false)
      https: false,
      // https: {
      //   key: fs.readFileSync('cert.key'),
      //   cert: fs.readFileSync('cert.crt')
      // },
      
      // 主机地址 (默认：'localhost')
      // '0.0.0.0' 允许外部访问
      host: '0.0.0.0',
      
      // 严格端口 (端口被占用时退出，默认：false)
      strictPort: false,
      
      // 是否启用 HMR (默认：true)
      hmr: {
        protocol: 'ws',
        host: 'localhost',
        port: 5173,
        path: '/hmr',
        overlay: true // 显示错误遮罩层
      },
      
      // 代理配置 (解决跨域问题)
      proxy: {
        // 字符串简写
        '/api': 'http://localhost:8080',
        
        // 对象配置 (更灵活)
        '/api/v1': {
          target: 'http://localhost:8080',
          changeOrigin: true, // 修改 Host 头
          secure: false,      // 允许 HTTPS
          rewrite: (path) => path.replace(/^\/api\/v1/, '/v1'),
          // WebSocket 支持
          ws: true,
          // 配置代理超时
          timeout: 60000,
          // 错误处理
          onError: (err, req, res) => {
            console.error('Proxy error:', err);
          }
        },
        
        // 多个代理
        '/images': {
          target: 'http://cdn.example.com',
          changeOrigin: true
        }
      },
      
      // CORS 配置 (默认：允许所有)
      cors: true,
      // cors: {
      //   origin: 'http://example.com',
      //   methods: ['GET', 'POST'],
      //   allowedHeaders: ['Content-Type'],
      //   credentials: true
      // },
      
      // 请求头
      headers: {
        'X-Custom-Header': 'value'
      },
      
      // 预热文件 (提前转换，提升首屏速度)
      warmup: {
        clientFiles: [
          './src/main.ts',
          './src/components/*.vue'
        ],
        ssrFiles: []
      },
      
      // 文件监听配置
      watch: {
        // 忽略的文件
        ignored: ['**/node_modules/**', '**/.git/**'],
        // 是否使用轮询 (默认：false)
        usePolling: false,
        // 轮询间隔 (ms)
        interval: 100
      },
      
      // 中间件模式 (用于集成到其他服务器)
      middlewareMode: false,
      
      // 静态文件服务配置
      fs: {
        // 允许访问的目录
        allow: ['..'],
        // 拒绝访问的文件
        deny: ['.env', '.env.*', '*.local'],
        // 严格模式 (默认：true)
        strict: true
      }
    },
    
    // ============================================
    // 4️⃣ 构建配置 (Build - 生产环境)
    // ============================================
    
    build: {
      // 输出目录 (默认：'dist')
      outDir: 'dist',
      
      // 静态资源目录 (默认：'assets')
      assetsDir: 'assets',
      
      // 是否生成 source map (默认：false)
      // 'inline' | true | false | 'hidden'
      sourcemap: false,
      
      // 构建前是否清空输出目录 (默认：true)
      emptyOutDir: true,
      
      // 构建后是否写入 manifest 文件 (默认：false)
      manifest: false,
      // manifest: 'manifest.json',
      
      // 是否启用 CSS 代码分割 (默认：true)
      cssCodeSplit: true,
      
      // 是否启用 SSR 构建 (默认：false)
      ssr: false,
      
      // SSR 构建入口
      // ssrEntry: 'src/entry-server.ts',
      
      // 是否生成 SSR 外部化依赖列表 (默认：true)
      ssrEmitAssets: false,
      
      // 最小化压缩 (默认：'esbuild')
      // false | true | 'terser' | 'esbuild'
      minify: 'esbuild',
      
      // Terser 配置 (当 minify: 'terser' 时)
      terserOptions: {
        compress: {
          drop_console: true,    // 移除 console
          drop_debugger: true    // 移除 debugger
        },
        format: {
          comments: false        // 移除注释
        }
      },
      
      // 目标浏览器 (默认：'modules')
      target: 'modules',
      // target: ['chrome58', 'edge18', 'safari11'],
      
      // 代码分割策略
      rollupOptions: {
        // 输入入口
        input: {
          main: path.resolve(__dirname, 'index.html'),
          // 多页面应用
          // about: path.resolve(__dirname, 'about.html')
        },
        
        // 输出配置
        output: {
          // 文件命名模板
          entryFileNames: 'js/[name].[hash].js',
          chunkFileNames: 'js/[name].[hash].js',
          assetFileNames: '[ext]/[name].[hash].[ext]',
          
          // 手动分割代码块
          manualChunks: {
            // 第三方库单独打包
            vendor: ['vue', 'vue-router', 'pinia'],
            // 大型组件单独打包
            // charts: ['echarts']
          },
          
          // 内联动态导入 (默认：false)
          inlineDynamicImports: false,
          
          // 生成格式 (默认：'es')
          // 'es' | 'cjs' | 'iife' | 'umd' | 'amd'
          format: 'es',
          
          // 全局变量 (UMD/IIFE 模式)
          // globals: {
          //   vue: 'Vue'
          // },
          
          // 是否使用 ES Module
          esModule: true,
          
          // 是否严格模式
          strict: true,
          
          // 是否生成 interop 代码
          interop: 'auto'
        },
        
        // 外部依赖 (不打包)
        external: ['vue', 'vue-router'],
        
        // 插件配置
        plugins: [
          // 自定义 Rollup 插件
        ]
      },
      
      // 构建时是否报告 gzip 大小 (默认：true)
      reportCompressedSize: true,
      
      // 触发警告的 chunk 大小限制 (默认：500kb)
      chunkSizeWarningLimit: 500,
      
      // 构建时是否保留 __dirname 和 __filename (默认：false)
      commonjsOptions: {
        include: [/node_modules/],
        extensions: ['.js', '.cjs']
      }
    },
    
    // ============================================
    // 5️⃣ 依赖预构建配置 (OptimizeDeps)
    // ============================================
    
    optimizeDeps: {
      // 是否启用 (默认：true)
      enabled: true,
      
      // 强制预构建的依赖
      include: [
        'vue',
        'vue-router',
        'axios',
        'lodash-es'
      ],
      
      // 排除预构建的依赖
      exclude: [
        'vue-demi'
      ],
      
      // 预构建时的 esbuild 选项
      esbuildOptions: {
        // 解析器平台 (默认：'browser')
        platform: 'browser',
        
        // 目标浏览器
        target: 'es2020',
        
        // 定义全局常量
        define: {
          'process.env.NODE_ENV': '"development"'
        },
        
        // 自定义插件
        plugins: []
      },
      
      // 预构建缓存目录 (默认：'node_modules/.vite')
      // cacheDir: 'node_modules/.vite',
      
      // 是否强制预构建 (默认：false)
      force: false,
      
      // 预构建时的持有时间 (ms)
      holdToCrawlDelay: 300
    },
    
    // ============================================
    // 6️⃣ CSS 配置
    // ============================================
    
    css: {
      // CSS 预处理器配置
      preprocessorOptions: {
        scss: {
          // 全局 SCSS 变量
          additionalData: `
            @import "@/styles/variables.scss";
            @import "@/styles/mixins.scss";
          `,
          // SCSS 选项
          javascriptEnabled: true,
          // 源映射
          sourceMap: true,
          // 静默模式
          quietDeps: true,
          // 包含路径
          includePaths: [path.resolve(__dirname, 'src/styles')]
        },
        less: {
          additionalData: `@import "@/styles/variables.less";`,
          javascriptEnabled: true,
          modifyVars: {
            'primary-color': '#1890ff'
          }
        },
        stylus: {
          additionalData: `@import '@/styles/variables.styl'`,
          define: {
            $primaryColor: '#1890ff'
          }
        }
      },
      
      // CSS 模块配置
      modules: {
        // 类名生成策略
        // 'hash' | 'scoped' | function
        generateScopedName: '[name]__[local]___[hash:base64:5]',
        // generateScopedName: (name, filename, css) => {
        //   return `custom-${name}-${local}`;
        // },
        
        // 局部作用域类名前缀
        scopeBehaviour: 'local',
        
        // 全局类名模式
        globalModulePaths: [/global\.module\.css$/],
        
        // 导出模式
        exportGlobals: false,
        
        // 是否启用 namedExports
        namedExports: false,
        
        // 本地类名约定
        localsConvention: 'camelCaseOnly',
        // 'camelCase' | 'camelCaseOnly' | 'dashes' | 'dashesOnly'
        
        // 是否生成源映射
        sourceMap: false,
        
        // 自动启用 CSS Modules 的文件
        auto: true
      },
      
      // PostCSS 配置
      postcss: {
        // 插件配置
        plugins: [
          // 自动添加浏览器前缀
          require('autoprefixer')({
            overrideBrowserslist: ['> 1%', 'last 2 versions']
          }),
          // CSS 压缩
          require('cssnano')({
            preset: 'default'
          }),
          // 其他插件
          // require('postcss-import'),
          // require('postcss-nesting')
        ],
        
        // PostCSS 配置文件路径
        // config: 'postcss.config.js'
      },
      
      // 是否启用 CSS 源映射 (默认：false)
      devSourcemap: false,
      
      // 是否内联 CSS (默认：false)
      // true | false | 'inline'
      // inline: false
    },
    
    // ============================================
    // 7️⃣ 资源处理配置 (Assets)
    // ============================================
    
    // 静态资源处理
    assetsInclude: [
      '**/*.md',
      '**/*.webm'
    ],
    
    // 资源内联限制 (字节，默认：4096)
    // 小于此值的资源会转为 Base64
    assetsInlineLimit: 4096,
    
    // 资源文件命名
    // 在 build.rollupOptions.output.assetFileNames 中配置
    
    // ============================================
    // 8️⃣ Esbuild 配置 (TypeScript/JSX 转译)
    // ============================================
    
    esbuild: {
      // 目标浏览器 (默认：'es2020')
      target: 'es2020',
      
      // 是否启用源映射 (默认：true)
      sourcemap: true,
      
      // 是否启用 JSX (默认：自动检测)
      jsx: 'automatic',
      // 'transform' | 'preserve' | 'automatic'
      
      // JSX 工厂函数
      jsxFactory: 'React.createElement',
      jsxFragment: 'React.Fragment',
      
      // JSX 导入源 (React 17+)
      jsxImportSource: 'react',
      
      // 定义全局常量
      define: {
        __DEV__: 'true',
        __VERSION__: '"1.0.0"'
      },
      
      // 是否保留函数名 (调试用)
      keepNames: false,
      
      // 是否启用树摇
      treeShaking: true,
      
      // 是否启用最小化
      minify: false,
      
      // 是否启用法律注释
      legalComments: 'none',
      // 'none' | 'inline' | 'eof' | 'linked' | 'external'
      
      // 自定义 loader
      loader: {
        '.js': 'jsx'
      },
      
      // 排除的文件
      exclude: [/node_modules/, /\.test\./],
      
      // 支持的文件扩展名
      supported: {
        'import-meta': true
      }
    },
    
    // ============================================
    // 9️⃣ JSON 配置
    // ============================================
    
    json: {
      // 是否启用命名导出 (默认：true)
      namedExports: true,
      
      // 是否压缩 JSON (默认：false)
      stringify: false,
      
      // 最大 JSON 文件大小 (字节)
      // 超过此值会警告
      maxSize: 10000
    },
    
    // ============================================
    // 🔟 SSR 配置 (服务端渲染)
    // ============================================
    
    ssr: {
      // SSR 构建入口
      // entry: 'src/entry-server.ts',
      
      // 外部化依赖 (不转换，直接使用 Node.js 版本)
      external: ['vue', 'vue-router'],
      
      // 不外部化的依赖 (强制转换)
      noExternal: ['@my-company/component-lib'],
      
      // 是否启用 SSR 源映射 (默认：false)
      sourcemap: false,
      
      // SSR 目标 (默认：'node')
      target: 'node',
      
      // 是否启用 SSR 优化 (默认：true)
      optimizeDeps: {
        enabled: true
      },
      
      // SSR 构建时的 resolve 配置
      resolve: {
        // 条件导出
        conditions: ['node']
      }
    },
    
    // ============================================
    // 1️⃣1️⃣ 解析配置 (Resolve)
    // ============================================
    
    resolve: {
      // 路径别名
      alias: {
        '@': path.resolve(__dirname, 'src'),
        '@components': path.resolve(__dirname, 'src/components'),
        '@utils': path.resolve(__dirname, 'src/utils'),
        '@assets': path.resolve(__dirname, 'src/assets'),
        '@styles': path.resolve(__dirname, 'src/styles')
      },
      
      // 文件扩展名解析 (默认：['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json'])
      extensions: ['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.vue'],
      
      // 是否解析浏览器字段 (默认：true)
      browserField: true,
      
      // 条件导出 (默认：['browser', 'module', 'import', 'require'])
      conditions: ['browser', 'module', 'import'],
      
      // 是否保留查询参数 (默认：false)
      preserveSymlinks: false,
      
      // 自定义解析函数
      // customResolver: (source, importer, options) => {}
      
      // 是否启用 dedupe (去重)
      dedupe: ['vue', 'react']
    },
    
    // ============================================
    // 1️⃣2️⃣ 实验性功能 (Experimental)
    // ============================================
    
    experimental: {
      // 是否启用 import 元数据 (默认：false)
      importGlobRestoreExtension: false,
      
      // 是否启用 hmr 部分接受 (默认：false)
      hmrPartialAccept: false,
      
      // 是否跳过 SSR 转换 (默认：false)
      skipSsrTransform: false
    },
    
    // ============================================
    // 1️⃣3️ 预览服务器配置 (Preview)
    // ============================================
    
    preview: {
      // 预览服务器端口 (默认：4173)
      port: 4173,
      
      // 是否自动打开浏览器
      open: true,
      
      // 主机地址
      host: '0.0.0.0',
      
      // 是否启用 HTTPS
      https: false,
      
      // 代理配置 (同 server.proxy)
      proxy: {},
      
      // CORS 配置
      cors: true,
      
      // 请求头
      headers: {}
    },
    
    // ============================================
    // 1️⃣4️⃣ 工作器配置 (Worker)
    // ============================================
    
    worker: {
      // 工作器格式 (默认：'es')
      format: 'es',
      
      // 工作器插件
      plugins: [],
      
      // 是否启用预构建
      rollupOptions: {}
    },
    
    // ============================================
    // 1️⃣5️⃣ 其他配置
    // ============================================
    
    // 是否启用 Vue 特定配置 (使用 @vitejs/plugin-vue 时)
    // vue: {},
    
    // 是否启用 React 特定配置 (使用 @vitejs/plugin-react 时)
    // react: {},
    
    // 是否启用 Legacy 插件 (@vitejs/plugin-legacy)
    // legacy: {},
    
    // 是否启用 PWA 配置
    // pwa: {}
  };
});
```

---

## 常用配置速查表

### 开发环境常用配置
```typescript
// vite.config.ts
export default defineConfig({
  server: {
    port: 3000,
    open: true,
    proxy: {
      '/api': 'http://localhost:8080'
    }
  },
  optimizeDeps: {
    include: ['vue', 'axios']
  }
});
```

### 生产环境常用配置
```typescript
// vite.config.ts
export default defineConfig({
  build: {
    outDir: 'dist',
    minify: 'esbuild',
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['vue', 'vue-router']
        }
      }
    }
  }
});
```

### 多页面应用配置
```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      input: {
        main: path.resolve(__dirname, 'index.html'),
        about: path.resolve(__dirname, 'about.html'),
        contact: path.resolve(__dirname, 'contact.html')
      }
    }
  }
});
```

### 库模式配置
```typescript
// vite.config.ts
export default defineConfig({
  build: {
    lib: {
      entry: path.resolve(__dirname, 'src/index.ts'),
      name: 'MyLib',
      fileName: (format) => `my-lib.${format}.js`
    },
    rollupOptions: {
      external: ['vue'],
      output: {
        globals: {
          vue: 'Vue'
        }
      }
    }
  }
});
```

---

## 注意事项与最佳实践

### 1. 环境变量使用
```typescript
// .env.development
VITE_API_URL=http://dev-api.example.com

// .env.production
VITE_API_URL=https://api.example.com

// vite.config.ts 中使用
const env = loadEnv(mode, process.cwd(), '');
console.log(env.VITE_API_URL);

// 源代码中使用
console.log(import.meta.env.VITE_API_URL);
```

### 2. 路径别名最佳实践
```typescript
// vite.config.ts
resolve: {
  alias: {
    '@': path.resolve(__dirname, 'src'),
    '@components': path.resolve(__dirname, 'src/components')
  }
}

// tsconfig.json (IDE 支持)
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@components/*": ["src/components/*"]
    }
  }
}
```

### 3. 性能优化建议
```typescript
// 1. 代码分割
build: {
  rollupOptions: {
    output: {
      manualChunks: {
        vendor: ['vue', 'vue-router', 'pinia'],
        echarts: ['echarts']
      }
    }
  }
}

// 2. 依赖预构建
optimizeDeps: {
  include: ['vue', 'axios', 'lodash-es']
}

// 3. 资源压缩
plugins: [
  viteCompression({ algorithm: 'brotliCompress' })
]
```

### 4. 常见问题解决

| 问题 | 解决方案 |
|------|----------|
| 跨域问题 | 配置 `server.proxy` |
| 静态资源 404 | 检查 `publicDir` 或 `assetsDir` |
| 构建速度慢 | 启用 `optimizeDeps`，排除大依赖 |
| CSS 不生效 | 检查 `css.modules` 配置 |
| 环境变量不生效 | 确保前缀是 `VITE_` |

---

## 总结

```
┌─────────────────────────────────────────────────────────┐
│  📌 Vite 配置核心要点                                    │
├─────────────────────────────────────────────────────────┤
│  🔹 基础配置：root, base, mode, publicDir               │
│  🔹 开发配置：server (端口/代理/HMR)                     │
│  🔹 构建配置：build (输出/压缩/分割)                     │
│  🔹 依赖优化：optimizeDeps (预构建)                      │
│  🔹 样式配置：css (预处理器/模块/PostCSS)                │
│  🔹 资源处理：assetsInclude, assetsInlineLimit          │
│  🔹 插件系统：plugins (Vite/Rollup 插件)                 │
│  🔹 路径解析：resolve.alias (路径别名)                   │
├─────────────────────────────────────────────────────────┤
│  💡 最佳实践：按需配置，不要过度优化                     │
│  🚀 趋势：Vite 5+ 性能更优，配置更简洁                   │
└─────────────────────────────────────────────────────────┘
```

> 💡 **核心建议**：
> 1. **开发环境**：关注 `server` 配置，提升开发体验
> 2. **生产环境**：关注 `build` 配置，优化输出质量
> 3. **依赖管理**：合理使用 `optimizeDeps` 加速启动
> 4. **代码组织**：使用 `resolve.alias` 简化导入路径



MSW 已成为 2024-2026 年前端工程化中**API Mock 的事实标准**。它抛弃了传统的“重写全局 API”或“硬编码静态文件”思路，转而利用浏览器/Node.js 原生网络拦截能力，在**不修改业务代码**的前提下提供高保真、可测试、环境隔离的 Mock 服务。

以下从架构原理、工程配置、高级用法到最佳实践进行系统拆解。

---
## 一、核心理念与架构设计

### 1. 设计哲学
- **零侵入**：业务代码中的 `fetch` / `axios` / `graphql-request` 无需任何改动。
- **网络层拦截**：不修改 JS 运行时 API，而是在请求真正离开浏览器/Node 进程前“劫持”网络流。
- **环境统一**：同一套 `handlers` 可同时运行于浏览器（开发调试）与 Node 环境（单元测试/SSR）。
- **契约驱动**：天然契合 OpenAPI/Swagger，可与类型系统深度绑定。

### 2. 双环境拦截架构
| 环境       | 底层机制                          | 运行位置         | 特点                                                                 |
|------------|-----------------------------------|------------------|----------------------------------------------------------------------|
| **浏览器** | `Service Worker` (SW)             | 独立浏览器线程   | 拦截 `fetch` & `XHR`；支持跨域、缓存策略、离线调试；首次注册后持久化 |
| **Node**   | `@mswjs/interceptors` (原生 Hook) | Node.js 进程     | 代理 `http`/`https`/`undici` 模块；用于 Vitest/Jest、SSR、BFF 预渲染 |

> 💡 **关键区别**：传统 Mock 库（如 Mock.js）重写 `window.fetch` 原型，易引发兼容性问题；MSW 利用浏览器/Node 原生扩展点，稳定性与安全性更高。

---
## 二、核心 API 与工作原理

### 1. 路由定义 (`handlers`)
```ts
// src/mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  // REST 拦截
  http.get('/api/users/:id', ({ params, request }) => {
    const { id } = params;
    const headers = Object.fromEntries(request.headers);
    return HttpResponse.json({ id, name: `User-${id}`, role: 'admin' }, { status: 200 });
  }),

  // 动态响应 & 延迟模拟
  http.post('/api/login', async ({ request }) => {
    const body = await request.json();
    if (body.username !== 'admin') {
      return new HttpResponse('Unauthorized', { status: 401, delay: 800 });
    }
    return HttpResponse.json({ token: 'mock-jwt-xxx' });
  }),

  // GraphQL 拦截（自动解析查询字段）
  graphql.query('GetUser', () => {
    return HttpResponse.json({
      data: { user: { id: '1', name: 'MSW User' } }
    });
  })
];
```

### 2. 环境初始化
```ts
// src/mocks/browser.ts (开发环境)
import { setupWorker } from 'msw/browser';
import { handlers } from './handlers';

export const worker = setupWorker(...handlers);

// src/mocks/server.ts (测试/Node 环境)
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);
```

### 3. 请求生命周期
```
应用发起请求 → 浏览器 SW / Node 拦截器捕获 → 匹配 handlers → 
  ├─ 匹配成功 → 执行 handler 逻辑 → 返回 HttpResponse
  └─ 匹配失败 → 触发 onUnhandledRequest (warn/bypass/error) → 透传至真实网络
```

---
## 三、完整工程化配置指南（Vite + React/Vue）

### 1. 安装依赖
```bash
npm i -D msw
# 可选：配合 OpenAPI 自动生成类型
npm i -D openapi-typescript-codegen zod
```

### 2. 目录结构
```
src/
├── mocks/
│   ├── handlers.ts      # 路由规则
│   ├── browser.ts       # 浏览器端入口
│   └── server.ts        # Node/测试端入口
├── main.tsx             # 应用入口
└── vite.config.ts       # 构建配置
```

### 3. 主入口集成（按需启用）
```ts
// src/main.tsx
import { createRoot } from 'react-dom/client';
import App from './App';

const enableMock = import.meta.env.VITE_ENABLE_MOCK === 'true';

async function bootstrap() {
  if (enableMock) {
    const { worker } = await import('./mocks/browser');
    // 静默未匹配请求，避免开发环境频繁报警
    await worker.start({ onUnhandledRequest: 'bypass' });
  }

  createRoot(document.getElementById('root')!).render(<App />);
}

bootstrap();
```

### 4. 环境变量控制
```env
# .env.development
VITE_ENABLE_MOCK=true
VITE_API_BASE_URL=/api # 相对路径，由 MSW 拦截

# .env.production
VITE_ENABLE_MOCK=false
VITE_API_BASE_URL=https://api.yourdomain.com
```

---
## 四、高级场景与最佳实践

### 1. 测试环境无缝集成（Vitest）
```ts
// vitest.config.ts
export default defineConfig({
  test: {
    setupFiles: ['./test/setup-mock.ts'],
    environment: 'jsdom',
  }
});

// test/setup-mock.ts
import { server } from '../src/mocks/server';

beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));
afterEach(() => server.resetHandlers()); // 清除用例间状态污染
afterAll(() => server.close());
```

### 2. 动态覆盖规则（测试边界条件）
```ts
// user.test.ts
import { http, HttpResponse } from 'msw';
import { server } from '../src/mocks/server';

test('handles server 500 error', async () => {
  server.use(
    http.get('/api/users', () => HttpResponse.json({ msg: 'Internal Error' }, { status: 500 }))
  );

  const res = await fetch('/api/users');
  expect(res.status).toBe(500);
});
```

### 3. 混合代理（Mock + 真实后端降级）
```ts
// handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/mock/*', () => HttpResponse.json({ source: 'msw' })),
  // 其余请求透传至真实后端（需配合 vite proxy 或 nginx）
];
```

### 4. 类型安全契约（2026 推荐实践）
```ts
// 使用 zod 校验 + OpenAPI 生成类型
import { z } from 'zod';
import { HttpResponse, http } from 'msw';
import type { User } from './api-types'; // 由 openapi-typescript 生成

const UserSchema = z.object({ id: z.number(), name: z.string() });

http.get('/api/users', () => {
  const mockUser: User = UserSchema.parse({ id: 1, name: 'Alice' });
  return HttpResponse.json([mockUser]);
});
```

---
## 五、局限性与排错指南

| 问题现象                  | 根因分析                          | 解决方案                                  |
|---------------------------|-----------------------------------|-------------------------------------------|
| SW 注册失败 / 拦截不生效  | 非 `localhost` 或 HTTPS 环境      | 开发环境使用 `localhost` 或配置可信证书   |
| Network 面板显示 `(sw)`   | 请求被 SW 拦截，非真实网络请求    | 属正常现象；MSW DevTools 可查看详细信息   |
| 跨域请求未拦截            | SW 作用域未覆盖或跨域策略限制     | 配置 `worker.start({ serviceWorker: { url: '/mock-sw.js' } })` 或调整 scope |
| 测试中 Cookie 未传递      | `http` 拦截器未配置凭证           | 使用 `http.get(url, { credentials: 'include' }, handler)` |
| 生产环境意外启用          | 环境变量未严格隔离                | 编译时剔除：`if (import.meta.env.PROD) throw new Error('Mock enabled in prod')` |

> 🔍 **调试技巧**：
> - 浏览器打开 `chrome://serviceworker-internals/` 查看 SW 状态
> - 安装 [MSW DevTools](https://github.com/mswjs/msw-devtools) 浏览器插件，实时查看拦截日志与响应结构
> - 使用 `console.info('MSW: Intercepted', request.url)` 在 handler 中打点

---
## 六、在 2026 前端工程中的定位

| 维度           | MSW 的表现                                                                 |
|----------------|----------------------------------------------------------------------------|
| **开发体验**   | 热更新友好；Network 面板透明化；支持 AI 辅助生成 Handlers                  |
| **测试集成**   | Vitest/Jest/Playwright/Cypress 官方推荐；支持 E2E 与组件级测试网络隔离      |
| **工程规范**   | 与 OpenAPI 生成器、CI/CD 流水线深度绑定；支持契约测试（Pact）              |
| **演进趋势**   | v2 已稳定；v3 规划强化 AI 驱动 Mock、边缘计算适配、WebContainer 原生支持    |
| **替代方案**   | 已全面取代 Mock.js；与 Dev Server Mock 形成互补（开发快 vs 测试真）        |

---
## 七、总结：何时选择 MSW？

✅ **强烈推荐场景**：
- 需要高保真网络模拟（延迟、超时、状态码、跨域）
- 前后端并行开发 + 自动化测试（单元/集成/E2E）
- 技术栈为现代框架（React/Vue/Next/Nuxt）+ Vite
- 团队追求类型安全与契约驱动开发

❌ **不建议场景**：
- 纯静态页面 / 无网络请求的组件库展示
- 老旧构建工具（Webpack 3 / 无 SW 支持环境）
- 企业内网严格禁止 Service Worker 注册（需改用 Dev Server Mock 或独立平台）

🔧 **2026 最佳实践组合**：
```
API 契约平台 (Apifox/YApi) 
   ↓ (导出 OpenAPI → 自动生成 TS 类型 + MSW Handlers)
MSW (测试环境 / 高保真联调)
   ↓ (按需降级)
Vite Plugin Mock (本地开发快速迭代)
   ↓ (环境变量切换)
真实后端 (预发/生产)
```

MSW 不是“更快的 Mock 工具”，而是**研发流的基础设施**。掌握其网络拦截原理与测试集成模式，可显著降低联调成本、提升代码可测性与交付质量。
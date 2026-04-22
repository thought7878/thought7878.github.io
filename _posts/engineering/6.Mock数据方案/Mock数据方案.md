>在前端开发过程中，需要系统梳理常用的Mock数据方案及其各自的优缺点。请详细列举至少5种主流的Mock数据实现方式，针对每种方案，需分析其适用场景、实现复杂度、灵活性、与开发环境的集成程度、性能表现及局限性。同时，需提供各方案的典型实现示例和配置方法，并从开发效率、维护成本和团队协作角度进行综合对比，最终给出不同项目规模和技术栈下的方案选择建议。

# 前端Mock数据方案全面解析

## 一、主流Mock数据方案概览

前端开发中常用的Mock数据方案至少有8种，我将从多个维度进行系统梳理和对比分析。

---

## 二、各方案详细分析

### 方案1：硬编码Mock数据（Inline Mock）

#### 适用场景
- 项目初期快速原型开发
- 组件开发和单元测试
- 数据量小、接口简单的场景
- 当前项目采用的方式

#### 典型实现

```typescript
// 直接在组件内定义Mock数据
const mockOrders = [
  {
    id: '1',
    orderNo: 'ORD202401010001',
    customer: '张三',
    amount: 12999,
    status: 'completed',
  },
];

// 或在单独的文件中定义
// mock/data.ts
export const mockDashboardData = {
  gmv: 1234567,
  orders: 8923,
  visitors: 45678,
  conversionRate: 12.5,
};

// 使用方式
const Dashboard = () => {
  const [data, setData] = useState(mockDashboardData);
  // ...
};
```

#### 优缺点分析

| 维度 | 评价 |
|------|------|
| **实现复杂度** | ⭐ 极低，直接定义即可 |
| **灵活性** | ⭐⭐ 低，数据静态，难以模拟动态场景 |
| **开发效率** | ⭐⭐⭐⭐⭐ 极高，零配置 |
| **维护成本** | ⭐⭐ 高，数据分散在各组件中 |
| **团队协作** | ⭐⭐ 差，前后端无法共用 |
| **性能表现** | ⭐⭐⭐⭐⭐ 最优，无网络开销 |

#### 局限性
- 数据与业务代码耦合，难以维护
- 无法模拟真实API调用（延迟、错误等）
- 不支持动态参数和条件响应
- 难以进行接口联调切换

---

### 方案2：Mock.js

#### 适用场景
- 传统jQuery/Vue项目
- 需要拦截Ajax请求的场景
- 快速生成随机数据
- 中小型项目

#### 典型实现

```bash
# 安装
npm install mockjs
```

```typescript
// mock/index.ts
import Mock from 'mockjs';

// 拦截请求并返回Mock数据
Mock.mock('/api/dashboard', 'get', {
  code: 200,
  message: 'success',
  data: {
    gmv: Mock.Random.float(100000, 999999, 2),
    orders: Mock.Random.integer(1000, 9999),
    visitors: Mock.Random.integer(10000, 99999),
    'products|10': [
      {
        id: '@id',
        name: '@ctitle(5, 10)',
        price: '@float(99, 9999, 2)',
        sales: '@integer(100, 9999)',
      },
    ],
  },
});

// 支持动态参数
Mock.mock(/\/api\/product\/\d+/, 'get', (options) => {
  const id = options.url.match(/\/api\/product\/(\d+)/)[1];
  return {
    code: 200,
    data: {
      id,
      name: Mock.Random.ctitle(),
      price: Mock.Random.float(99, 9999, 2),
    },
  };
});

// 在入口文件引入
// main.ts
import './mock';
```

#### 配置方法

```typescript
// mock/config.ts
import Mock from 'mockjs';

// 设置全局延迟
Mock.setup({
  timeout: '200-600',  // 随机延迟200-600ms
});

// 条件启用（仅开发环境）
if (import.meta.env.DEV) {
  import('./mock');
}
```

#### 优缺点分析

| 维度 | 评价 |
|------|------|
| **实现复杂度** | ⭐⭐ 低，API简单易用 |
| **灵活性** | ⭐⭐⭐ 中等，支持模板语法和函数 |
| **开发效率** | ⭐⭐⭐⭐ 高，快速生成随机数据 |
| **维护成本** | ⭐⭐⭐ 中等，集中管理Mock数据 |
| **团队协作** | ⭐⭐⭐ 中等，可导出接口文档 |
| **性能表现** | ⭐⭐⭐⭐ 较好，拦截XMLHttpRequest |

#### 局限性
- 仅支持浏览器环境，不支持Node.js/SSR
- 拦截的是XMLHttpRequest，对Fetch API支持不佳
- 无法模拟真实的网络层行为
- 项目已停止维护，存在兼容性风险
- 与TypeScript集成不够友好

---

### 方案3：MSW (Mock Service Worker)

#### 适用场景
- **现代前端项目（强烈推荐）**
- 需要真实网络层模拟的场景
- 测试环境Mock
- 支持Service Worker的浏览器环境

#### 典型实现

```bash
# 安装
npm install msw --save-dev
```

```typescript
// mock/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  // GET请求
  http.get('/api/dashboard', () => {
    return HttpResponse.json({
      code: 200,
      data: {
        gmv: 1234567.89,
        orders: 8923,
        visitors: 45678,
      },
    });
  }),

  // 带参数的请求
  http.get('/api/product/:id', ({ params }) => {
    const { id } = params;
    return HttpResponse.json({
      code: 200,
      data: {
        id,
        name: `商品${id}`,
        price: 9999,
      },
    });
  }),

  // POST请求
  http.post('/api/order', async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({
      code: 200,
      data: {
        orderId: Date.now(),
        ...body,
      },
    });
  }),

  // 模拟错误
  http.get('/api/error', () => {
    return HttpResponse.json(
      { code: 500, message: '服务器错误' },
      { status: 500 }
    );
  }),

  // 模拟网络延迟
  http.get('/api/slow', async () => {
    await new Promise(resolve => setTimeout(resolve, 2000));
    return HttpResponse.json({ code: 200, data: '慢响应' });
  }),
];
```

```typescript
// mock/browser.ts - 浏览器环境
import { setupWorker } from 'msw/browser';
import { handlers } from './handlers';

export const worker = setupWorker(...handlers);
```

```typescript
// mock/node.ts - Node.js环境（测试用）
import { setupServer } from 'msw/node';
import { handlers } from './handlers';

export const server = setupServer(...handlers);
```

```typescript
// main.ts - 启动Mock
async function bootstrap() {
  if (import.meta.env.DEV) {
    const { worker } = await import('./mock/browser');
    await worker.start({
      onUnhandledRequest: 'bypass',  // 未Mock的请求直接放行
    });
  }
  
  // 启动应用
  ReactDOM.createRoot(document.getElementById('root')!).render(<App />);
}

bootstrap();
```

#### 动态Mock实现

```typescript
// mock/handlers.ts - 高级用法
import { http, HttpResponse, delay } from 'msw';

// 使用数据库模拟状态
const db = {
  products: [
    { id: '1', name: 'iPhone 15', price: 9999 },
    { id: '2', name: 'MacBook Pro', price: 14999 },
  ],
};

export const handlers = [
  // 列表查询（支持分页）
  http.get('/api/products', ({ request }) => {
    const url = new URL(request.url);
    const page = parseInt(url.searchParams.get('page') || '1');
    const pageSize = parseInt(url.searchParams.get('pageSize') || '10');
    
    const start = (page - 1) * pageSize;
    const end = start + pageSize;
    
    return HttpResponse.json({
      code: 200,
      data: {
        list: db.products.slice(start, end),
        total: db.products.length,
        page,
        pageSize,
      },
    });
  }),

  // 创建资源
  http.post('/api/products', async ({ request }) => {
    const body = await request.json();
    const newProduct = {
      id: Date.now().toString(),
      ...body,
    };
    db.products.push(newProduct);
    return HttpResponse.json({ code: 200, data: newProduct });
  }),

  // 更新资源
  http.put('/api/products/:id', async ({ params, request }) => {
    const { id } = params;
    const body = await request.json();
    const index = db.products.findIndex(p => p.id === id);
    
    if (index === -1) {
      return HttpResponse.json(
        { code: 404, message: '商品不存在' },
        { status: 404 }
      );
    }
    
    db.products[index] = { ...db.products[index], ...body };
    return HttpResponse.json({ code: 200, data: db.products[index] });
  }),

  // 删除资源
  http.delete('/api/products/:id', ({ params }) => {
    const { id } = params;
    db.products = db.products.filter(p => p.id !== id);
    return HttpResponse.json({ code: 200, message: '删除成功' });
  }),
];
```

#### 优缺点分析

| 维度        | 评价                        |
| --------- | ------------------------- |
| **实现复杂度** | ⭐⭐⭐ 中等，需要理解Service Worker |
| **灵活性**   | ⭐⭐⭐⭐⭐ 极高，完全控制请求响应         |
| **开发效率**  | ⭐⭐⭐⭐ 高，一次编写多处使用           |
| **维护成本**  | ⭐⭐⭐⭐ 低，集中管理，类型友好          |
| **团队协作**  | ⭐⭐⭐⭐⭐ 优秀，前后端测试共用          |
| **性能表现**  | ⭐⭐⭐⭐ 好，真实网络层拦截            |

#### 局限性
- 需要浏览器支持Service Worker（IE不支持）
- 首次启动需要注册Service Worker
- 学习成本相对较高
- 对WebSocket支持有限

---

### 方案4：JSON Server

#### 适用场景
- 需要完整REST API模拟
- 原型演示和POC项目
- 需要CRUD操作的场景
- 前后端分离开发

#### 典型实现

```bash
# 安装
npm install json-server --save-dev
```

```json
// mock/db.json
{
  "products": [
    {
      "id": 1,
      "name": "iPhone 15 Pro",
      "category": "手机",
      "price": 9999,
      "sales": 1234
    },
    {
      "id": 2,
      "name": "MacBook Pro 14",
      "category": "电脑",
      "price": 14999,
      "sales": 567
    }
  ],
  "orders": [
    {
      "id": 1,
      "orderNo": "ORD202401010001",
      "customerId": 1,
      "amount": 12999,
      "status": "completed"
    }
  ],
  "users": [
    {
      "id": 1,
      "username": "admin",
      "role": "admin"
    }
  ]
}
```

```typescript
// mock/server.ts - 自定义路由和中间件
import jsonServer from 'json-server';

const server = jsonServer.create();
const router = jsonServer.router('mock/db.json');
const middlewares = jsonServer.defaults();

// 自定义中间件
server.use(middlewares);

// 自定义路由
server.get('/api/dashboard', (req, res) => {
  res.jsonp({
    gmv: 1234567,
    orders: 8923,
    visitors: 45678,
  });
});

// 延迟响应
server.use((req, res, next) => {
  setTimeout(next, 500);
});

// 认证中间件
server.use('/api/*', (req, res, next) => {
  const token = req.headers.authorization;
  if (!token) {
    return res.status(401).jsonp({ message: '未授权' });
  }
  next();
});

server.use(router);

server.listen(3001, () => {
  console.log('JSON Server is running on http://localhost:3001');
});
```

```json
// package.json - 启动脚本
{
  "scripts": {
    "dev": "vite",
    "mock": "json-server --watch mock/db.json --port 3001",
    "dev:mock": "concurrently \"npm run mock\" \"npm run dev\""
  }
}
```

```typescript
// vite.config.ts - 代理配置
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3001',
        changeOrigin: true,
      },
    },
  },
});
```

#### 优缺点分析

| 维度 | 评价 |
|------|------|
| **实现复杂度** | ⭐⭐ 低，JSON配置即可 |
| **灵活性** | ⭐⭐⭐ 中等，支持基本CRUD |
| **开发效率** | ⭐⭐⭐⭐ 高，快速搭建Mock服务器 |
| **维护成本** | ⭐⭐⭐ 中等，需维护JSON文件 |
| **团队协作** | ⭐⭐⭐⭐ 好，独立服务可共享 |
| **性能表现** | ⭐⭐⭐ 中等，独立进程运行 |

#### 局限性
- 需要额外运行一个服务进程
- 复杂业务逻辑实现困难
- 数据持久化能力弱
- 不适合大型复杂项目

---

### 方案5：vite-plugin-mock

#### 适用场景
- **Vite项目（强烈推荐）**
- 需要与构建工具深度集成
- 本地开发环境Mock
- 当前项目最适合的方案

#### 典型实现

```bash
# 安装
npm install vite-plugin-mock mockjs -D
```

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { viteMockServe } from 'vite-plugin-mock';

export default defineConfig({
  plugins: [
    react(),
    viteMockServe({
      mockPath: 'mock',           // Mock文件目录
      localEnabled: true,         // 开发环境启用
      prodEnabled: false,         // 生产环境禁用
      injectCode: `
        import { setupProdMockServer } from './mockProdServer';
        setupProdMockServer();
      `,
      logger: true,               // 显示日志
    }),
  ],
});
```

```typescript
// mock/dashboard.ts
import { MockMethod } from 'vite-plugin-mock';

export default [
  {
    url: '/api/dashboard',
    method: 'get',
    response: () => {
      return {
        code: 200,
        message: 'success',
        data: {
          gmv: 1234567.89,
          orders: 8923,
          visitors: 45678,
          conversionRate: 12.5,
        },
      };
    },
  },
  {
    url: '/api/products',
    method: 'get',
    response: ({ query }) => {
      const { page = 1, pageSize = 10 } = query;
      return {
        code: 200,
        data: {
          list: mockProducts.slice((page - 1) * pageSize, page * pageSize),
          total: mockProducts.length,
        },
      };
    },
  },
  {
    url: '/api/login',
    method: 'post',
    response: ({ body }) => {
      const { username, password } = body;
      if (username === 'admin' && password === 'admin123') {
        return {
          code: 200,
          data: {
            token: 'mock-token-' + Date.now(),
            user: {
              id: 1,
              username: 'admin',
              role: 'admin',
            },
          },
        };
      }
      return {
        code: 401,
        message: '用户名或密码错误',
      };
    },
  },
] as MockMethod[];
```

```typescript
// mock/products.ts
import { MockMethod } from 'vite-plugin-mock';

const products = [
  { id: 1, name: 'iPhone 15 Pro', price: 9999, sales: 1234 },
  { id: 2, name: 'MacBook Pro 14', price: 14999, sales: 567 },
  { id: 3, name: 'AirPods Pro 2', price: 1899, sales: 2345 },
];

export default [
  {
    url: '/api/products',
    method: 'get',
    response: () => ({
      code: 200,
      data: products,
    }),
  },
  {
    url: '/api/products/:id',
    method: 'get',
    response: ({ query }) => {
      const product = products.find(p => p.id === parseInt(query.id));
      return product
        ? { code: 200, data: product }
        : { code: 404, message: '商品不存在' };
    },
  },
] as MockMethod[];
```

#### 优缺点分析

| 维度 | 评价 |
|------|------|
| **实现复杂度** | ⭐⭐ 低，Vite插件配置简单 |
| **灵活性** | ⭐⭐⭐⭐ 高，支持完整Node.js能力 |
| **开发效率** | ⭐⭐⭐⭐⭐ 极高，零额外配置 |
| **维护成本** | ⭐⭐⭐⭐ 低，文件化管理 |
| **团队协作** | ⭐⭐⭐⭐ 好，与项目代码共存 |
| **性能表现** | ⭐⭐⭐⭐⭐ 最优，构建时集成 |

#### 局限性
- 仅支持Vite项目
- 生产环境Mock需要额外配置
- 依赖vite-plugin-mock的维护

---

### 方案6：Mirage JS

#### 适用场景
- Ember/React/Vue项目
- 需要ORM能力
- 复杂数据关系模拟
- 测试环境

#### 典型实现

```bash
# 安装
npm install miragejs
```

```typescript
// mock/server.ts
import { createServer, Model, Factory, belongsTo, hasMany } from 'miragejs';
import faker from 'faker';

export function makeServer({ environment = 'development' } = {}) {
  const server = createServer({
    environment,

    models: {
      product: Model,
      order: Model,
      user: Model,
    },

    factories: {
      product: Factory.extend({
        name: () => faker.commerce.productName(),
        price: () => faker.commerce.price(),
        sales: () => faker.random.number({ min: 100, max: 9999 }),
      }),
    },

    seeds(server) {
      server.createList('product', 20);
    },

    routes() {
      this.namespace = 'api';

      this.get('/products', (schema, request) => {
        return schema.products.all();
      });

      this.get('/products/:id', (schema, request) => {
        const id = request.params.id;
        return schema.products.find(id);
      });

      this.post('/products', (schema, request) => {
        const attrs = JSON.parse(request.requestBody);
        return schema.products.create(attrs);
      });

      this.put('/products/:id', (schema, request) => {
        const attrs = JSON.parse(request.requestBody);
        const product = schema.products.find(request.params.id);
        return product.update(attrs);
      });

      this.del('/products/:id', (schema, request) => {
        const product = schema.products.find(request.params.id);
        product.destroy();
        return {};
      });
    },
  });

  return server;
}
```

```typescript
// main.ts
import { makeServer } from './mock/server';

if (import.meta.env.DEV) {
  makeServer();
}
```

#### 优缺点分析

| 维度 | 评价 |
|------|------|
| **实现复杂度** | ⭐⭐⭐⭐ 较高，需学习ORM概念 |
| **灵活性** | ⭐⭐⭐⭐⭐ 极高，完整ORM支持 |
| **开发效率** | ⭐⭐⭐ 中等，学习成本高 |
| **维护成本** | ⭐⭐⭐ 中等，模型定义复杂 |
| **团队协作** | ⭐⭐⭐ 中等，概念较重 |
| **性能表现** | ⭐⭐⭐⭐ 好，内存数据库 |

#### 局限性
- 学习曲线陡峭
- 项目活跃度下降
- 过度设计风险
- 不适合简单场景

---

### 方案7：在线Mock平台（Apifox/Postman/YApi）

#### 适用场景
- 团队协作项目
- 需要接口文档管理
- 前后端并行开发
- 企业级项目

#### 典型实现（以Apifox为例）

```typescript
// 1. 在Apifox平台定义接口
// 2. 获取Mock URL
// 3. 配置环境变量

// .env.development
VITE_API_BASE_URL=https://mock.apifox.cn/m1/123456-0-default

// .env.production
VITE_API_BASE_URL=https://api.production.com

// axios配置
import axios from 'axios';

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
});

// 开发环境自动使用Mock
// 生产环境切换真实API
```

#### 优缺点分析

| 维度 | 评价 |
|------|------|
| **实现复杂度** | ⭐⭐ 低，可视化配置 |
| **灵活性** | ⭐⭐⭐⭐ 高，支持脚本和规则 |
| **开发效率** | ⭐⭐⭐⭐ 高，接口文档一体化 |
| **维护成本** | ⭐⭐⭐⭐ 低，云端管理 |
| **团队协作** | ⭐⭐⭐⭐⭐ 优秀，多人协作 |
| **性能表现** | ⭐⭐⭐ 中等，依赖网络 |

#### 局限性
- 依赖第三方服务稳定性
- 免费版有功能限制
- 数据隐私风险
- 需要网络连接

---

### 方案8：Nginx Mock

#### 适用场景
- 生产环境降级方案
- 后端服务不可用时
- 静态数据Mock
- 运维层面控制

#### 典型实现

```nginx
# nginx.conf
server {
    listen 80;
    server_name localhost;

    # Mock接口
    location /api/dashboard {
        default_type application/json;
        return 200 '{
            "code": 200,
            "data": {
                "gmv": 1234567,
                "orders": 8923
            }
        }';
    }

    # 代理真实API
    location /api/ {
        proxy_pass http://backend:3000;
    }
}
```

#### 优缺点分析

| 维度 | 评价 |
|------|------|
| **实现复杂度** | ⭐⭐⭐ 中等，需Nginx知识 |
| **灵活性** | ⭐⭐ 低，静态配置 |
| **开发效率** | ⭐⭐ 低，配置繁琐 |
| **维护成本** | ⭐⭐ 高，运维介入 |
| **团队协作** | ⭐⭐ 差，门槛高 |
| **性能表现** | ⭐⭐⭐⭐⭐ 最优，服务器级别 |

#### 局限性
- 配置复杂，不适合前端开发者
- 动态数据支持差
- 调试困难
- 仅适合特定场景

---

## 三、综合对比分析

### 3.1 多维度对比表

| 方案 | 复杂度 | 灵活性 | 开发效率 | 维护成本 | 团队协作 | 性能 | TypeScript | 测试支持 | 推荐指数 |
|------|--------|--------|----------|----------|----------|------|------------|----------|----------|
| 硬编码 | ⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ |
| Mock.js | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| **MSW** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| JSON Server | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| **vite-plugin** | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Mirage JS | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| 在线平台 | ⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Nginx | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐ | ⭐ | ⭐⭐ |

### 3.2 开发效率对比

```
快速原型开发：
硬编码 > vite-plugin > Mock.js > JSON Server > 在线平台 > MSW > Mirage JS > Nginx

长期维护项目：
MSW > vite-plugin > 在线平台 > JSON Server > Mirage JS > Mock.js > 硬编码 > Nginx

团队协作项目：
在线平台 > MSW > vite-plugin > JSON Server > Mirage JS > Mock.js > 硬编码 > Nginx
```

### 3.3 维护成本对比

```
低维护成本（推荐）：
MSW ≈ vite-plugin > 在线平台 > JSON Server > Mirage JS > Mock.js > 硬编码 > Nginx
```

---

## 四、不同项目规模方案选择建议

### 4.1 小型项目/个人项目

**特征**：1-2人开发，周期短（<1个月），接口简单（<20个）

**推荐方案**：硬编码 或 vite-plugin-mock

```typescript
// 选择依据
- 开发速度优先
- 不需要复杂的Mock逻辑
- 快速验证想法
```

### 4.2 中型项目/团队项目

**特征**：3-5人开发，周期中等（1-3个月），接口较多（20-100个）

**推荐方案**：vite-plugin-mock 或 MSW

```typescript
// 当前项目（Vite + React）推荐配置
// vite.config.ts
import { viteMockServe } from 'vite-plugin-mock';

export default defineConfig({
  plugins: [
    viteMockServe({
      mockPath: 'mock',
      localEnabled: true,
      logger: true,
    }),
  ],
});

// 目录结构
mock/
├── dashboard.ts      # 仪表盘相关Mock
├── product.ts        # 商品相关Mock
├── order.ts          # 订单相关Mock
├── auth.ts           # 认证相关Mock
└── index.ts          # 统一导出
```

### 4.3 大型项目/企业项目

**特征**：5+人开发，周期长（>3个月），接口复杂（100+个）

**推荐方案**：MSW + 在线Mock平台

```typescript
// 开发环境：MSW
// 测试环境：MSW
// 联调阶段：在线Mock平台
// 生产环境：真实API

// 优势
- 前后端共用Mock定义
- 接口文档自动生成
- 测试用例可复用
- 支持CI/CD集成
```

### 4.4 技术栈匹配建议

| 技术栈 | 推荐方案 | 原因 |
|--------|----------|------|
| **Vite + React/Vue** | vite-plugin-mock | 原生集成，零配置 |
| **Webpack + React** | MSW | 构建工具无关 |
| **Next.js/Nuxt** | MSW | 支持SSR |
| **Vue2 + jQuery** | Mock.js | 兼容性好 |
| **Ember** | Mirage JS | 框架内置支持 |
| **企业级项目** | Apifox + MSW | 团队协作优先 |

---

## 五、当前项目实践建议

基于当前项目的技术栈（Vite + React + TypeScript + Ant Design），我推荐以下方案：

### 5.1 推荐方案：vite-plugin-mock

**理由**：
1. 与Vite原生集成，性能最优
2. 配置简单，开箱即用
3. 支持TypeScript
4. 文件化管理，易于维护
5. 支持动态逻辑和参数处理

### 5.2 实施步骤

```bash
# 1. 安装依赖
npm install vite-plugin-mock mockjs -D

# 2. 创建Mock目录
mkdir -p mock

# 3. 配置vite.config.ts
# 4. 编写Mock数据文件
# 5. 在组件中使用axios调用（无需修改）
```

### 5.3 渐进式迁移策略

```
阶段1：保留现有硬编码数据（快速验证）
阶段2：将Mock数据迁移到mock/目录
阶段3：组件改用axios调用API
阶段4：完善Mock逻辑（分页、筛选等）
阶段5：后端就绪后切换真实API
```

---

## 六、总结

Mock数据方案的选择应该基于以下原则：

1. **项目规模**：小项目求快，大项目求稳
2. **技术栈**：选择与构建工具匹配的方案
3. **团队规模**：多人协作优先考虑在线平台
4. **维护周期**：长期项目选择可维护性高的方案
5. **测试需求**：需要测试支持选择MSW

**最终推荐**：
- **当前项目**：vite-plugin-mock（最匹配技术栈）
- **通用项目**：MSW（最灵活、最现代）
- **企业项目**：Apifox + MSW（最佳协作）



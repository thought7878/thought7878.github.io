>京东商智类企业级数据分析与商业智能前端项目规划方案

## 一、需求分析

### 1.1 用户角色与权限体系

#### 1.1.1 用户角色定义

| 角色 | 权限等级 | 核心职责 | 功能访问范围 |
|------|----------|----------|--------------|
| 超级管理员 | L1 | 系统全局管理、权限分配、系统配置 | 全部功能，含系统设置、审计日志 |
| 数据管理员 | L2 | 数据质量管理、指标配置、数据源管理 | 数据分析、报表管理、指标配置 |
| 运营总监 | L3 | 业务数据查看、策略制定、团队管理 | 全业务模块数据、团队数据概况 |
| 运营专员 | L4 | 日常数据监控、业务数据维护 | 本职相关数据模块、基础分析功能 |
| 数据分析师 | L5 | 数据深度分析、定制报表、洞察发现 | 高级分析功能、自定义报表、可视化工具 |
| 访客 | L6 | 基础数据查看 | 仅限公开dashboard查看 |

#### 1.1.2 权限体系设计

```
权限模型：RBAC（Role-Based Access Control）+ ABAC（Attribute-Based Access Control）

├── 菜单权限（可见性控制）
├── 操作权限（按钮级控制）
├── 数据权限（行级/列级数据隔离）
└── API权限（接口访问控制）
```

### 1.2 核心业务场景与用户故事

#### 场景一：运营数据监控
```
用户故事：作为运营专员，我需要在每天早上快速查看昨日核心业务指标，
包括GMV、订单量、客单价、转化率等，以便快速了解业务状态并开始日常工作。

验收标准：
- 首页仪表盘首屏加载时间 < 2秒
- 核心指标卡片展示完整，支持点击钻取
- 支持自定义指标组合和时间范围
```

#### 场景二：商品数据分析
```
用户故事：作为运营总监，我需要分析商品的销售趋势、库存周转和竞品对比，
以便制定商品运营策略和促销计划。

验收标准：
- 支持至少100万商品数据的流畅展示
- 图表支持导出和分享
- 支持自定义时间维度和对比维度
```

#### 场景三：用户行为分析
```
用户故事：作为数据分析师，我需要分析用户在平台的浏览、点击、购买等行为路径，
识别用户流失节点和转化机会，为产品优化提供数据支撑。

验收标准：
- 支持用户分群和标签管理
- 支持漏斗分析和路径分析
- 支持自定义事件追踪
```

#### 场景四：报表定制与分享
```
用户故事：作为运营经理，我需要根据业务需求定制个性化报表，
并定期自动推送给相关团队成员。

验收标准：
- 报表编辑器支持拖拽布局
- 支持定时任务和邮件推送
- 支持报表版本管理和历史记录
```

### 1.3 功能需求清单（含优先级）

#### P0 - 核心功能（必须实现）

| 功能模块 | 功能点               | 优先级 | 工作量(人天) |
| ---- | ----------------- | --- | ------- |
| 登录认证 | 多因素认证、SSO集成、JWT刷新 | P0  | 8       |
| 仪表盘  | 多图表仪表盘、拖拽布局、数据刷新  | P0  | 15      |
| 数据概览 | 核心KPI卡片、趋势图、实时数据  | P0  | 10      |
| 商品分析 | 商品列表、搜索筛选、销售排行    | P0  | 12      |
| 订单分析 | 订单列表、状态流转、退款管理    | P0  | 10      |
| 权限管理 | 角色配置、用户管理、权限分配    | P0  | 8       |

#### P1 - 重要功能（计划实现）

| 功能模块 | 功能点 | 优先级 | 工作量(人天) |
|----------|--------|--------|--------------|
| 流量分析 | 来源分析、页面分析、事件追踪 | P1 | 12 |
| 用户分析 | 用户分群、留存分析、LTV计算 | P1 | 15 |
| 报表中心 | 报表创建、编辑、导出、分享 | P1 | 12 |
| 数据可视化 | 图表配置、交互设计、自定义图表 | P1 | 10 |
| 预警中心 | 阈值预警、智能告警、通知管理 | P1 | 8 |

#### P2 - 增强功能（优化实现）

| 功能模块 | 功能点 | 优先级 | 工作量(人天) |
|----------|--------|--------|--------------|
| 对比分析 | 同比环比、自定义对比维度 | P2 | 8 |
| 预测分析 | 销售预测、趋势预测 | P2 | 15 |
| 自定义配置 | 主题定制、Dashboard模板 | P2 | 6 |
| 移动端 | 响应式适配、APP化体验 | P2 | 20 |

### 1.4 非功能需求

#### 1.4.1 性能指标

| 指标项 | 目标值 | 说明 |
|--------|--------|------|
| 首屏加载时间 | ≤2s | Lighthouse评分 ≥90 |
| 交互响应时间 | ≤200ms | 用户操作的反馈时间 |
| 图表渲染时间 | ≤500ms | 10万数据点以内 |
| 大数据量加载 | ≤3s | 100万数据分页加载 |
| 内存占用 | ≤500MB | 正常使用峰值 |
| 包体积 | ≤500KB | 首屏加载资源 |
| Web Vitals - LCP | ≤2.5s | Largest Contentful Paint |
| Web Vitals - FID | ≤100ms | First Input Delay |
| Web Vitals - CLS | ≤0.1 | Cumulative Layout Shift |

#### 1.4.2 安全性要求

- 传输安全：全站HTTPS，HSTS头配置
- 认证授权：JWT双令牌、Refresh Token轮换、登录异常检测
- XSS防护：输入校验、输出编码、Content-Security-Policy
- CSRF防护：Token验证、双重提交Cookie
- 数据安全：敏感数据加密、脱敏展示、操作日志审计
- 依赖安全：定期漏洞扫描、依赖升级策略

#### 1.4.3 可扩展性要求

- 插件化架构支持第三方组件集成
- 动态路由支持菜单和页面配置化
- 国际化完整支持（中文、英文）
- 主题定制支持（深色模式、主题色切换）
- 模块独立部署支持（微前端）

### 1.5 用户界面与交互体验要求

#### 1.5.1 设计规范

```
设计系统：自建Design System（基于Ant Design定制）

├── 色彩系统
│   ├── 主色：#1677FF（品牌蓝）
│   ├── 成功：#52C41A
│   ├── 警告：#FAAD14
│   ├── 错误：#FF4D4F
│   └── 中性色板：Gray 50-900
├── 字体系统
│   ├── 主字体：PingFang SC / -apple-system / Microsoft YaHei
│   ├── 数字字体：DIN Pro / Roboto Mono
│   └── 字重：300/400/500/600/700
├── 间距系统
│   ├── 基础单位：4px
│   ├── 间距梯度：4/8/12/16/20/24/32/40/48/64
│   └── 页面边距：24px（桌面）/ 16px（移动）
├── 圆角系统
│   ├── 小：4px（标签、徽章）
│   ├── 中：8px（卡片、按钮）
│   └── 大：12px（模态框、容器）
└── 阴影系统
    ├── 层级1：0 1px 2px rgba(0,0,0,0.03)
    ├── 层级2：0 4px 8px rgba(0,0,0,0.06)
    └── 层级3：0 8px 24px rgba(0,0,0,0.12)
```

#### 1.5.2 交互规范

- 加载状态：骨架屏优先，避免白屏
- 空状态：提供插画和引导操作
- 错误状态：友好提示+重试操作
- 反馈机制：操作结果Toast提示
- 键盘支持：主要操作支持键盘快捷键
- 辅助功能：ARIA标签、屏幕阅读器支持

---

## 二、项目架构设计

### 2.1 整体架构图

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              客户端层 (Client Layer)                         │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   PC Web    │  │   Mobile    │  │   Pad Web   │  │  Electron   │        │
│  │  (主应用)    │  │  (响应式)    │  │  (响应式)    │  │  (桌面端)    │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            应用框架层 (Application Layer)                     │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                        React 18 + TypeScript                        │    │
│  ├──────────────┬──────────────┬──────────────┬──────────────┬────────┤    │
│  │   状态管理    │    路由管理    │   权限控制    │   国际化      │  主题   │    │
│  │ Redux Toolkit│ React Router │   CASL.js    │   i18next    │ Context│    │
│  └──────────────┴──────────────┴──────────────┴──────────────┴────────┘    │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                      组件库 (Business Components)                    │    │
│  │  ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐            │    │
│  │  │ 通用组件 ││ 业务组件 │ │ 图表组件 │ │表单组件 │ │表格组件 │            │    │
│  │  └────────┘ └────────┘ └────────┘ └────────┘ └────────┘            │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            网络层 (Network Layer)                             │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │   Axios     │  │ React Query │  │   Mock.js   │  │  WebSocket  │        │
│  │ (HTTP Client)│ │(Server State)│  │ (数据模拟)  │  │ (实时推送)  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                            后端服务层 (Backend Services)                      │
├─────────────────────────────────────────────────────────────────────────────┤
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐          │
│  │ 用户服务  │ │ 报表服务  │  │ 分析服务  │ │ 预警服务  │ │ 文件服务  │          │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘          │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 前端工程目录结构

```
bi-dashboard/
├── .vscode/                     # VSCode配置
│   ├── extensions.json
│   └── settings.json
├── public/                      # 静态资源
│   ├── favicon.ico
│   ├── manifest.json
│   └── robots.txt
├── src/
│   ├── assets/                  # 静态资源
│   │   ├── images/
│   │   ├── icons/
│   │   ├── fonts/
│   │   └── styles/
│   │       ├── global.less
│   │       ├── variables.less
│   │       └── mixins.less
│   ├── components/             # 组件目录
│   │   ├── common/              # 通用组件
│   │   │   ├── Button/
│   │   │   ├── Input/
│   │   │   ├── Select/
│   │   │   ├── Table/
│   │   │   ├── Modal/
│   │   │   ├── Drawer/
│   │   │   └── ... (按需扩展)
│   │   ├── business/            # 业务组件
│   │   │   ├── DataCard/        # 数据卡片组件
│   │   │   ├── ChartContainer/  # 图表容器
│   │   │   ├── FilterBar/       # 筛选栏
│   │   │   ├── ExportButton/    # 导出按钮
│   │   │   └── DateRangePicker/ # 日期范围选择
│   │   ├── layout/              # 布局组件
│   │   │   ├── AppLayout/
│   │   │   ├── Header/
│   │   │   ├── Sider/
│   │   │   └── Content/
│   │   └── charts/             # 图表组件
│   │       ├── LineChart/
│   │       ├── BarChart/
│   │       ├── PieChart/
│   │       ├── ScatterPlot/
│   │       ├── FunnelChart/
│   │       └── MapChart/
│   ├── pages/                 # 页面目录
│   │   ├── Login/
│   │   ├── Dashboard/
│   │   │   ├── Overview/        # 数据概览
│   │   │   └── Monitor/         # 实时监控
│   │   ├── Product/            # 商品分析
│   │   │   ├── List/
│   │   │   ├── Detail/
│   │   │   └── Ranking/
│   │   ├── Order/              # 订单分析
│   │   │   ├── List/
│   │   │   ├── Detail/
│   │   │   └── Statistics/
│   │   ├── Traffic/            # 流量分析
│   │   ├── User/               # 用户分析
│   │   ├── Report/             # 报表中心
│   │   │   ├── List/
│   │   │   ├── Create/
│   │   │   └── Preview/
│   │   ├── Alert/              # 预警中心
│   │   └── System/             # 系统设置
│   │       ├── User/
│   │       ├── Role/
│   │       └── Config/
│   ├── hooks/                  # 自定义Hooks
│   │   ├── useAuth.ts
│   │   ├── usePermission.ts
│   │   ├── useLoading.ts
│   │   ├── usePagination.ts
│   │   ├── useVirtualList.ts
│   │   ├── useCharts.ts
│   │   └── useWebSocket.ts
│   ├── store/                  # 状态管理
│   │   ├── index.ts
│   │   ├── slices/
│   │   │   ├── userSlice.ts
│   │   │   ├── permissionSlice.ts
│   │   │   ├── uiSlice.ts
│   │   │   └── notificationSlice.ts
│   │   └── middleware/
│   │       ├── loggerMiddleware.ts
│   │       └── authMiddleware.ts
│   ├── services/              # API服务层
│   │   ├── api/
│   │   │   ├── axios.ts        # Axios实例配置
│   │   │   ├── request.ts      # 请求封装
│   │   │   ├── response.ts     # 响应处理
│   │   │   └── errorHandler.ts # 错误处理
│   │   ├── modules/
│   │   │   ├── auth.ts
│   │   │   ├── user.ts
│   │   │   ├── product.ts
│   │   │   ├── order.ts
│   │   │   ├── traffic.ts
│   │   │   ├── report.ts
│   │   │   └── alert.ts
│   │   └── types/              # API类型定义
│   │       └── api.d.ts
│   ├── routes/                 # 路由配置
│   │   ├── index.ts
│   │   ├── routes.ts
│   │   ├── guards/
│   │   │   ├── AuthGuard.tsx
│   │   │   ├── PermissionGuard.tsx
│   │   │   └── LoadingGuard.tsx
│   │   └── modules/
│   │       ├── dashboard.ts
│   │       ├── product.ts
│   │       ├── order.ts
│   │       └── system.ts
│   ├── utils/                 # 工具函数
│   │   ├── common/
│   │   │   ├── format.ts       # 格式化工具
│   │   │   ├── validate.ts     # 校验工具
│   │   │   ├── cookie.ts       # Cookie操作
│   │   │   └── storage.ts      # 存储操作
│   │   ├── date/
│   │   │   ├── dateUtils.ts
│   │   │   └── rangeUtils.ts
│   │   ├── number/
│   │   │   ├── formatNumber.ts
│   │   │   └── calcUtils.ts
│   │   └── permission/
│   │       └── permissionUtils.ts
│   ├── types/                  # TypeScript类型定义
│   │   ├── global.d.ts
│   │   ├── components.d.ts
│   │   └── stores.d.ts
│   ├── constants/              # 常量定义
│   │   ├── api.ts
│   │   ├── config.ts
│   │   ├── storage.ts
│   │   └── regExp.ts
│   ├── locales/               # 国际化资源
│   │   ├── zh-CN/
│   │   └── en-US/
│   ├── styles/                # 全局样式
│   │   ├── index.less
│   │   ├── global.less
│   │   └── variables.less
│   ├── App.tsx
│   ├── main.tsx
│   └── vite-env.d.ts
├── tests/                     # 测试目录
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── mock/                       # Mock数据
│   ├── server.ts
│   └── routes/
├── scripts/                    # 脚本目录
│   ├── build.ts
│   └── analyze.ts
├── config/                     # 配置文件
│   ├── vite.config.ts
│   ├── tsconfig.json
│   ├── eslint.config.js
│   └── prettier.config.js
├── .env                        # 环境变量
├── .env.development
├── .env.production
├── .gitignore
├── package.json
├── pnpm-lock.yaml
├── README.md
└── CHANGELOG.md
```

### 2.3 模块化设计方案

#### 2.3.1 特性模块划分

```
特性模块 (Feature Modules)

├── features/
│   ├── auth/                  # 认证模块
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── store/
│   │   ├── types/
│   │   └── index.ts
│   │
│   ├── dashboard/             # 仪表盘模块
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── store/
│   │   ├── types/
│   │   └── index.ts
│   │
│   ├── product/               # 商品分析模块
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── pages/
│   │   ├── services/
│   │   ├── store/
│   │   ├── types/
│   │   └── index.ts
│   │
│   └── ... (其他模块)
```

#### 2.3.2 微前端架构（可选扩展）

```
主应用 (Container)
├── 壳工程：提供统一的登录、导航、权限控制
├── 基础服务：公共组件、公共Hooks、公共工具
└── 模块加载：按需加载子应用

子应用 (Micro Frontends)
├── product-app/      # 商品分析子应用
├── order-app/        # 订单分析子应用
├── traffic-app/      # 流量分析子应用
└── report-app/       # 报表中心子应用

技术选型：qiankun / single-spa
```

### 2.4 跨端适配策略

#### 2.4.1 响应式设计断点

```css
/* 响应式断点 */
$breakpoint-sm: 576px;   /* 手机竖屏 */
$breakpoint-md: 768px;   /* 平板竖屏 */
$breakpoint-lg: 992px;   /* 平板横屏 / 小桌面 */
$breakpoint-xl: 1200px;  /* 标准桌面 */
$breakpoint-xxl: 1600px; /* 大屏显示器 */
```

#### 2.4.2 适配策略

```
┌──────────────────┬──────────────────┬──────────────────┐
│     Mobile       │      Tablet      │      Desktop     │
│    (< 768px)     │   (768-992px)    │    (> 992px)     │
├──────────────────┼──────────────────┼──────────────────┤
│ 单列布局         │ 双列/三列布局     │ 多列布局         │
│ 底部导航         │ 侧边导航(收起)   │ 侧边导航(展开)   │
│ 卡片堆叠         │ 卡片混合         │ 图表并排         │
│ 简化图表         │ 中等复杂度图表   │ 完整图表+表格    │
│ 触摸交互         │ 触摸+点击        │ 点击+悬停        │
└──────────────────┴──────────────────┴──────────────────┘
```

### 2.5 与后端API交互架构

```
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│   Frontend  │ ──── │   Gateway   │ ──── │   Backend   │
│   (React)   │      │  (Nginx)    │      │   Services  │
└─────────────┘      └─────────────┘      └─────────────┘
      │                    │                    │
      │  HTTPS/WSS         │  HTTP              │ gRPC
      │                    │                    │
      ▼                    ▼                    ▼
┌─────────────────────────────────────────────────────────┐
│                      API Layer                          │
├──────────┬──────────┬──────────┬──────────┬────────────┤
│  Auth    │  Data    │  Report  │  Alert   │   File     │
│ /api/auth│ /api/v1  │ /api/v1  │ /api/v1  │  /api/v1   │
└──────────┴──────────┴──────────┴──────────┴────────────┘
```

---

## 三、技术选型明细

### 3.1 核心框架版本

| 技术         | 版本     | 说明         |
| ---------- | ------ | ---------- |
| React      | 18.2+  | 核心框架，最新稳定版 |
| React DOM  | 18.2+  | DOM渲染      |
| TypeScript | 5.3+   | 类型系统       |
| Node.js    | 20 LTS | 运行环境       |

### 3.2 状态管理方案

#### 选型对比

| 方案                | 适用场景      | 优点                     | 缺点     |
| ----------------- | --------- | ---------------------- | ------ |
| Redux Toolkit     | 大型复杂应用    | 功能完善、性能好、社区成熟          | 配置较复杂  |
| Zustand           | 中小型应用     | 轻量、API简洁               | 功能相对简单 |
| Recoil            | React原声集成 | 与React深度集成             | 社区较小   |
| Jotai             | 原子化状态     | 高度灵活、组合方便              | 学习曲线   |
| **Redux Toolkit** | **企业级应用** | **推荐：功能完善、性能优秀、可维护性强** | -      |

#### 选型理由

```
选择 Redux Toolkit 的核心理由：

1. 功能完整性
   - 内置Immer支持，简化不可变更新
   - 内置Redux Thunk，简化异步逻辑
   - RTK Query，数据获取和缓存一体化

2. 性能优化
   - 自动创建基于差异的selector
   - 最小化重渲染
   - 内置reselect支持

3. 开发体验
   - Redux DevTools集成
   - TypeScript原生支持
   - 标准化最佳实践

4. 生态系统
   - 庞大的社区和插件生态
   - 丰富的中间件
   - 成熟的文档和教程
```

### 3.3 路由管理

| 技术 | 版本 | 说明 |
|------|------|------|
| React Router | 6.20+ | 核心路由库 |
| React Router DOM | 6.20+ | DOM绑定 |

#### 路由设计策略

```typescript
// 路由设计原则
// 1. 嵌套路由：使用 React Router 的嵌套路由功能
// 2. 动态路由：使用路由参数处理动态内容
// 3. 懒加载路由：使用 React.lazy + Suspense 实现按需加载
// 4. 权限路由：结合高阶组件实现路由守卫

// 示例：路由配置结构
interface RouteConfig {
  path: string;
  component: React.ComponentType;
  guards?: GuardType[];
  children?: RouteConfig[];
  meta?: RouteMeta;
}
```

### 3.4 UI组件库选型

#### 选型对比

| 组件库            | 社区        | 定制性   | 学习成本  | 适用场景              |
| -------------- | --------- | ----- | ----- | ----------------- |
| Ant Design     | ★★★★★     | 中     | 中     | 企业级后台产品           |
| Element Plus   | ★★★★☆     | 中     | 低     | Vue项目迁移           |
| Material UI    | ★★★★☆     | 高     | 中     | 国际化项目             |
| **Ant Design** | **★★★★★** | **中** | **中** | **推荐：最适合国内企业级产品** |

#### 定制化方案

```typescript
// 1. 主题定制
// 使用 Ant Design ConfigProvider 进行主题配置
const theme = {
  token: {
    colorPrimary: '#1677FF',
    borderRadius: 8,
    // ... 其他token
  },
  components: {
    Button: {
      borderRadius: 8,
    },
    Table: {
      borderColor: '#f0f0f0',
    },
  },
};

// 2. 组件样式覆盖
// 使用 CSS Modules 或 Less 变量覆盖
// 路径：@ant-design/cssinjs

// 3. 业务组件封装
// 基于 Ant Design 原子组件封装业务组件
```

### 3.5 图表库选型

| 图表库                    | 图表类型    | 性能    | 学习成本  | 推荐场景             |
| ---------------------- | ------- | ----- | ----- | ---------------- |
| ECharts                | 丰富      | 中     | 中     | **推荐：复杂图表、大数据量** |
| Recharts               | 基础      | 好     | 低     | 简单图表、React集成     |
| D3.js                  | 高度自定义   | 低     | 高     | 自定义可视化           |
| AntV、Ant Design Charts | 封装良好    | 好     | 中     | Ant Design项目集成   |
| **ECharts**            | **50+** | **好** | **中** | **推荐：企业级数据可视化**  |

#### 选型理由

```
ECharts 核心优势：

1. 图表类型丰富
   - 基础图表：折线图、柱状图、饼图、散点图
   - 高级图表：漏斗图、雷达图、桑基图、地图
   - 3D图表：3D散点、3D柱状图、3D曲面

2. 大数据支持
   - 数据采样(Data Sampling)
   - 大数据量渲染优化
   - 支持Canvas和SVG渲染

3. 交互能力强
   - 缩放、拖拽
   - 数据区域缩放
   - 动态数据切换

4. 主题定制
   - 内置主题
   - 在线主题编辑器
   - 支持自定义主题
```

### 3.6 数据请求库

| 技术 | 版本 | 说明 |
|------|------|------|
| Axios | 1.6+ | HTTP客户端 |
| React Query | 3.39+ | 服务端状态管理 |
| SWR | 2.0+ | 数据获取策略 |

#### 封装策略

```typescript
// 请求封装层次
// 1. Axios 实例配置层
// 2. 请求拦截层（认证、loading、日志）
// 3. 响应处理层（错误处理、格式化）
// 4. API 模块封装层
// 5. React Query 数据层（缓存、分页）

// 推荐：Axios + React Query 组合
// - Axios 处理底层请求和拦截
// - React Query 处理缓存、轮询、分页
```

### 3.7 构建工具

| 技术 | 版本 | 说明 |
|------|------|------|
| Vite | 5.0+ | 构建工具 |
| @vitejs/plugin-react | 4.2+ | React插件 |
| vite-plugin-compression | 0.12+ | 压缩插件 |
| vite-plugin-visualizer | 5.0+ | 打包分析 |

#### Vite配置方案

```typescript
// vite.config.ts 核心配置
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import compression from 'vite-plugin-compression';
import visualizer from 'vite-plugin-visualizer';
import path from 'path';

export default defineConfig({
  plugins: [
    react(),
    compression(),
    visualizer(),
  ],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@pages': path.resolve(__dirname, './src/pages'),
      '@utils': path.resolve(__dirname, './src/utils'),
      '@services': path.resolve(__dirname, './src/services'),
      '@store': path.resolve(__dirname, './src/store'),
    },
  },
  build: {
    target: 'es2015',
    rollupOptions: {
      output: {
        manualChunks: {
          'vendor': ['react', 'react-dom', 'react-router-dom'],
          'antd': ['antd', '@ant-design/icons'],
          'echarts': ['echarts', 'zrender'],
        },
      },
    },
  },
});
```

### 3.8 代码规范与质量工具

#### ESLint配置

```javascript
// .eslintrc.js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react-hooks/recommended',
    'plugin:jsx-a11y/recommended',
    'prettier',
  ],
  plugins: ['react', '@typescript-eslint', 'react-hooks', 'jsx-a11y'],
  rules: {
    // React规则
    'react/react-in-jsx-scope': 'off',
    'react/prop-types': 'off',
    '@typescript-eslint/no-unused-vars': ['warn', { argsIgnorePattern: '^_' }],
    // 禁止 console（生产环境）
    'no-console': process.env.NODE_ENV === 'production' ? 'warn' : 'off',
  },
};
```

#### Prettier配置

```javascript
// .prettierrc
{
  "semi": true,
  "singleQuote": true,
  "printWidth": 100,
  "tabWidth": 2,
  "trailingComma": "es5",
  "bracketSpacing": true,
  "arrowParens": "always",
  "endOfLine": "lf"
}
```

### 3.9 其他辅助工具

| 类别      | 技术             | 版本    | 说明      |
| ------- | -------------- | ----- | ------- |
| 国际化     | i18next        | 23.0+ | 国际化框架   |
| 权限控制    | CASL           | 6.5+  | 权限管理    |
| 错误监控    | Sentry         | 7.0+  | 前端监控    |
| 日志      | loglevel       | 1.8+  | 日志框架    |
| 工具库     | lodash         | 4.17+ | 函数工具库   |
| 日期处理    | dayjs          | 1.11+ | 日期处理    |
| 拖拽      | dnd-kit        | 6.0+  | 拖拽排序    |
| 虚拟列表    | react-virtuoso | 4.0+  | 虚拟列表    |
| PDF导出   | jspdf          | 2.5+  | PDF生成   |
| Excel导出 | xlsx           | 0.18+ | Excel操作 |

---

## 四、核心功能模块划分

### 4.1 模块边界定义与职责说明

```
┌─────────────────────────────────────────────────────────────────────┐
│                         系统模块架构                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  ┌───────────────┐                                                  │
│  │   公共模块     │  提供基础设施：组件库、工具函数、配置服务           │
│  │  (Common)     │                                                  │
│  └───────┬───────┘                                                  │
│          │                                                           │
│  ┌───────┴───────────────────────────────────────────────────┐      │
│  │                      业务模块层                              │      │
│  ├─────────────┬─────────────┬─────────────┬───────────────┤      │
│  │  认证模块    │  仪表盘模块   │  商品模块    │  订单模块     │      │
│  │  (Auth)     │ (Dashboard) │  (Product)  │   (Order)    │      │
│  ├─────────────┼─────────────┼─────────────┼───────────────┤      │
│  │  流量模块    │  用户模块     │  报表模块    │  预警模块     │      │
│  │  (Traffic)  │   (User)    │  (Report)   │   (Alert)    │      │
│  └─────────────┴─────────────┴─────────────┴───────────────┘      │
│                                                                      │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                      系统模块层                                 │  │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌───────────┐ │  │
│  │  │  用户管理    │ │  角色管理    │  │  系统配置    │ │  操作日志  │  │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └───────────┘ │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 各模块核心功能点

#### 4.2.1 认证模块 (Auth Module)

```typescript
// 模块职责：用户认证、会话管理、权限获取

// 功能点清单
interface AuthFeatures {
  // 登录登出
  login: (credentials: LoginCredentials) => Promise<AuthToken>;
  logout: () => Promise<void>;
  refreshToken: () => Promise<AuthToken>;

  // SSO集成
  ssoLogin: (provider: 'CAS' | 'OAuth2' | 'LDAP') => Promise<AuthToken>;

  // 会话管理
  getSession: () => Promise<UserSession>;
  validateSession: () => Promise<boolean>;

  // 权限获取
  getPermissions: () => Promise<Permission[]>;
  getUserInfo: () => Promise<UserInfo>;
}
```

#### 4.2.2 仪表盘模块 (Dashboard Module)

```typescript
// 模块职责：数据概览、实时监控、KPI卡片展示

// 功能点清单
interface DashboardFeatures {
  // 概览数据
  getOverviewData: (params: DateRangeParams) => Promise<OverviewData>;
  getKPICards: (params: KPICardParams) => Promise<KPICard[]>;

  // 趋势图
  getTrendChart: (params: TrendChartParams) => Promise<TrendChartData>;
  getRealtimeData: () => Promise<RealtimeData>;

  // 实时监控
  subscribeRealtime: (callback: (data: RealtimeData) => void) => void;
  unsubscribeRealtime: () => void;

  // Dashboard布局
  saveDashboardLayout: (layout: LayoutConfig[]) => Promise<void>;
  getDashboardLayout: () => Promise<LayoutConfig[]>;
}
```

#### 4.2.3 商品模块 (Product Module)

```typescript
// 模块职责：商品数据分析、销售排行、库存分析

// 功能点清单
interface ProductFeatures {
  // 商品列表
  getProductList: (params: ProductListParams) => Promise<PaginatedResult<Product>>;
  getProductDetail: (id: string) => Promise<ProductDetail>;

  // 商品分析
  getSalesTrend: (params: SalesTrendParams) => Promise<SalesTrendData>;
  getSalesRanking: (params: RankingParams) => Promise<ProductRanking[]>;
  getCategoryAnalysis: (params: CategoryParams) => Promise<CategoryData[]>;

  // 库存分析
  getInventoryStatus: (params: InventoryParams) => Promise<InventoryStatus>;
  getLowStockAlerts: () => Promise<LowStockAlert[]>;

  // 商品对比
  compareProducts: (ids: string[]) => Promise<ProductCompare>;
}
```

#### 4.2.4 订单模块 (Order Module)

```typescript
// 模块职责：订单查询、状态管理、订单分析

// 功能点清单
interface OrderFeatures {
  // 订单查询
  getOrderList: (params: OrderListParams) => Promise<PaginatedResult<Order>>;
  getOrderDetail: (id: string) => Promise<OrderDetail>;

  // 订单操作
  updateOrderStatus: (id: string, status: OrderStatus) => Promise<void>;
  cancelOrder: (id: string, reason: string) => Promise<void>;

  // 订单分析
  getOrderTrend: (params: OrderTrendParams) => Promise<OrderTrendData>;
  getOrderStatistics: (params: StatisticsParams) => Promise<OrderStatistics>;

  // 退款管理
  getRefundList: (params: RefundParams) => Promise<PaginatedResult<Refund>>;
  processRefund: (id: string, action: RefundAction) => Promise<void>;
}
```

#### 4.2.5 流量模块 (Traffic Module)

```typescript
// 模块职责：流量分析、来源分析、用户行为分析

// 功能点清单
interface TrafficFeatures {
  // 流量概览
  getTrafficOverview: (params: DateRangeParams) => Promise<TrafficOverview>;
  getSourceDistribution: (params: SourceParams) => Promise<SourceData[]>;

  // 页面分析
  getPageViews: (params: PageParams) => Promise<PaginatedResult<PageView>>;
  getPagePerformance: (params: PerformanceParams) => Promise<PagePerformance>;

  // 事件追踪
  trackEvent: (event: TrackEvent) => Promise<void>;
  getEventAnalysis: (params: EventParams) => Promise<EventAnalysis>;

  // 用户行为
  getBehaviorPath: (params: PathParams) => Promise<BehaviorPath[]>;
  getSessionRecording: (sessionId: string) => Promise<SessionData>;
}
```

#### 4.2.6 用户模块 (User Module)

```typescript
// 模块职责：用户分析、分群管理、留存分析

// 功能点清单
interface UserFeatures {
  // 用户概览
  getUserOverview: (params: DateRangeParams) => Promise<UserOverview>;
  getUserGrowth: (params: GrowthParams) => Promise<UserGrowth>;

  // 用户分析
  getUserPortrait: (userId: string) => Promise<UserPortrait>;
  getUserSegments: () => Promise<UserSegment[]>;

  // 留存分析
  getRetentionReport: (params: RetentionParams) => Promise<RetentionData>;
  getChurnAnalysis: (params: ChurnParams) => Promise<ChurnData>;

  // 用户分群
  createSegment: (config: SegmentConfig) => Promise<string>;
  updateSegment: (id: string, config: SegmentConfig) => Promise<void>;
}
```

### 4.3 模块间交互关系

```
模块交互图

┌──────────┐     ┌──────────┐     ┌──────────┐
│   Auth   │────▶│ Dashboard│────▶│ Product  │
│   模块   │      │   模块    │     │   模块   │
└──────────┘     └──────────┘     └──────────┘
     │                │                │
     │ 共享用户信息     │ 共享筛选条件     │ 共享时间范围
     │                │                │
     ▼                ▼                ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│   User   │◀───▶│   Order  │────▶│ Traffic  │
│   模块    │     │   模块   │      │   模块   │
└──────────┘     └──────────┘     └──────────┘
     │                │                │
     │ 用户行为数据     │ 订单关联分析     │ 流量来源
     │                │                │
     ▼                ▼                ▼
┌──────────────────────────────────────────────────┐
│                    Report 模块                    │
│         (报表中心 - 聚合各模块数据进行报表展示)       │
└──────────────────────────────────────────────────┘
```

### 4.4 关键页面原型说明

#### 4.4.1 数据概览页

```
┌────────────────────────────────────────────────────────────────────┐
│  Header: Logo | 导航菜单 | 搜索 | 通知 | 用户头像                    │
├────────────────────────────────────────────────────────────────────┤
│ ┌──────────────────────────────────────────────────────────────┐   │
│ │  日期选择  |  昨日  今日  7日  30日  自定义   |  刷新  导出   │   │
│ └──────────────────────────────────────────────────────────────┘   │
│                                                                     │
│ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐        │
│ │ GMV     │ │ 订单量   │ │ 客单价   │ │转化率    │ │ 活跃用户 │        │
│ │ ¥1,234K │ │ 56,789  │ │ ¥218    │ │ 3.24%   │ │ 12,345  │        │
│ │ ▲12.3%  │ │ ▲5.6%   │ │ ▼1.2%   │ │ ▲0.3%   │ │ ▲8.9%   │        │
│ └─────────┘ └─────────┘ └─────────┘ └─────────┘ └─────────┘        │
│                                                                     │
│ ┌──────────────────────────────────────────────────────────────┐   │
│ │                       销售趋势图                              │   │
│ │  [折线图 - 展示GMV、订单量、客单价趋势]                        │   │
│ └──────────────────────────────────────────────────────────────┘   │
│                                                                     │
│ ┌───────────────────────┐ ┌───────────────────────────────────┐   │
│ │    商品销售排行        │ │         订单状态分布              │   │
│ │  [横向柱状图]          │ │       [环形图/饼图]               │   │
│ └───────────────────────┘ └───────────────────────────────────┘   │
│                                                                     │
│ ┌──────────────────────────────────────────────────────────────┐   │
│ │                     实时数据播报                              │   │
│ │  [滚动列表 - 最新订单、预警信息]                               │   │
│ └──────────────────────────────────────────────────────────────┘   │
│                                                                     │
└────────────────────────────────────────────────────────────────────┘
```

---

## 五、数据流程设计

### 5.1 全局状态设计与数据模型定义

#### 5.1.1 Store架构

```typescript
// store 结构设计
interface RootState {
  // 认证状态
  auth: AuthState;
  // 用户信息
  user: UserState;
  // 权限状态
  permission: PermissionState;
  // UI状态
  ui: UIState;
  // 通知状态
  notification: NotificationState;
  // 业务状态（按模块划分）
  business: {
    dashboard: DashboardState;
    product: ProductState;
    order: OrderState;
    traffic: TrafficState;
    user: UserAnalyticsState;
    report: ReportState;
  };
}
```

#### 5.1.2 数据模型

```typescript
// 核心数据模型定义

// 用户模型
interface User {
  id: string;
  username: string;
  email: string;
  phone?: string;
  avatar?: string;
  roles: Role[];
  permissions: Permission[];
  department?: string;
  position?: string;
  lastLoginTime?: string;
  status: 'active' | 'inactive' | 'banned';
}

// 权限模型
interface Permission {
  id: string;
  code: string;        // 权限码，如 'product:view'
  name: string;         // 权限名称
  type: 'menu' | 'button' | 'api';
  resource?: string;    // 资源标识
  action?: string;     // 操作类型
}

// 角色模型
interface Role {
  id: string;
  code: string;
  name: string;
  description?: string;
  permissions: Permission[];
  isSystem: boolean;    // 系统内置角色
}

// 订单模型
interface Order {
  id: string;
  orderNo: string;
  userId: string;
  userName: string;
  totalAmount: number;
  status: OrderStatus;
  items: OrderItem[];
  shippingAddress: Address;
  createdAt: string;
  updatedAt: string;
  payTime?: string;
  shipTime?: string;
  receiveTime?: string;
}

// 商品模型
interface Product {
  id: string;
  sku: string;
  name: string;
  category: Category;
  price: number;
  cost: number;
  stock: number;
  salesCount: number;
  images: string[];
  status: 'online' | 'offline' | 'deleted';
  createdAt: string;
  updatedAt: string;
}

// 报表模型
interface Report {
  id: string;
  name: string;
  type: 'standard' | 'custom';
  config: ReportConfig;
  createdBy: string;
  createdAt: string;
  updatedAt: string;
  isShared: boolean;
  shareRoles?: string[];
}
```

### 5.2 API接口设计规范

#### 5.2.1 RESTful API规范

```
API 设计规范

基础URL: /api/v1

资源端点：
├── 用户管理
│   GET    /users              # 获取用户列表
│   GET    /users/:id          # 获取用户详情
│   POST   /users              # 创建用户
│   PUT    /users/:id          # 更新用户
│   DELETE /users/:id          # 删除用户
│
├── 商品管理
│   GET    /products           # 获取商品列表
│   GET    /products/:id       # 获取商品详情
│   POST   /products           # 创建商品
│   PUT    /products/:id       # 更新商品
│   DELETE /products/:id       # 删除商品
│   GET    /products/ranking   # 获取商品排行
│
├── 订单管理
│   GET    /orders             # 获取订单列表
│   GET    /orders/:id         # 获取订单详情
│   PUT    /orders/:id/status  # 更新订单状态
│   POST   /orders/:id/cancel  # 取消订单
│   GET    /orders/statistics  # 订单统计
│
├── 数据分析
│   GET    /analytics/overview     # 数据概览
│   GET    /analytics/trend        # 趋势数据
│   GET    /analytics/distribution # 分布数据
│
└── 报表管理
    GET    /reports            # 获取报表列表
    POST   /reports            # 创建报表
    GET    /reports/:id        # 获取报表详情
    PUT    /reports/:id        # 更新报表
    DELETE /reports/:id        # 删除报表
    POST   /reports/:id/export # 导出报表
```

#### 5.2.2 API请求响应格式

```typescript
// 统一响应格式
interface ApiResponse<T> {
  code: number;        // 状态码：0成功，非0失败
  message: string;     // 响应消息
  data: T;             // 响应数据
  timestamp: number;   // 时间戳
  requestId: string;   // 请求ID，用于追踪
}

// 分页响应格式
interface PaginatedResponse<T> {
  code: number;
  message: string;
  data: {
    list: T[];
    pagination: {
      page: number;
      pageSize: number;
      total: number;
      totalPages: number;
    };
  };
  timestamp: number;
  requestId: string;
}

// 错误响应格式
interface ErrorResponse {
  code: number;
  message: string;
  details?: Record<string, string[]>;  // 字段级错误
  timestamp: number;
  requestId: string;
}
```

#### 5.2.3 数据转换策略

```typescript
// API数据转换层

// 1. 请求转换：前端模型 -> API格式
const transformRequest = <T>(data: T): APIFormat => {
  return {
    ...data,
    // 转换时间格式
    createdAt: data.createdAt ? new Date(data.createdAt).toISOString() : undefined,
    // 转换金额（分->元或元->分）
    amount: data.amount ? data.amount / 100 : undefined,
  };
};

// 2. 响应转换：API格式 -> 前端模型
const transformResponse = <T>(data: APIFormat): T => {
  return {
    ...data,
    // 转换时间格式
    createdAt: data.createdAt ? new Date(data.createdAt) : undefined,
    // 转换金额（元->分或分->元）
    amount: data.amount ? data.amount * 100 : undefined,
  };
};

// 3. 列表转换
const transformList = <T>(list: APIFormat[], transformer: (item: APIFormat) => T): T[] => {
  return list.map(transformer);
};
```

### 5.3 数据流图

```
┌─────────────────────────────────────────────────────────────────────┐
│                          数据流架构                                  │
└─────────────────────────────────────────────────────────────────────┘

用户交互
    │
    ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         React Components                             │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐                │
│  │  页面    │  │  业务   │  │  通用    │  │  图表    │                │
│  │ 组件     │  │  组件   │  │  组件    │  │  组件    │                │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘                │
└───────┼────────────┼────────────┼────────────┼─────────────────────┘
        │            │            │            │
        │ Props/     │ Props/     │ Props/     │ Props/
        │ Events     │ Events     │ Events     │ Events
        ▼            ▼            ▼            ▼
┌─────────────────────────────────────────────────────────────────────┐
│                           Hooks Layer                                │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐    │
│  │  业务逻辑Hooks   │  │  状态管理Hooks   │   │  数据获取Hooks  │    │
│  │ useDashboard()  │  │ useSelector()   │  │ useQuery()     │    │
│  │ useProduct()    │  │ useDispatch()   │  │ useMutation()  │    │
│  │ useOrder()      │  │                 │  │ useInfinite()  │    │
│  └────────┬────────┘  └────────-┬────────┘  └───────-─┬────────┘    │
└───────────┼─────────────────────┼─────────────────────┼──────────────┘
            │                     │                     │
            │ Dispatch Action     │ Query Key           │ API Call
            ▼                     ▼                     ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         State Management                             │
│  ┌─────────────────────┐    ┌─────────────────────┐                │
│  │     Redux Store      │    │   React Query Cache │                │
│  │  ┌─────────────────┐ │    │  ┌─────────────────┐ │                │
│  │  │  Auth Slice     │ │    │  │  Query Cache   │ │                │
│  │  │  User Slice     │ │    │  │  - dashboard   │ │                │
│  │  │  UI Slice       │ │    │  │  - products    │ │                │
│  │  │  Business Slice  │ │    │  │  - orders      │ │                │
│  │  └─────────────────┘ │    │  │  - ...         │ │                │
│  └─────────────────────┘    │  └─────────────────┘ │                │
│                             └─────────────────────┘                  │
└─────────────────────────────────────────────────────────────────────┘
            │                     │
            │                     │ Fetch/Subscribe
            ▼                     ▼
┌─────────────────────────────────────────────────────────────────────┐
│                          API Layer                                    │
│  ┌─────────────────────────────────────────────────────────────────┐ │
│  │                         Axios Instance                           │ │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐                │ │
│  │  │ Interceptor│  │   Router    │  │  Middleware │                │ │
│  │  │  Request   │  │             │  │             │                │ │
│  │  └────────────┘  └────────────┘  └────────────┘                │ │
│  └─────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
            │
            │ HTTP/HTTPS
            ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       Backend Services                                │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐            │
│  │   Auth   │  │   Data   │  │  Report  │  │   File   │            │
│  │  Service │  │  Service │  │  Service │  │  Service │            │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘            │
│         │            │            │            │                    │
│         ▼            ▼            ▼            ▼                    │
│  ┌──────────────────────────────────────────────────────────────┐   │
│  │                        Database / Cache                       │   │
│  │                   MySQL / Redis / Elasticsearch              │   │
│  └──────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.4 本地存储策略

```typescript
// 存储策略定义

interface StorageStrategy {
  // Token管理
  tokens: {
    storage: 'memory' | 'sessionStorage';
    key: 'auth_tokens';
    ttl: number;  // 过期时间(ms)
  };

  // 用户偏好设置
  preferences: {
    storage: 'localStorage';
    key: 'user_preferences';
    include: ['theme', 'language', 'dashboardLayout'];
  };

  // 缓存数据
  cache: {
    storage: 'sessionStorage';
    key: 'data_cache';
    maxSize: '5MB';
    ttl: 5 * 60 * 1000;  // 5分钟
  };

  // 表单草稿
  formDrafts: {
    storage: 'sessionStorage';
    key: 'form_drafts';
    ttl: 30 * 60 * 1000;  // 30分钟
  };
}

// 存储操作封装
class StorageManager {
  static set(key: string, value: any, strategy: StorageStrategy): void;
  static get(key: string): any;
  static remove(key: string): void;
  static clearExpired(): void;
  static getSize(): number;
}
```

---

## 六、开发规范制定

### 6.1 代码命名规范

#### 6.1.1 组件命名

```typescript
// 组件命名规范

// 1. 文件命名
// - React组件文件：PascalCase.tsx
// - 组件样式文件：PascalCase.module.less
// - 组件测试文件：PascalCase.test.tsx

// 示例
Dashboard.tsx              // 组件文件
Dashboard.module.less      // 样式文件
Dashboard.test.tsx         // 测试文件
Dashboard.types.ts        // 类型定义文件

// 2. 组件命名
// - 组件名：PascalCase
// - Props接口：ComponentNameProps
// - 组件返回值：ComponentNameFC

// 示例
interface DashboardProps {
  title?: string;
  data?: DashboardData;
}

export const Dashboard: React.FC<DashboardProps> = (props) => {
  // ...
};

// 3. 业务组件命名
// - 使用业务场景命名
// - 包含组件功能描述

// 示例
ProductListTable          // 商品列表表格
OrderStatusBadge          // 订单状态徽章
SalesTrendChart           // 销售趋势图表
KPICard                   // KPI卡片
DateRangeSelector         // 日期范围选择器
```

#### 6.1.2 函数命名

```typescript
// 函数命名规范

// 1. 事件处理函数
// - 命名格式：handle + 动作 + Target
// - 示例
handleClickSubmit()
handleChangeInput()
handleSelectOption()
handleBlurValidate()

// 2. 业务逻辑函数
// - 使用动词或动词短语
// - 命名应描述其功能

// 示例
fetchUserList()
createOrder()
updateProductStatus()
deleteSelectedItems()
exportTableData()
refreshDashboard()

// 3. 数据转换函数
// - 命名格式：transform + 目标类型 / format + 目标类型

// 示例
transformUserToTableRow()
formatCurrency()
formatDate()
parseQueryParams()
serializeFormData()

// 4. 验证函数
// - 命名格式：validate + 验证目标 / is + 状态 / check + 检查项

// 示例
validateEmail()
isLoggedIn()
checkPermission()
canAccessResource()
```

#### 6.1.3 变量命名

```typescript
// 变量命名规范

// 1. 常规变量：camelCase
const userName = '张三';
const isLoading = false;
const hasError = false;

// 2. 常量：UPPER_SNAKE_CASE
const MAX_RETRY_COUNT = 3;
const API_BASE_URL = '/api/v1';
const DATE_FORMAT = 'YYYY-MM-DD';

// 3. 枚举变量：PascalCase
enum OrderStatus {
  Pending = 'pending',
  Paid = 'paid',
  Shipped = 'shipped',
  Completed = 'completed',
}

// 4. 类型变量：PascalCase
type UserId = string;
type Timestamp = number;
interface UserList = User[];

// 5. 布尔变量：is/has/should/can + 名词/形容词
const isVisible = true;
const hasPermission = true;
const shouldRefresh = false;
const canEdit = true;

// 6. 数组变量：复数名词或 + List/Array 后缀
const users = [];
const productList = [];
const selectedIds = [];

// 7. 字典/Map变量：Map后缀或 + Map
const userMap = new Map();
const productById = {};
```

### 6.2 组件设计规范

#### 6.2.1 组件拆分原则

```typescript
// 组件拆分原则

// 1. 单一职责原则
// 每个组件只负责一个功能或展示一个独立的UI元素

// Good: 分离组件
const UserProfile = () => (
  <div>
    <Avatar src={user.avatar} />
    <UserInfo name={user.name} email={user.email} />
    <UserStats stats={user.stats} />
  </div>
);

// Bad: 职责过多
const UserProfile = () => {
  // 渲染头像、用户信息、用户统计、社交链接、设置按钮...
  // 这应该拆分为多个组件
};

// 2. 复用性原则
// 优先设计可复用组件，再考虑业务组件

// 3. 组合优于继承
// 使用组合来共享逻辑，而非继承

// 4. Props设计原则
// - 最小化Props数量
// - 提供合理的默认Props
// - 使用Props类型约束
```

#### 6.2.2 组件复用策略

```typescript
// 组件复用层级

// Level 1: 原子组件 (Atoms)
// 最基础的UI元素，不可再分

// 示例
Button, Input, Select, Checkbox, Radio, Badge, Tag, Icon, Avatar, etc.

// Level 2: 分子组件 (Molecules)
// 组合原子组件形成简单组合

// 示例
SearchInput = Input + Button + Icon
FormField = Label + Input + ErrorMessage
DataCard = Avatar + Title + Description + Action
ProductItem = Image + Title + Price + AddToCart

// Level 3: 组织组件 (Organisms)
// 组合分子组件形成复杂模块

// 示例
Header = Logo + Nav + SearchInput + UserMenu + Notification
ProductList = DataCard[] + Pagination + FilterBar
UserTable = Table + Checkbox + ActionButtons + Pagination

// Level 4: 模板组件 (Templates)
// 页面级别布局，不包含业务逻辑

// 示例
DashboardLayout = Header + Sider + Content + Footer
ListPageTemplate = FilterBar + Table + Pagination
FormPageTemplate = Form + ActionButtons
```

### 6.3 状态管理规范

```typescript
// 状态管理规范

// 1. 状态放置原则
// - 全局状态：用户信息、权限、主题、语言等
// - 模块状态：各业务模块的列表、筛选条件、分页等
// - 组件状态：UI交互状态、表单输入等

// 2. Redux状态更新规范
// 使用 Redux Toolkit，遵循以下规范：

// slice.ts
const dashboardSlice = createSlice({
  name: 'dashboard',
  initialState,
  reducers: {
    // 同步action
    setDateRange: (state, action: PayloadAction<DateRange>) => {
      state.dateRange = action.payload;
    },
    setLoading: (state, action: PayloadAction<boolean>) => {
      state.loading = action.payload;
    },
    // 异步action
    fetchDashboardData: createAsyncThunk(
      'dashboard/fetchData',
      async (params: FetchParams, { rejectWithValue }) => {
        try {
          const response = await dashboardAPI.getData(params);
          return response.data;
        } catch (error) {
          return rejectWithValue(error.message);
        }
      }
    ),
  },
});

// 3. Selector设计规范
// - 优先使用createSelector创建记忆化selector
// - 避免在组件中直接访问深层状态

// Good
const selectOrderList = createSelector(
  [selectOrderState],
  (orderState) => orderState.list
);

const selectTodayOrders = createSelector(
  [selectOrderList],
  (orders) => orders.filter(o => isToday(o.createdAt))
);

// 4. 状态初始化规范
// - 使用loading、error、data分离的状态模式
// - 提供初始状态的常量定义

// 示例状态结构
interface OrderState {
  list: Order[];
  selectedId: string | null;
  filters: OrderFilters;
  pagination: Pagination;
  loading: boolean;
  error: string | null;
}
```

### 6.4 Git工作流规范

#### 6.4.1 分支策略

```
Git Flow 分支模型

┌─────────────────────────────────────────────────────────────────┐
│                        Main Branches                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  main (生产分支)                                                 │
│  ├── tag: v1.0.0                                                │
│  ├── tag: v1.1.0                                                │
│  └── tag: v2.0.0                                                │
│                                                                  │
│  develop (开发分支)                                              │
│  ├── merge: feature/login                                       │
│  ├── merge: feature/dashboard                                   │
│  └── merge: release/v1.2.0                                      │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│                       Feature Branches                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  feature/{功能名}                                                │
│  ├── feature/auth-login                                         │
│  ├── feature/dashboard-overview                                 │
│  ├── feature/product-list                                       │
│  └── feature/order-export                                       │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│                       Release Branches                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  release/{版本号}                                                │
│  ├── release/v1.0.0                                             │
│  └── release/v1.1.0                                             │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│                        Hotfix Branches                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  hotfix/{问题描述}                                               │
│  ├── hotfix/fix-login-bug                                       │
│  └── hotfix/fix-chart-render-error                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

#### 6.4.2 提交信息规范

```bash
# 提交信息格式
# <type>(<scope>): <subject>
#
# [type]
# - feat: 新功能
# - fix: 修复bug
# - docs: 文档更新
# - style: 代码格式（不影响功能）
# - refactor: 重构（不是功能也不是bug修复）
# - perf: 性能优化
# - test: 测试相关
# - chore: 构建或辅助工具的变动
#
# [scope]
# - 模块范围，如: auth, dashboard, product, order 等
#
# [subject]
# - 简短描述，不超过50字符

# 示例
feat(auth): add login with SSOsupport
fix(dashboard): fix date picker range selection bug
docs(readme): update API documentation
style(product): format code with prettier
refactor(order): extract order status constants
perf(table): implement virtual scrolling for large datasets
test(product): add unit tests for ProductList
chore(deps): upgrade antd to v5.0.0

# 带body的提交
feat(order): add order export functionality

Added CSV and Excel export support for order list.
- Support export with custom date range
- Support export with selected columns
- Add export progress indicator

# 带footer的提交
fix(user): resolve permission check issue

Closes #123
Related to #456
```

### 6.5 文档编写规范

```typescript
// 1. 组件文档规范 (使用JSDoc/TypeDoc)

/**
 * 数据卡片组件
 *
 * @description 展示关键业务指标的数据卡片组件，支持趋势展示和点击跳转
 * @example
 * ```tsx
 * <DataCard
 *   title="今日销售额"
 *   value={123456}
 *   trend={12.5}
 *   format="currency"
 *   onClick={() => navigate('/sales')}
 * />
 * ```
 *
 * @author 张三
 * @version 1.0.0
 */
interface DataCardProps {
  /** 卡片标题 */
  title: string;
  /** 数值 */
  value: number;
  /** 趋势百分比，正数表示上升，负数表示下降 */
  trend?: number;
  /** 数值格式化类型 */
  format?: 'number' | 'currency' | 'percent';
  /** 点击事件 */
  onClick?: () => void;
}

// 2. API文档规范

/**
 * 获取仪表盘概览数据
 *
 * @description 获取指定时间范围内的仪表盘核心指标数据
 *
 * @param {Object} params - 请求参数
 * @param {string} params.startDate - 开始日期 (YYYY-MM-DD)
 * @param {string} params.endDate - 结束日期 (YYYY-MM-DD)
 * @param {string[]} [params.metrics] - 指标列表，不传则返回全部
 *
 * @returns {Promise<OverviewData>} 概览数据
 *
 * @throws {ApiError} 400 - 参数错误
 * @throws {ApiError} 401 - 未登录
 * @throws {ApiError} 403 - 无权限
 * @throws {ApiError} 500 - 服务器错误
 *
 * @example
 * ```typescript
 * const data = await getDashboardOverview({
 *   startDate: '2024-01-01',
 *   endDate: '2024-01-31',
 *   metrics: ['gmv', 'orders', 'visitors']
 * });
 * ```
 */
async function getDashboardOverview(params: OverviewParams): Promise<OverviewData>
```

---

## 七、性能优化策略

### 7.1 首屏加载优化方案

```typescript
// 首屏加载优化策略

// 1. 代码分割 (Code Splitting)
const Dashboard = lazy(() => import('./pages/Dashboard'));
const ProductList = lazy(() => import('./pages/ProductList'));
const OrderDetail = lazy(() => import('./pages/OrderDetail'));

// 2. 路由级别的懒加载
// React Router 6 配置
<Routes>
  <Route
    path="/dashboard"
    element={
      <Suspense fallback={<PageSkeleton />}>
        <Dashboard />
      </Suspense>
    }
  />
</Routes>

// 3. 组件级别的懒加载
const HeavyChart = lazy(() => import('./components/HeavyChart'));

// 4. 预加载策略
// 预测用户行为，提前加载可能访问的页面
const prefetchDashboard = () => {
  import('./pages/Dashboard');
};

// 在合适的时机调用
<Link to="/dashboard" onMouseEnter={prefetchDashboard}>Dashboard</Link>

// 5. 资源预加载
<link rel="prefetch" href="/api/dashboard" />
<link rel="preload" as="script" href="/static/chunk-vendor.js" />

// 6. Tree Shaking配置
// vite.config.ts
export default {
  build: {
    treeShaking: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
        },
      },
    },
  },
};
```

### 7.2 渲染性能优化

```typescript
// 渲染性能优化

// 1. React.memo 使用
// 包装纯展示组件，避免不必要的重渲染
const ProductCard = memo<ProductCardProps>(({ product, onSelect }) => {
  return (
    <div onClick={() => onSelect(product.id)}>
      <img src={product.image} alt={product.name} />
      <span>{product.name}</span>
    </div>
  );
});

// 2. useMemo 使用
// 缓存计算结果
const sortedProducts = useMemo(() => {
  return products
    .filter(p => p.status === 'active')
    .sort((a, b) => b.sales - a.sales);
}, [products]);

// 3. useCallback 使用
// 缓存回调函数，避免子组件不必要的重渲染
const handleSelect = useCallback((productId: string) => {
  setSelectedId(productId);
}, []);

// 4. 虚拟列表 (Virtual List)
// 大数据量列表渲染优化
import { Virtuoso } from 'react-virtuoso';

const ProductList = () => (
  <Virtuoso
    data={products}
    itemContent={(index, product) => (
      <ProductCard key={product.id} product={product} />
    )}
  />
);

// 5. 避免不必要的嵌套
// Bad: 过度嵌套的组件导致重渲染链过长
<Parent>
  <Child>
    <GrandChild>
      <DeepComponent />
    </GrandChild>
  </Child>
</Parent>

// Good: 扁平化组件结构，按需传递props
<Parent>
  <FlatComponentA />
  <FlatComponentB />
</Parent>

// 6. 列表渲染优化
// 使用 key 而不是 index
// Bad: 使用index作为key
{data.map((item, index) => <Item key={index} {...item} />)}

// Good: 使用唯一id作为key
{data.map((item) => <Item key={item.id} {...item} />)}
```

### 7.3 网络请求优化

```typescript
// 网络请求优化

// 1. 请求缓存策略 (React Query)
const { data } = useQuery({
  queryKey: ['dashboard', dateRange],
  queryFn: () => fetchDashboard(dateRange),
  staleTime: 5 * 60 * 1000,     // 5分钟内不重新获取
  cacheTime: 30 * 60 * 1000,    // 30分钟后从缓存移除
  refetchOnWindowFocus: false, // 窗口聚焦时不自动刷新
});

// 2. 请求去重
// React Query 自动去重相同请求

// 3. 请求取消
const { data } = useQuery({
  queryKey: ['product', productId],
  queryFn: ({ signal }) => fetchProduct(productId, signal),
  enabled: !!productId, // 条件执行
});

// 4. 乐观更新
const mutation = useMutation({
  mutationFn: updateOrder,
  onMutate: async (newOrder) => {
    // 立即更新UI
    await queryClient.cancelQueries(['order', newOrder.id]);
    const previousOrder = queryClient.getQueryData(['order', newOrder.id]);
    queryClient.setQueryData(['order', newOrder.id], newOrder);
    return { previousOrder };
  },
  onError: (err, newOrder, context) => {
    // 回滚到之前的状态
    queryClient.setQueryData(['order', newOrder.id], context.previousOrder);
  },
});

// 5. 请求合并 (Batching)
// 合并短时间内多个相同请求

// 6. 分页加载
const {
  fetchNextPage,
  hasNextPage,
  isFetchingNextPage,
} = useInfiniteQuery({
  queryKey: ['products'],
  queryFn: fetchProducts,
  getNextPageParam: (lastPage) => lastPage.nextCursor,
});
```

### 7.4 资源优化

```typescript
// 资源优化策略

// 1. 图片优化
// - 使用 WebP 格式
// - 图片懒加载
// - 响应式图片 srcset
<img
  src="image.webp"
  srcSet="image-1x.webp 1x, image-2x.webp 2x"
  loading="lazy"
  alt="description"
/>

// 2. 第三方库按需引入
// Bad: 导入完整库
import _ from 'lodash';
_.cloneDeep(data);

// Good: 按需导入
import cloneDeep from 'lodash/cloneDeep';

// 3. Ant Design 按需加载
// vite-plugin-import 配置
import { defineConfig } from 'vite';
import importPlugin from 'vite-plugin-import';

export default defineConfig({
  plugins: [
    importPlugin([
      {
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: false,
      },
    ]),
  ],
});

// 4. 字体优化
// - 字体子集化
// - font-display: swap
@font-face {
  font-family: 'CustomFont';
  src: url('/fonts/custom.woff2') format('woff2');
  font-display: swap;
}

// 5. CSS优化
// - CSS Modules 避免样式冲突
// - 提取公共CSS
// - CSS压缩

// 6. 资源压缩
// vite-plugin-compression
import compression from 'vite-plugin-compression';

export default {
  plugins: [
    compression({
      algorithm: 'gzip',
      ext: '.gz',
    }),
  ],
};
```

### 7.5 性能监控指标与优化目标

```typescript
// 性能监控指标

// Core Web Vitals
interface WebVitalsMetrics {
  // Largest Contentful Paint (LCP)
  // 衡量加载性能，目标 < 2.5s
  LCP: number;

  // First Input Delay (FID)
  // 衡量交互性，目标 < 100ms
  FID: number;

  // Cumulative Layout Shift (CLS)
  // 衡量视觉稳定性，目标 < 0.1
  CLS: number;

  // Time to First Byte (TTFB)
  // 目标 < 800ms
  TTFB: number;

  // First Contentful Paint (FCP)
  // 目标 < 1.8s
  FCP: number;
}

// 性能监控SDK集成
// 使用 web-vitals 库收集指标
import { getCLS, getFID, getLCP, getTTFB, getFCP } from 'web-vitals';

const sendToAnalytics = (metric: Metric) => {
  // 发送到分析服务
  navigator.sendBeacon('/analytics', JSON.stringify(metric));
};

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getLCP(sendToAnalytics);
getTTFB(sendToAnalytics);
getFCP(sendToAnalytics);

// 性能预算
interface PerformanceBudget {
  // 资源大小限制
  totalBundleSize: '500KB',
  initialJS: '300KB',
  initialCSS: '50KB',

  // 加载时间限制
  LCP: 2500,       // ms
  FID: 100,        // ms
  CLS: 0.1,

  // 请求数量限制
  initialRequests: 20,
  maxConcurrentRequests: 6,
}
```

---

## 八、测试计划

### 8.1 单元测试策略

```typescript
// 单元测试配置 (Jest + React Testing Library)

// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/tests/setup.ts'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
    '\\.(css|less|scss)$': 'identity-obj-proxy',
  },
  testPathIgnorePatterns: ['/node_modules/', '/dist/'],
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/vite-env.d.ts',
  ],
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 70,
      lines: 70,
      statements: 70,
    },
  },
};

// 测试示例
// components/DataCard.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { DataCard } from './DataCard';

describe('DataCard', () => {
  it('should render title and value', () => {
    render(<DataCard title="销售额" value={10000} />);
    expect(screen.getByText('销售额')).toBeInTheDocument();
    expect(screen.getByText('10,000')).toBeInTheDocument();
  });

  it('should render trend indicator', () => {
    render(<DataCard title="销售额" value={10000} trend={5.5} />);
    const trendElement = screen.getByText(/5.5%/);
    expect(trendElement).toBeInTheDocument();
  });

  it('should call onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<DataCard title="销售额" value={10000} onClick={handleClick} />);
    fireEvent.click(screen.getByText('销售额'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

### 8.2 组件测试覆盖范围

```typescript
// 组件测试覆盖要求

// 1. 通用业务组件 (100%覆盖)
// - DataCard
// - ChartContainer
// - FilterBar
// - Table (分页、排序、筛选)
// - Modal / Drawer

// 2. 表单组件 (100%覆盖)
// - Form 验证逻辑
// - 输入组件值变化
// - 错误提示展示

// 3. 图表组件 (80%覆盖)
// - 数据渲染正确性
// - 交互功能 (tooltip, zoom, legend)
// - 空数据和加载状态

// 4. 页面组件 (70%覆盖)
// - 主要业务流程
// - 权限控制展示
// - 错误边界展示
```

### 8.3 集成测试方案

```typescript
// 集成测试方案

// 使用 MSW (Mock Service Worker) 模拟API
import { setupServer } from 'msw/node';
import { render, screen, waitFor } from '@testing-library/react';
import { server } from '../mocks/server';
import { Dashboard } from './Dashboard';

// 测试 API 集成
describe('Dashboard Integration', () => {
  const server = setupServer();

  beforeAll(() => server.listen());
  afterEach(() => server.resetHandlers());
  afterAll(() => server.close());

  it('should load and display dashboard data', async () => {
    render(<Dashboard />);

    // 等待数据加载
    await waitFor(() => {
      expect(screen.getByText('¥1,234,567')).toBeInTheDocument();
    });
  });

  it('should handle API errors gracefully', async () => {
    server.use(
      rest.get('/api/dashboard', (req, res, ctx) => {
        return res(ctx.status(500), ctx.json({ message: 'Server Error' }));
      })
    );

    render(<Dashboard />);

    await waitFor(() => {
      expect(screen.getByText('加载失败')).toBeInTheDocument();
    });
  });
});
```

### 8.4 E2E测试框架选型

| 框架 | 优点 | 缺点 | 推荐场景 |
|------|------|------|----------|
| Cypress | API完善、调试方便 | 浏览器限制 | **推荐：快速迭代项目** |
| Playwright | 多浏览器支持、性能好 | 学习成本 | 跨浏览器测试 |
| Selenium | 成熟稳定 | 配置复杂、慢 | 遗留项目 |

#### 关键流程测试用例

```typescript
// e2e/cypress/integration/login.spec.ts
describe('登录流程', () => {
  it('应该能够使用用户名密码登录', () => {
    cy.visit('/login');
    cy.get('[data-testid=username-input]').type('admin');
    cy.get('[data-testid=password-input]').type('password123');
    cy.get('[data-testid=login-button]').click();
    cy.url().should('include', '/dashboard');
    cy.get('[data-testid=user-menu]').should('contain', '管理员');
  });

  it('应该显示登录错误提示', () => {
    cy.visit('/login');
    cy.get('[data-testid=username-input]').type('invalid');
    cy.get('[data-testid=password-input]').type('wrong');
    cy.get('[data-testid=login-button]').click();
    cy.get('[data-testid=error-message]').should('contain', '用户名或密码错误');
  });
});

// e2e/cypress/integration/dashboard.spec.ts
describe('仪表盘功能', () => {
  beforeEach(() => {
    cy.login();
    cy.visit('/dashboard');
  });

  it('应该展示核心KPI卡片', () => {
    cy.get('[data-testid=kpi-card]').should('have.length', 5);
    cy.get('[data-testid=kpi-card]').first().should('contain', 'GMV');
  });

  it('应该支持日期范围筛选', () => {
    cy.get('[data-testid=date-range-picker]').click();
    cy.get('[data-testid=preset-7d]').click();
    cy.waitForAPI('@dashboard');
    cy.get('[data-testid=chart]').should('be.visible');
  });

  it('应该能够导出数据', () => {
    cy.get('[data-testid=export-button]').click();
    cy.get('[data-testid=export-option]').contains('导出Excel').click();
    cy.verifyDownload('dashboard-export.xlsx');
  });
});
```

### 8.5 性能测试与兼容性测试

```typescript
// 性能测试

// 1. Lighthouse CI 集成
// .lighthouserc.js
module.exports = {
  ci: {
    collect: {
      url: ['http://localhost:3000/dashboard'],
      numberOfRuns: 3,
    },
    assert: {
      assertions: {
        'categories:performance': ['error', { minScore: 0.9 }],
        'categories:accessibility': ['error', { minScore: 0.9 }],
        'first-contentful-paint': ['error', { maxNumericValue: 2000 }],
        'largest-contentful-paint': ['error', { maxNumericValue: 2500 }],
      },
    },
  },
};

// 2. 压力测试
// 使用 k6 进行API压力测试
// load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 100 },
    { duration: '1m', target: 100 },
    { duration: '30s', target: 0 },
  ],
};

export default function () {
  const res = http.get('http://localhost/api/dashboard');
  check(res, {
    'status was 200': (r) => r.status === 200,
    'response time < 500ms': (r) => r.timing.duration < 500,
  });
  sleep(1);
}

// 3. 兼容性测试矩阵
const BROWSER_MATRIX = [
  { browser: 'Chrome', version: 'latest' },
  { browser: 'Chrome', version: '90' },
  { browser: 'Firefox', version: 'latest' },
  { browser: 'Firefox', version: '88' },
  { browser: 'Safari', version: 'latest' },
  { browser: 'Safari', version: '14' },
  { browser: 'Edge', version: 'latest' },
];

// 使用 BrowserStack 或 Sauce Labs 进行跨浏览器测试
```

---

## 九、项目里程碑及时间节点安排

### 9.1 分阶段开发任务分解

```
┌─────────────────────────────────────────────────────────────────────┐
│                         项目阶段划分                                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Phase 1: 需求分析与设计        ████░░░░░░░░░░░░░░░░░░░  15%         │
│  Phase 2: 基础架构搭建          ██████░░░░░░░░░░░░░░░░░░  15%         │
│  Phase 3: 核心功能开发          ██████████████░░░░░░░░░░  35%         │
│  Phase 4: 联调测试              ██████████░░░░░░░░░░░░░░  20%         │
│  Phase 5: 上线准备              ████░░░░░░░░░░░░░░░░░░░░  15%         │
│                                                                      │
│  总工期：20周 (约5个月)                                               │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 9.2 各阶段详细任务

#### Phase 1: 需求分析与设计 (Week 1-3)

| 任务      | 交付物      | 责任人   | 周数       |
| ------- | -------- | ----- | -------- |
| 需求调研    | 需求调研报告   | 产品经理  | Week 1   |
| 用户角色分析  | 角色权限矩阵   | 产品经理  | Week 1   |
| 功能清单梳理  | 功能清单文档   | 产品经理  | Week 1   |
| 交互原型设计  | 高保真原型    | UI设计师 | Week 2   |
| 技术方案设计  | 技术方案文档   | 技术负责人 | Week 2-3 |
| 数据库设计   | ER图、数据字典 | 后端架构师 | Week 3   |
| API接口设计 | API文档    | 后端架构师 | Week 3   |

#### Phase 2: 基础架构搭建 (Week 4-6)

| 任务     | 交付物                | 责任人    | 周数       |
| ------ | ------------------ | ------ | -------- |
| 项目初始化  | Git仓库、基础框架         | 前端负责人  | Week 4   |
| 组件库搭建  | 基础组件库              | 前端开发   | Week 4-5 |
| 状态管理配置 | Redux配置            | 前端开发   | Week 4   |
| 路由配置   | 路由架构               | 前端开发   | Week 4   |
| UI框架集成 | Ant Design集成       | 前端开发   | Week 4   |
| 图表库集成  | ECharts封装          | 前端开发   | Week 5   |
| 工程化配置  | ESLint、Prettier、CI | DevOps | Week 5   |
| 权限系统开发 | 权限组件、守卫            | 前端开发   | Week 5-6 |
| 登录模块开发 | 登录页面、认证            | 前端开发   | Week 6   |

#### Phase 3: 核心功能开发 (Week 7-14)

| 任务     | 交付物         | 责任人  | 周数         |
| ------ | ----------- | ---- | ---------- |
| 仪表盘开发  | Dashboard页面 | 前端开发 | Week 7-8   |
| 商品分析模块 | Product模块   | 前端开发 | Week 8-9   |
| 订单分析模块 | Order模块     | 前端开发 | Week 9-10  |
| 流量分析模块 | Traffic模块   | 前端开发 | Week 10-11 |
| 用户分析模块 | User模块      | 前端开发 | Week 11-12 |
| 报表中心模块 | Report模块    | 前端开发 | Week 12-13 |
| 预警中心模块 | Alert模块     | 前端开发 | Week 13-14 |
| 系统设置模块 | System模块    | 前端开发 | Week 14    |

#### Phase 4: 联调测试 (Week 15-18)

| 任务    | 交付物    | 责任人   | 周数         |
| ----- | ------ | ----- | ---------- |
| 接口联调  | 联调报告   | 前后端   | Week 15    |
| 功能测试  | 测试报告   | 测试工程师 | Week 15-16 |
| 性能测试  | 性能报告   | 测试工程师 | Week 16-17 |
| 安全测试  | 安全报告   | 安全工程师 | Week 17    |
| Bug修复 | 修复记录   | 前端开发  | Week 17-18 |
| 集成测试  | 集成测试报告 | 测试工程师 | Week 18    |

#### Phase 5: 上线准备 (Week 19-20)

| 任务   | 交付物       | 责任人    | 周数         |
| ---- | --------- | ------ | ---------- |
| 文档编写 | 用户手册、运维文档 | 技术文档   | Week 19    |
| 部署配置 | 部署脚本、环境配置 | DevOps | Week 19    |
| 灰度发布 | 灰度方案执行    | DevOps | Week 19-20 |
| 监控配置 | 监控大盘      | DevOps | Week 20    |
| 正式上线 | 上线报告      | 全体     | Week 20    |

### 9.3 进度跟踪与风险预警

```typescript
// 进度跟踪机制

// 1. 每日站会
// - 同步昨日完成、今日计划、阻塞问题
// - 15分钟限时

// 2. 周报机制
// - 周五提交周报
// - 包含：进度、风险、下周计划

// 3. Sprint评审
// - 每两周一次
// - 展示完成的Sprint内容

// 4. 里程碑评审
// - 每个Phase结束进行评审
// - 评审通过方可进入下一阶段

// 风险评估矩阵
interface RiskItem {
  id: string;
  description: string;
  probability: 'low' | 'medium' | 'high';
  impact: 'low' | 'medium' | 'high';
  mitigation: string;
  owner: string;
}

// 风险预警阈值
const RISK_THRESHOLDS = {
  schedule: {
    warning: 10,   // 进度落后10%
    critical: 20,  // 进度落后20%
  },
  quality: {
    bugCount: {
      critical: 50,  // 致命bug超过50个
      high: 100,     // 高级bug超过100个
    },
  },
  scope: {
    changeRate: 15,  // 需求变更超过15%
  },
};
```

---

## 十、部署流程设计

### 10.1 环境配置

```yaml
# 环境配置矩阵

environments:
  development:
    name: 开发环境
    domain: dev.bi.example.com
    api_base: https://dev-api.example.com
    debug: true
    sourcemap: true
    mock_enabled: true

  staging:
    name: 测试环境
    domain: staging.bi.example.com
    api_base: https://staging-api.example.com
    debug: false
    sourcemap: true
    mock_enabled: false

  preproduction:
    name: 预发布环境
    domain: pre.bi.example.com
    api_base: https://api.example.com
    debug: false
    sourcemap: false
    mock_enabled: false

  production:
    name: 生产环境
    domain: bi.example.com
    api_base: https://api.example.com
    debug: false
    sourcemap: false
    mock_enabled: false
```

### 10.2 CI/CD流程设计

```yaml
# GitHub Actions CI/CD 配置
# .github/workflows/main.yml

name: CI/CD Pipeline

on:
  push:
    branches: [main, develop, 'release/*']
  pull_request:
    branches: [main, develop]

env:
  NODE_VERSION: '20'
  PNPM_VERSION: '8'

jobs:
  # 1. 代码检查
  lint:
    name: Lint & Type Check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: pnpm/action-setup@v2
        with:
          version: ${{ env.PNPM_VERSION }}
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm lint
      - run: pnpm typecheck

  # 2. 单元测试
  test:
    name: Unit Tests
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v3
      - uses: pnpm/action-setup@v2
        with:
          version: ${{ env.PNPM_VERSION }}
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm test:coverage

  # 3. 构建
  build:
    name: Build
    runs-on: ubuntu-latest
    needs: lint
    steps:
      - uses: actions/checkout@v3
      - uses: pnpm/action-setup@v2
        with:
          version: ${{ env.PNPM_VERSION }}
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - name: Build Production
        if: github.ref == 'refs/heads/main'
        run: pnpm build --mode production
        env:
          VITE_API_BASE: https://api.example.com
      - name: Build Staging
        if: github.ref == 'refs/heads/develop'
        run: pnpm build --mode staging
        env:
          VITE_API_BASE: https://staging-api.example.com
      - uses: actions/upload-artifact@v3
        with:
          name: dist
          path: dist/

  # 4. 部署到测试环境
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    needs: [build, test]
    if: github.ref == 'refs/heads/develop'
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: dist
      - name: Deploy to Staging Server
        run: |
          scp -r dist/* user@staging-server:/var/www/bi-staging/
          ssh user@staging-server "cd /var/www/bi-staging && pm2 restart bi-app"

  # 5. 部署到生产环境
  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: [build]
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: dist
      - name: Deploy to Production Server
        run: |
          scp -r dist/* user@production-server:/var/www/bi/
          ssh user@production-server "cd /var/www/bi && pm2 restart bi-app"
```

### 10.3 版本管理与发布策略

```bash
# 版本号规范 (Semantic Versioning)
# MAJOR.MINOR.PATCH
# MAJOR: 不兼容的重大变更
# MINOR: 向后兼容的功能新增
# PATCH: 向后兼容的bug修复

# 发布流程
# 1. 创建发布分支
git checkout -b release/v1.0.0

# 2. 更新版本号
npm version patch  # 或 minor / major

# 3. 创建Tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# 4. 推送Tag
git push origin v1.0.0

# 5. 合并到main和develop
git checkout main
git merge release/v1.0.0
git checkout develop
git merge release/v1.0.0

# 6. 删除发布分支
git branch -d release/v1.0.0
```

### 10.4 回滚机制设计

```bash
# 回滚策略

# 1. 蓝绿部署
# 维护两套环境：蓝（当前）和绿（新版本）
# 发布时切换流量，回滚时切回原环境

# 2. 金丝雀发布
# 先将新版本部署到少量服务器
# 验证无误后逐步扩大范围

# 3. 快速回滚脚本
#!/bin/bash
# rollback.sh

set -e

VERSION=$1
SERVERS=("server1" "server2" "server3")

echo "Rolling back to version: $VERSION"

for server in ${SERVERS[@]}; do
    echo "Rolling back $server..."
    ssh user@$server "cd /var/www/bi && pm2 stop bi-app"
    ssh user@$server "cd /var/www/bi-backup && cp -r v$VERSION/* ../bi/"
    ssh user@$server "cd /var/www/bi && pm2 start bi-app"
done

echo "Rollback completed"

# 4. 数据库回滚
# 使用数据库迁移工具
# 支持向上和向下迁移
```

### 10.5 监控告警体系

```typescript
// 监控告警配置

// 1. 前端监控 (Sentry)
import * as Sentry from '@sentry/react';

Sentry.init({
  dsn: 'https://xxx@sentry.io/xxx',
  environment: process.env.NODE_ENV,
  integrations: [
    new Sentry.BrowserTracing(),
    new Sentry.Replay(),
  ],
  tracesSampleRate: 0.1,
  replaysSessionSampleRate: 0.1,
  replaysOnErrorSampleRate: 1.0,
});

// 2. 性能监控
// 使用 web-vitals 上报核心指标
import { getCLS, getFID, getLCP } from 'web-vitals';

function sendToAnalytics({ name, delta, id }: Metric) {
  ga('send', 'event', {
    eventCategory: 'Web Vitals',
    eventAction: name,
    eventValue: Math.round(name === 'CLS' ? delta * 1000 : delta),
    nonInteraction: true,
    transport: 'beacon',
  });
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getLCP(sendToAnalytics);

// 3. 告警规则配置
const ALERT_RULES = {
  frontend: [
    {
      name: 'High Error Rate',
      condition: 'error_count > 10 per minute',
      severity: 'critical',
      channel: ['slack', 'email'],
    },
    {
      name: 'High LCP',
      condition: 'p95(LCP) > 4000',
      severity: 'warning',
      channel: ['slack'],
    },
  ],
  backend: [
    {
      name: 'API Error Rate',
      condition: 'error_rate > 5%',
      severity: 'critical',
      channel: ['slack', 'phone'],
    },
    {
      name: 'API Latency',
      condition: 'p95(latency) > 1000ms',
      severity: 'warning',
      channel: ['slack'],
    },
  ],
};

// 4. 监控大盘
// - 实时用户监控 (RUM)
// - API调用监控
// - 前端性能监控
// - 错误率监控
```

---

## 十一、潜在风险评估与应对方案

### 11.1 技术风险

| 风险项 | 概率 | 影响 | 应对措施 |
|--------|------|------|----------|
| 新技术踩坑 | 中 | 高 | 1. 技术选型阶段充分调研<br>2. POC验证关键难点<br>3. 预留技术攻关时间 |
| 大数据量性能问题 | 高 | 高 | 1. 前端虚拟列表<br>2. 数据采样<br>3. 服务端分页<br>4. 提前做性能测试 |
| 浏览器兼容性 | 中 | 中 | 1. 使用兼容性好的技术<br>2. 自动化测试覆盖主流浏览器<br>3. 降级策略 |
| 第三方库依赖 | 低 | 中 | 1. 评估库的质量和活跃度<br>2. 锁定版本<br>3. 准备替代方案 |
| 微前端架构复杂度 | 中 | 高 | 1. 评估是否真正需要<br>2. 小范围试点<br>3. 完善的监控体系 |

### 11.2 进度风险

| 风险项 | 概率 | 影响 | 应对措施 |
|--------|------|------|----------|
| 需求变更 | 高 | 中 | 1. 需求冻结机制<br>2. 变更评估流程<br>3. 控制迭代范围 |
| 人员变动 | 中 | 高 | 1. 代码Review机制<br>2. 文档和注释规范<br>3. 知识传承计划 |
| 联调延期 | 中 | 中 | 1. 提前约定接口契约<br>2. Mock服务支持<br>3. 预留Buffer |
| 测试发现大量Bug | 中 | 中 | 1. 持续集成，早发现问题<br>2. 严格的代码Review<br>3. 单元测试覆盖 |
| 关键路径延误 | 低 | 高 | 1. 关键路径识别<br>2. 并行开发<br>3. 每日进度跟踪 |

### 11.3 质量风险

| 风险项 | 概率 | 影响 | 应对措施 |
|--------|------|------|----------|
| 代码质量下降 | 中 | 高 | 1. ESLint/Prettier强制执行<br>2. CodeReview制度<br>3. 定期重构 |
| 技术债累积 | 高 | 中 | 1. 每个迭代预留技术债处理时间<br>2. 定期重构<br>3. 技术方案设计评审 |
| 用户体验问题 | 中 | 高 | 1. 交互设计评审<br>2. 用户测试<br>3. 数据分析驱动优化 |
| 安全性漏洞 | 低 | 极高 | 1. 安全编码规范<br>2. 安全测试<br>3. 渗透测试 |

### 11.4 资源风险

| 风险项 | 概率 | 影响 | 应对措施 |
|--------|------|------|----------|
| 人力不足 | 中 | 高 | 1. 提前招聘<br>2. 合理分配任务<br>3. 外部资源支持 |
| 第三方服务不可用 | 低 | 中 | 1. 评估SLA<br>2. 多服务商备份<br>3. 本地缓存策略 |
| 服务器资源不足 | 低 | 中 | 1. 提前容量规划<br>2. 自动扩缩容<br>3. 监控告警 |
| 预算超支 | 低 | 中 | 1. 详细预算规划<br>2. 定期成本审核<br>3. 优先级排序 |

---

## 附录

### A. 技术栈完整清单

```json
{
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "@reduxjs/toolkit": "^2.0.0",
    "react-redux": "^9.0.0",
    "@tanstack/react-query": "^5.0.0",
    "axios": "^1.6.0",
    "antd": "^5.12.0",
    "@ant-design/icons": "^5.2.0",
    "echarts": "^5.4.0",
    "echarts-for-react": "^3.0.0",
    "dayjs": "^1.11.0",
    "i18next": "^23.0.0",
    "react-i18next": "^13.0.0",
    "casl": "^6.5.0",
    "@sentry/react": "^7.0.0",
    "lodash": "^4.17.21",
    "react-virtuoso": "^4.0.0",
    "jspdf": "^2.5.0",
    "xlsx": "^0.18.0",
    "web-vitals": "^3.0.0"
  },
  "devDependencies": {
    "typescript": "^5.3.0",
    "vite": "^5.0.0",
    "@vitejs/plugin-react": "^4.2.0",
    "eslint": "^8.0.0",
    "prettier": "^3.0.0",
    "@typescript-eslint/eslint-plugin": "^6.0.0",
    "@typescript-eslint/parser": "^6.0.0",
    "jest": "^29.0.0",
    "@testing-library/react": "^14.0.0",
    "@testing-library/jest-dom": "^6.0.0",
    "msw": "^2.0.0",
    "cypress": "^13.0.0"
  }
}
```

### B. 参考文档

- [React官方文档](https://react.dev)
- [Ant Design组件库](https://ant.design)
- [Redux Toolkit文档](https://redux-toolkit.js.org)
- [React Query文档](https://tanstack.com/query)
- [ECharts文档](https://echarts.apache.org)
- [React Router文档](https://reactrouter.com)
- [TypeScript handbook](https://www.typescriptlang.org/docs/handbook/)
- [Vite构建工具](https://vitejs.dev)

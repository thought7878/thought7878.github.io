
>京东商智平台权限体系架构与前端实现方案详解

## 一、权限体系架构 总览

### 1.1 权限模型设计

京东商智平台采用 **RBAC（基于角色的访问控制）+ ABAC（基于属性的访问控制）** 的混合权限模型：

```
权限体系架构
├── RBAC（Role-Based Access Control）
│   ├── 用户 → 角色 → 权限（标准三层模型）
│   └── 支持多角色分配，权限取并集
│
└── ABAC（Attribute-Based Access Control）
    ├── 基于用户属性（部门、职级、地区）
    ├── 基于资源属性（数据敏感度、业务线）
    └── 基于环境属性（时间、IP、设备）
```

### 1.2 权限类型划分

#### （1）功能权限（菜单权限）
- **作用**：*控制用户可见的菜单项和页面*
- **粒度**：*页面级/路由级*、模块级
- **示例**：
  - `dashboard:view` - 数据概览
  - `product:view` - 商品分析
  - `order:view` - 订单分析
  - `system:manage` - 系统管理

#### （2）操作权限（按钮权限）
- **作用**：*控制页面内的操作按钮和功能*
- **粒度**：*组件级、按钮级*
- **示例**：
  - `product:create` - 创建商品
  - `product:edit` - 编辑商品
  - `product:delete` - 删除商品
  - `report:export` - 导出报表

#### （3）数据权限
- **作用**：控制用户可访问的数据范围
- **粒度**：行级、列级
- **类型**：
  - **全部数据**：超级管理员
  - **本部门数据**：部门主管
  - **本人数据**：普通员工
  - **自定义范围**：按地区、业务线划分

#### （4）API权限
- **作用**：控制后端接口的访问权限
- **实现**：JWT Token + 接口级权限校验

### 1.3 权限层级结构

```
L1 - 超级管理员
├── 全部功能权限
├── 全部数据权限
├── 角色管理权限
└── 系统配置权限

L2 - 数据管理员
├── 数据分析功能
├── 报表管理功能
├── 指标配置功能
└── 本部门数据权限

L3 - 运营总监
├── 全业务模块查看
├── 团队数据概况
├── 策略制定功能
└── 团队数据权限

L4 - 运营专员
├── 本职相关数据模块
├── 基础分析功能
└── 本人负责数据权限

L5 - 数据分析师
├── 高级分析功能
├── 自定义报表
├── 可视化工具
└── 全量数据查看（只读）

L6 - 访客
└── 公开Dashboard查看（只读）
```

### 1.4 角色定义与数据模型

```typescript
// 权限模型
interface Permission {
  id: string;
  code: string;        // 权限码，如 'product:view'
  name: string;        // 权限名称
  type: 'menu/page' | 'button/component' | 'api' | 'data';
  resource?: string;   // 资源标识
  action?: string;     // 操作类型：view/create/edit/delete/export
}

// 角色模型
interface Role {
  id: string;
  code: string;        // 角色编码，如 'admin', 'operator'
  name: string;        // 角色名称
  level: number;       // 权限等级 L1-L6
  description?: string;
  permissions: Permission[];
  isSystem: boolean;   // 系统内置角色（不可删除）
}

// 用户模型
interface User {
  id: string;
  username: string;
  roles: Role[];
  permissions: Permission[];  // 所有角色权限的并集
  department?: string;
  dataScope?: 'all' | 'department' | 'self' | 'custom';
  status: 'active' | 'inactive' | 'banned';
}
```

### 1.5 权限管理流程

```
权限分配流程：
1. 创建角色 → 定义角色名称、等级、描述
2. 配置权限 → 为角色分配功能权限、数据权限
3. 分配用户 → 将用户关联到角色
4. 权限生效 → 用户登录时获取权限信息
5. 权限变更 → 实时更新或下次登录生效

权限校验流程：
1. 用户登录 → 获取Token和权限列表
2. 访问资源 → 前端校验（路由/菜单/按钮）
3. 发起请求 → 后端校验（API权限）
4. 返回结果 → 权限不足返回403
```

---

## 二、前端权限体系 实现方案

### 2.1 权限数据的获取与存储

#### （1）登录时 获取权限数据

```typescript
// 登录接口响应结构
interface LoginResponse {
  token: string;
  refreshToken: string;
  user: {
    id: string;
    username: string;
    roles: string[];
    permissions: string[];  // 权限码数组
    dataScope: string;
  };
}

// 登录流程
const handleLogin = async (credentials: LoginCredentials) => {
  const response = await authAPI.login(credentials);
  
  // 存储Token
  localStorage.setItem('token', response.token);
  localStorage.setItem('refreshToken', response.refreshToken);
  
  // 存储用户信息和权限
  localStorage.setItem('user', JSON.stringify(response.user));
  
  // 或使用Redux/Zustand进行状态管理
  dispatch(setUser(response.user));
};
```

#### （2）权限存储策略

```typescript
// 存储方案对比
// localStorage - 简单场景，持久化存储
// sessionStorage - 会话级存储，关闭浏览器清除
// Cookie - 支持HttpOnly，更安全
// Redux Store - 内存存储，刷新页面丢失

// 推荐方案：Token存Cookie，权限信息存Redux + localStorage备份
const PERMISSION_STORAGE_KEY = 'app_permissions';

export const permissionStorage = {
  // 保存权限
  save: (permissions: string[]) => {
    localStorage.setItem(PERMISSION_STORAGE_KEY, JSON.stringify(permissions));
  },
  
  // 获取权限
  get: (): string[] => {
    const data = localStorage.getItem(PERMISSION_STORAGE_KEY);
    return data ? JSON.parse(data) : [];
  },
  
  // 清除权限
  clear: () => {
    localStorage.removeItem(PERMISSION_STORAGE_KEY);
  },
  
  // 检查权限
  has: (permission: string): boolean => {
    const permissions = permissionStorage.get();
    return permissions.includes(permission);
  }
};
```

### 2.2 权限校验逻辑实现

#### （1）路由权限守卫

当前项目的 [PermissionGuard](file:///Users/ll/Desktop/资料/编程/仓库/我的/sz/bi-dashboard/src/routes/guards/PermissionGuard.tsx#L1-L46) 实现：

```typescript
import React from 'react';
import { Navigate, Outlet } from 'react-router-dom';
import { Alert } from 'antd';

interface PermissionGuardProps {
  requiredRoles?: string[];
  requiredPermissions?: string[];
}

const PermissionGuard: React.FC<PermissionGuardProps> = ({ 
  requiredRoles = [], 
  requiredPermissions = [] 
}) => {
  const userStr = localStorage.getItem('user');
  const user = userStr ? JSON.parse(userStr) : null;

  // 检查用户是否存在
  if (!user) {
    return <Navigate to="/login" replace />;
  }

  // 检查角色权限
  const hasRole = requiredRoles.length === 0 || 
                  requiredRoles.includes(user.role);
  
  // 检查操作权限
  const hasPermission = requiredPermissions.length === 0 ||
                        requiredPermissions.some(p => 
                          user.permissions?.includes(p)
                        );

  if (!hasRole || !hasPermission) {
    return (
      <div style={{ padding: '40px', textAlign: 'center' }}>
        <Alert
          message="权限不足"
          description="您没有权限访问此页面，请联系管理员"
          type="error"
          showIcon
        />
      </div>
    );
  }

  return <Outlet />;
};
```

#### （2）路由配置示例

```typescript
// 路由配置
const routes = [
  {
    path: '/login',
    element: <Login />,
  },
  {
    path: '/',
    element: <AuthGuard />,  // 认证守卫
    children: [
      {
        path: '/',
        element: <AppLayout />,
        children: [
          {
            path: '/dashboard',
            element: <Dashboard />,  // 无需特殊权限
          },
          {
            path: '/product',
            element: <PermissionGuard requiredPermissions={['product:view']} />,
            children: [{ path: '/product', element: <Product /> }],
          },
          {
            path: '/system',
            element: <PermissionGuard requiredRoles={['admin']} />,
            children: [{ path: '/system', element: <System /> }],
          },
        ],
      },
    ],
  },
];
```

### 2.3 基于权限的UI动态渲染机制

#### （1）权限组件封装

```typescript
// Authorized 组件 - 权限控制容器
interface AuthorizedProps {
  permission?: string;
  role?: string;
  fallback?: React.ReactNode;
  children: React.ReactNode;
}

const Authorized: React.FC<AuthorizedProps> = ({
  permission,
  role,
  fallback = null,
  children
}) => {
  const user = useUser();  // 从全局状态获取用户信息
  
  // 检查权限
  const hasPermission = !permission || 
    user.permissions?.includes(permission);
  
  // 检查角色
  const hasRole = !role || 
    user.roles?.includes(role);

  if (hasPermission && hasRole) {
    return <>{children}</>;
  }

  return <>{fallback}</>;
};

// 使用示例
<Authorized permission="product:create">
  <Button type="primary">创建商品</Button>
</Authorized>
```

#### （2）权限Hook实现

```typescript
// usePermission Hook
const usePermission = () => {
  const user = useAppSelector(state => state.user);

  // 检查单个权限
  const hasPermission = (permission: string): boolean => {
    return user.permissions?.includes(permission) ?? false;
  };

  // 检查多个权限（满足任一）
  const hasAnyPermission = (permissions: string[]): boolean => {
    return permissions.some(p => user.permissions?.includes(p));
  };

  // 检查多个权限（全部满足）
  const hasAllPermissions = (permissions: string[]): boolean => {
    return permissions.every(p => user.permissions?.includes(p));
  };

  // 检查角色
  const hasRole = (role: string): boolean => {
    return user.roles?.includes(role) ?? false;
  };

  return {
    hasPermission,
    hasAnyPermission,
    hasAllPermissions,
    hasRole,
    user
  };
};

// 使用示例
const ProductList = () => {
  const { hasPermission } = usePermission();
  
  return (
    <div>
      <h1>商品列表</h1>
      {hasPermission('product:create') && (
        <Button>创建商品</Button>
      )}
      {hasPermission('product:export') && (
        <Button>导出</Button>
      )}
    </div>
  );
};
```

#### （3）按钮级权限控制

```typescript
// 高阶组件方式
const withPermission = (WrappedComponent, permission) => {
  return (props) => {
    const { hasPermission } = usePermission();
    
    if (!hasPermission(permission)) {
      return null;  // 无权限不渲染
    }
    
    return <WrappedComponent {...props} />;
  };
};

// 使用示例
const ExportButton = withPermission(Button, 'product:export');
<ExportButton icon={<ExportOutlined />}>导出</ExportButton>
```

### 2.4 菜单权限控制

#### （1）动态菜单生成

```typescript
// 菜单配置
const menuConfig = [
  {
    key: '/dashboard',
    icon: <DashboardOutlined />,
    label: '数据概览',
    permission: 'dashboard:view'
  },
  {
    key: '/product',
    icon: <ShoppingOutlined />,
    label: '商品分析',
    permission: 'product:view'
  },
  {
    key: '/system',
    icon: <SettingOutlined />,
    label: '系统设置',
    permission: 'system:manage'
  },
];

// 动态菜单组件
const DynamicMenu: React.FC = () => {
  const { hasPermission } = usePermission();
  const location = useLocation();

  // 过滤有权限的菜单项
  const visibleMenus = menuConfig.filter(menu => 
    hasPermission(menu.permission)
  );

  return (
    <Menu
      mode="inline"
      selectedKeys={[location.pathname]}
      items={visibleMenus}
    />
  );
};
```

#### （2）当前项目的菜单实现优化

当前 [Sider组件](file:///Users/ll/Desktop/资料/编程/仓库/我的/sz/bi-dashboard/src/components/layout/Sider/Sider.tsx#L1-L93) 的菜单是静态的，可以优化为动态菜单：

```typescript
const Sider: React.FC<SiderProps> = ({ collapsed, location }) => {
  const { hasPermission } = usePermission();
  
  const allMenuItems = [
    {
      key: '/dashboard',
      icon: <DashboardOutlined />,
      label: <Link to="/dashboard">数据概览</Link>,
      permission: 'dashboard:view'
    },
    {
      key: '/product',
      icon: <ShoppingOutlined />,
      label: <Link to="/product">商品分析</Link>,
      permission: 'product:view'
    },
    // ... 其他菜单项
    {
      key: '/system',
      icon: <SettingOutlined />,
      label: <Link to="/system">系统设置</Link>,
      permission: 'system:manage'
    },
  ];

  // 根据权限过滤菜单 
  const menuItems = allMenuItems.filter(item =>
    !item.permission || hasPermission(item.permission)
  );

  return (
    <div className="sider-container">
      {!collapsed && (
        <div className="sider-logo">
          <Title level={5}>BI 平台</Title>
        </div>
      )}
      <Menu
        mode="inline"
        selectedKeys={[getSelectedKey()]}
        items={menuItems}
      />
    </div>
  );
};
```

### 2.5 接口请求权限拦截处理

#### （1）Axios拦截器配置

```typescript
import axios from 'axios';

// 创建Axios实例
const apiClient = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: 10000,
});

// 请求拦截器 - 添加Token
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// 响应拦截器 - 处理权限错误
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response) {
      const { status } = error.response;
      
      switch (status) {
        case 401:
          // 未授权，清除Token并跳转登录
          localStorage.removeItem('token');
          localStorage.removeItem('user');
          window.location.href = '/login';
          break;
          
        case 403:
          // 权限不足
          message.error('您没有权限执行此操作');
          break;
          
        case 403:
          // Token过期，尝试刷新
          return refreshTokenAndRetry(error.config);
      }
    }
    return Promise.reject(error);
  }
);

// Token刷新机制
const refreshTokenAndRetry = async (originalConfig) => {
  try {
    const refreshToken = localStorage.getItem('refreshToken');
    const response = await axios.post('/auth/refresh', { refreshToken });
    
    const { token } = response.data;
    localStorage.setItem('token', token);
    
    // 重试原请求
    originalConfig.headers.Authorization = `Bearer ${token}`;
    return apiClient(originalConfig);
  } catch (error) {
    // 刷新失败，跳转登录
    localStorage.clear();
    window.location.href = '/login';
  }
};
```

### 2.6 数据权限实现

#### （1）数据范围过滤

```typescript
// 数据权限Hook
const useDataPermission = () => {
  const user = useAppSelector(state => state.user);

  // 获取数据范围
  const getDataScope = (): DataScope => {
    return user.dataScope || 'self';
  };

  // 构建查询参数
  const buildQueryParams = (baseParams: any) => {
    const scope = getDataScope();
    
    switch (scope) {
      case 'all':
        return baseParams;  // 无限制
      case 'department':
        return {
          ...baseParams,
          departmentId: user.departmentId
        };
      case 'self':
        return {
          ...baseParams,
          userId: user.id
        };
      default:
        return baseParams;
    }
  };

  return { getDataScope, buildQueryParams };
};

// 使用示例
const OrderList = () => {
  const { buildQueryParams } = useDataPermission();
  
  const fetchOrders = async () => {
    const params = buildQueryParams({
      page: 1,
      pageSize: 20,
    });
    
    const response = await orderAPI.getList(params);
    setOrders(response.data);
  };
};
```

### 2.7 性能优化措施

#### （1）权限数据缓存

```typescript
// 权限缓存策略
class PermissionCache {
  private cache: Map<string, boolean> = new Map();
  private ttl: number = 5 * 60 * 1000; // 5分钟
  private timestamp: number = 0;

  // 检查缓存是否有效
  isValid(): boolean {
    return Date.now() - this.timestamp < this.ttl;
  }

  // 获取权限
  get(permission: string): boolean | undefined {
    if (!this.isValid()) return undefined;
    return this.cache.get(permission);
  }

  // 设置权限
  set(permissions: string[]): void {
    this.cache.clear();
    permissions.forEach(p => this.cache.set(p, true));
    this.timestamp = Date.now();
  }

  // 清除缓存
  clear(): void {
    this.cache.clear();
    this.timestamp = 0;
  }
}

export const permissionCache = new PermissionCache();
```

#### （2）权限检查优化

```typescript
// 使用useMemo缓存权限检查结果
const PermissionButton: React.FC<{
  permission: string;
  children: React.ReactNode;
}> = ({ permission, children }) => {
  const user = useAppSelector(state => state.user);
  
  // 缓存权限检查结果
  const hasPermission = useMemo(() => {
    return user.permissions.includes(permission);
  }, [user.permissions, permission]);

  if (!hasPermission) return null;
  
  return <>{children}</>;
};
```

#### （3）路由懒加载

```typescript
// 路由懒加载 - 已在当前项目中实现
const Dashboard = lazy(() => import('../pages/Dashboard/Dashboard'));
const Product = lazy(() => import('../pages/Product/Product'));

// 配合Suspense使用
{
  path: '/dashboard',
  element: (
    <Suspense fallback={<Loading />}>
      <Dashboard />
    </Suspense>
  ),
}
```

#### （4）按需加载权限数据

```typescript
// 首次加载只获取基础权限
// 访问特定模块时再加载详细权限
const useModulePermission = (module: string) => {
  const [modulePermissions, setModulePermissions] = useState([]);
  
  useEffect(() => {
    const fetchModulePermissions = async () => {
      const response = await permissionAPI.getModulePermissions(module);
      setModulePermissions(response.data);
    };
    
    fetchModulePermissions();
  }, [module]);
  
  return modulePermissions;
};
```

---

## 三、实际应用场景与关键实现细节

### 3.1 场景一：运营专员查看商品数据

```
用户角色：运营专员（L4）
权限范围：
- 菜单权限：商品分析、订单分析
- 操作权限：查看、导出（不能删除、修改）
- 数据权限：仅查看负责的商品

实现流程：
1. 登录 → 获取权限列表
2. 菜单渲染 → 过滤无权限菜单项
3. 访问商品页 → 路由守卫校验权限
4. 页面渲染 → 隐藏删除/编辑按钮
5. 数据请求 → 自动添加用户ID过滤参数
```

### 3.2 场景二：管理员配置系统权限

```
用户角色：超级管理员（L1）
权限范围：全部权限

实现流程：
1. 访问系统设置 → 路由守卫校验admin角色
2. 显示角色管理 → 需要 system:role:manage 权限
3. 编辑角色权限 → 需要 system:permission:edit 权限
4. 保存配置 → 调用权限分配API
5. 权限生效 → 通知在线用户刷新权限
```

### 3.3 关键实现细节

#### （1）权限变更实时同步

```typescript
// WebSocket监听权限变更
const usePermissionSync = () => {
  const dispatch = useAppDispatch();
  
  useEffect(() => {
    const ws = new WebSocket(process.env.REACT_APP_WS_URL);
    
    ws.onmessage = (event) => {
      const message = JSON.parse(event.data);
      
      if (message.type === 'PERMISSION_UPDATED') {
        // 重新获取权限
        dispatch(fetchPermissions());
        message.info('您的权限已更新，页面将刷新');
      }
    };
    
    return () => ws.close();
  }, []);
};
```

#### （2）权限预检机制

```typescript
// 操作前预检权限，避免无效请求
const usePermissionCheck = () => {
  const { hasPermission } = usePermission();
  
  const checkBeforeAction = (
    permission: string,
    action: () => void,
    fallback?: () => void
  ) => {
    if (hasPermission(permission)) {
      action();
    } else {
      message.warning('您没有权限执行此操作');
      fallback?.();
    }
  };
  
  return { checkBeforeAction };
};

// 使用示例
const handleDelete = () => {
  checkBeforeAction(
    'product:delete',
    async () => {
      await productAPI.delete(productId);
      message.success('删除成功');
    }
  );
};
```

#### （3）权限审计日志

```typescript
// 记录权限相关操作
const logPermissionAction = (
  action: string,
  resource: string,
  result: 'success' | 'failed'
) => {
  const log = {
    userId: currentUser.id,
    action,
    resource,
    result,
    timestamp: Date.now(),
    ip: getUserIP(),
    userAgent: navigator.userAgent,
  };
  
  // 发送到审计服务
  auditAPI.log(log);
};
```

---

## 四、总结

京东商智平台的权限体系是一个多层次、多维度的复杂系统，前端实现需要关注以下核心要点：

1. **权限模型设计**：RBAC + ABAC 混合模型，灵活应对各种场景
2. **权限数据存储**：合理选择存储方案，平衡安全性和性能
3. **路由权限控制**：通过路由守卫实现页面级权限控制
4. **UI动态渲染**：通过组件、Hook、HOC等方式实现按钮级权限控制
5. **接口权限拦截**：通过Axios拦截器统一处理权限错误
6. **性能优化**：缓存、懒加载、按需加载等策略提升性能
7. **用户体验**：权限不足时提供友好提示，避免生硬的错误页面

当前项目已经实现了基础的认证守卫和权限守卫，后续可以根据实际需求逐步完善菜单权限、按钮权限、数据权限等功能。
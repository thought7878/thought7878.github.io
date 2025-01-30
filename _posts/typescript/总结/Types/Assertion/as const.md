### TypeScript 中的 `as const` 断言详解

**`as const`** 是 TypeScript 中的一种类型断言，用于将变量或表达式的类型锁定为**最具体的字面量类型**，**并标记所有属性为只读**（`readonly`）。它能防止类型被自动拓宽（Widening），常用于固定值集合、配置对象、联合类型等场景。

---

### 一、核心作用

#### 1. **锁定字面量类型**  
将值从宽泛类型（如 `string`、`number`）转换为具体的字面量类型（如 `"success"`、`42`）。  
```typescript
// 未使用 as const：类型为 string[]
const colors = ["red", "green", "blue"];

// 使用 as const：类型为 readonly ["red", "green", "blue"]
const colorsConst = ["red", "green", "blue"] as const;
```

#### 2. **属性只读化**  
所有属性变为 `readonly`，防止意外修改：  
```typescript
const user = {
  name: "Alice",
  role: "admin",
} as const;

user.role = "user"; // ❌ 错误：只读属性
```

#### 3. **阻止类型拓宽**  
避免 TypeScript 将字面量推断为宽泛类型：  
```typescript
// 未使用 as const：类型为 string
let status = "success";

// 使用 as const：类型为 "success"
let statusConst = "success" as const;
```

---

### 二、语法与用法

#### 1. **基本语法**  
```typescript
// 变量后直接断言
const array = [1, 2, 3] as const;

// 对象断言
const config = {
  env: "production",
  port: 3000,
} as const;

// 单值断言
const theme = "dark" as const;
```

#### 2. **与类型别名结合**  
生成精确的联合类型：  
```typescript
const LOG_LEVELS = ["debug", "info", "error"] as const;
type LogLevel = typeof LOG_LEVELS[number]; // "debug" | "info" | "error"
```

#### 3. **在函数中返回常量**  
确保返回值类型精确：  
```typescript
function getDefaultSettings() {
  return {
    theme: "light",
    language: "en",
  } as const;
}

type Settings = ReturnType<typeof getDefaultSettings>;
/* 类型为：
{
  readonly theme: "light";
  readonly language: "en";
}
*/
```

---

### 三、典型应用场景

#### 场景一：Redux Action 类型安全  
```typescript
const ActionTypes = {
  ADD_TODO: "ADD_TODO",
  REMOVE_TODO: "REMOVE_TODO",
} as const;

type ActionType = typeof ActionTypes[keyof typeof ActionTypes]; // "ADD_TODO" | "REMOVE_TODO"

interface AddTodoAction {
  type: typeof ActionTypes.ADD_TODO;
  payload: string;
}
```

#### 场景二：配置对象锁定  
```typescript
const AppConfig = {
  apiBaseUrl: "https://api.example.com",
  maxRetries: 3,
  timeout: 5000,
} as const;

// 使用配置时类型精确且不可变
fetch(AppConfig.apiBaseUrl);
```

#### 场景三：生成精确的联合类型  
```typescript
const DIRECTIONS = ["north", "south", "east", "west"] as const;
type Direction = typeof DIRECTIONS[number]; // "north" | "south" | "east" | "west"

function move(direction: Direction) {
  // 自动补全所有可能值
  switch (direction) {
    case "north": /* ... */ break;
    case "south": /* ... */ break;
    // ...
  }
}
```

#### 场景四：枚举替代方案  
```typescript
const UserRoles = {
  Admin: "admin",
  Editor: "editor",
  Viewer: "viewer",
} as const;

type UserRole = typeof UserRoles[keyof typeof UserRoles]; // "admin" | "editor" | "viewer"
```

---

### 四、与普通类型断言的区别

| 特性                  | **`as const`**                          | **`as T`**（普通断言）              |
|-----------------------|-----------------------------------------|------------------------------------|
| **类型推导**          | 推导为最具体的字面量类型 + `readonly`   | 强制指定为某个类型                 |
| **安全性**            | 无运行时风险                            | 可能掩盖实际类型不匹配的问题       |
| **不可变性**          | 自动添加 `readonly` 修饰符              | 不影响可变性                      |
| **适用场景**          | 固定常量、配置、联合类型生成            | 处理模糊类型（如 `any`、`unknown`）|

---

### 五、注意事项

#### 1. **不可修改性**  
使用 `as const` 后，对象和数组变为只读：  
```typescript
const arr = [1, 2, 3] as const;
arr.push(4); // ❌ 错误：push 方法不存在于只读数组

const obj = { key: "value" } as const;
obj.key = "new"; // ❌ 错误：只读属性
```

#### 2. **深层次只读**  
嵌套对象的所有层级属性都会变为只读：  
```typescript
const data = {
  user: {
    name: "Alice",
    permissions: ["read", "write"],
  },
} as const;

data.user.name = "Bob"; // ❌ 错误：只读
data.user.permissions.push("delete"); // ❌ 错误：只读数组
```

#### 3. **类型收窄限制**  
无法通过 `as const` 将宽泛类型收窄为不兼容的字面量：  
```typescript
const value: string = "hello";
const fixed = value as const; // ❌ 错误：string 不能断言为字面量
```

---

### 六、与其他特性的结合

#### 1. **泛型与 `as const`**  
在泛型函数中保持类型具体性：  
```typescript
function createConstants<T extends readonly any[]>(...args: T): T {
  return args;
}

const sizes = createConstants("small", "medium", "large"); // 类型为 ["small", "medium", "large"]
```

#### 2. **类型映射与 `as const`**  
生成精确的映射类型：  
```typescript
const COLORS = {
  red: "#FF0000",
  green: "#00FF00",
} as const;

type ColorKeys = keyof typeof COLORS; // "red" | "green"
type ColorValues = typeof COLORS[ColorKeys]; // "#FF0000" | "#00FF00"
```

---

### 七、总结

#### 核心优势：
- **类型精确性**：确保值不会被推断为宽泛类型。
- **不可变性**：自动添加 `readonly` 修饰符，防止意外修改。
- **代码简洁性**：替代枚举，减少样板代码。

#### 适用场景：
- 定义常量配置、路由路径、权限角色等固定值集合。
- 生成精确的联合类型（如 Redux Action Types）。
- 需要不可变数据结构的场景（如配置文件、全局常量）。

合理使用 `as const` 可以显著提升 TypeScript 代码的类型安全性，同时保持代码简洁性和可维护性。
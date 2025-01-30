### TypeScript 中的 `enum` 详解

`enum`（枚举）是 TypeScript 中用于定义**一组命名常量**的类型，常用于*表示固定的值集合*（如状态码、方向、颜色等）。*它通过语义化的命名替代“魔法数字”或“魔法字符串”，提升代码可读性和可维护性*。

---

### 一、为什么使用枚举？

1. **代码可读性**  
   用有意义的名称替代原始值，例如 `HttpStatus.OK` 比 `200` 更易理解。  
2. **类型安全**  
   限制变量只能取枚举中的值，避免无效值错误。  
3. **集中管理常量**  
   统一维护常量集合，便于修改和扩展。  
4. **智能提示**  
   编辑器（如 VS Code）提供自动补全和类型检查。

---

### 二、枚举的核心用法

#### 1. **数字枚举（默认）**  
成员值默认从 `0` 开始自动递增，也可手动指定初始值：
```typescript
enum Direction {
  Up,      // 0
  Down,    // 1
  Left,    // 2
  Right,   // 3
}

// 手动指定值
enum HttpStatus {
  OK = 200,
  BadRequest = 400,
  NotFound = 404,
}
```

#### 2. **字符串枚举**  
每个成员必须显式初始化：
```typescript
enum LogLevel {
  Debug = "DEBUG",
  Info = "INFO",
  Error = "ERROR",
}
```

#### 3. **异构枚举（混合类型）**  
混合数字和字符串成员（**不推荐**，易混乱）：
```typescript
enum MixedEnum {
  No = 0,
  Yes = "YES",
}
```

#### 4. **常量枚举（`const enum`）**  
编译时直接内联值，减少生成代码体积：
```typescript
const enum UserRole {
  Admin = 1,
  User = 2,
}

// 编译后代码中不会生成 UserRole 对象
const role = UserRole.Admin; // 编译为 const role = 1
```

---

### 三、枚举的实际应用场景

#### 场景一：状态管理
```typescript
enum OrderStatus {
  Pending = "PENDING",
  Shipped = "SHIPPED",
  Delivered = "DELIVERED",
}

function updateOrder(status: OrderStatus) {
  // 根据状态处理逻辑
}
updateOrder(OrderStatus.Shipped); // ✅ 合法
updateOrder("CANCELLED");         // ❌ 错误：非枚举值
```

#### 场景二：API 状态码
```typescript
enum HttpStatus {
  OK = 200,
  Created = 201,
  BadRequest = 400,
  Unauthorized = 401,
  InternalServerError = 500,
}

function handleResponse(code: HttpStatus) {
  switch (code) {
    case HttpStatus.OK:
      console.log("请求成功");
      break;
    case HttpStatus.Unauthorized:
      console.log("未授权");
      break;
    // ...其他状态处理
  }
}
```

#### 场景三：权限控制
```typescript
enum UserPermission {
  Read = 1 << 0,   // 1
  Write = 1 << 1,  // 2
  Delete = 1 << 2, // 4
}

const adminPermissions = UserPermission.Read | UserPermission.Write | UserPermission.Delete;
const hasWritePermission = (adminPermissions & UserPermission.Write) !== 0; // true
```

---

### 四、枚举的编译结果

#### 1. 数字枚举的编译结果  
TypeScript 会将枚举编译为**双向映射对象（值 ↔ 键）**：
```typescript
enum Direction {
  Up,
  Down,
}

// 编译后的 JavaScript
var Direction;
(function (Direction) {
  Direction[Direction["Up"] = 0] = "Up";
  Direction[Direction["Down"] = 1] = "Down";
})(Direction || (Direction = {}));
```

#### 2. 字符串枚举的编译结果  
直接生成键值对对象：
```typescript
enum LogLevel {
  Debug = "DEBUG",
}

// 编译后的 JavaScript
var LogLevel;
(function (LogLevel) {
  LogLevel["Debug"] = "DEBUG";
})(LogLevel || (LogLevel = {}));
```

#### 3. 常量枚举的编译结果  
不会生成任何代码，直接替换为字面量：
```typescript
const enum UserRole {
  Admin = 1,
}

console.log(UserRole.Admin); // 编译为 console.log(1)
```

---

### 五、枚举 vs 其他方案

| 方案            | 优点                          | 缺点                          |
|-----------------|------------------------------|-------------------------------|
| **`enum`**      | 类型安全、语义明确、支持双向映射 | 生成额外代码（非 const enum） |
| **联合类型**    | 轻量、无运行时开销            | 无法遍历值、无语义化命名      |
| **对象常量**    | 灵活、可动态修改              | 无类型约束、需手动维护        |

---

### 六、最佳实践

1. **优先使用字符串枚举**  
   避免数字枚举的“魔法数字”问题，字符串枚举更易调试和序列化。  
   ```typescript
   enum EventType {
     Click = "CLICK",
     Hover = "HOVER",
   }
   ```

2. **避免异构枚举**  
   混合数字和字符串成员会降低代码可读性。

3. **合理使用常量枚举**  
   在性能敏感场景（如库开发）中使用 `const enum`，减少代码体积。

4. **替代方案选择**  
   - 若无需运行时枚举：使用联合类型 `type LogLevel = "DEBUG" | "INFO"`。  
   - 若需动态扩展：使用 `const对象 + as const`：
     ```typescript
     const LogLevel = {
       Debug: "DEBUG",
       Info: "INFO",
     } as const;
     type LogLevel = typeof LogLevel[keyof typeof LogLevel];
     ```

---

### 七、完整示例

```typescript
// 定义枚举
enum Theme {
  Light = "LIGHT",
  Dark = "DARK",
}

// 使用枚举
function setTheme(theme: Theme) {
  document.body.className = theme.toLowerCase();
}

setTheme(Theme.Dark);

// 遍历枚举值
for (const themeName of Object.values(Theme)) {
  console.log(themeName); // "LIGHT", "DARK"
}

// 反向映射（仅数字枚举）
enum StatusCode {
  Success = 200,
  NotFound = 404,
}
console.log(StatusCode[200]); // "Success"
```

---

通过合理使用枚举，可以显著提升代码的可读性和健壮性，尤其在处理固定值集合时，枚举是 TypeScript 中不可或缺的工具。
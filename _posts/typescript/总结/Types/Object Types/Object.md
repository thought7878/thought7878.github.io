### TypeScript 中的 `Object` 详解

TypeScript 中的 **对象（Object）** 是描述键值对集合的核心类型。通过类型注解，可以精确约束对象的结构，确保属性存在性、类型正确性及方法签名的合法性。以下是 TypeScript 对象的核心用法和高级实践。

---

### 一、对象类型的基本定义

#### 1. **内联类型注解**  
直接定义对象结构，无需提前声明类型：  
```typescript
// 明确每个属性的类型
const user: { 
  name: string; 
  age: number; 
  isAdmin?: boolean; // 可选属性
} = { 
  name: "Alice", 
  age: 30 
};
```

#### 2. **类型别名（`type`）**  
复用复杂对象类型定义：  
```typescript
type User = {
  id: number;
  name: string;
  address: {
    city: string;
    zipCode: string;
  };
};

const currentUser: User = {
  id: 1,
  name: "Bob",
  address: { city: "New York", zipCode: "10001" },
};
```

#### 3. **接口（`interface`）**  
更适合定义对象契约，支持扩展和合并：  
```typescript
interface Product {
  sku: string;
  price: number;
  getDescription(): string; // 方法签名
}

const laptop: Product = {
  sku: "P-001",
  price: 999,
  getDescription() { return `${this.sku}: $${this.price}`; },
};
```

---

### 二、对象属性的高级约束

#### 1. **可选属性（`?`）**  
允许属性不存在：  
```typescript
type Config = {
  apiUrl: string;
  timeout?: number; // 可选
};

const devConfig: Config = { apiUrl: "/api" };
const prodConfig: Config = { apiUrl: "/api", timeout: 5000 };
```

#### 2. **只读属性（`readonly`）**  
初始化后不可修改：  
```typescript
interface ImmutablePoint {
  readonly x: number;
  readonly y: number;
}

const origin: ImmutablePoint = { x: 0, y: 0 };
origin.x = 1; // ❌ 错误：只读属性
```

#### 3. **索引签名（动态属性）**  
允许对象包含任意数量的特定类型属性：  
```typescript
// 允许任意字符串键，值为 number
type NumberDictionary = {
  [key: string]: number;
  default: number; // 必须符合索引签名类型
};

const scores: NumberDictionary = {
  math: 90,
  english: 85,
  default: 0,
};
```

---

### 三、对象方法的类型控制

#### 1. **方法签名定义**  
精确约束方法的参数和返回值：  
```typescript
interface Calculator {
  add(x: number, y: number): number;
  subtract: (x: number, y: number) => number; // 箭头函数语法
}

const calc: Calculator = {
  add(x, y) { return x + y; },
  subtract: (x, y) => x - y,
};
```

#### 2. **函数类型属性**  
定义对象属性为函数类型：  
```typescript
type EventHandler = {
  onClick: (event: MouseEvent) => void;
  onKeyPress?: (event: KeyboardEvent) => void;
};

const handler: EventHandler = {
  onClick: (e) => console.log(e.clientX),
};
```

---

### 四、对象与泛型结合

#### 1. **泛型对象类型**  
创建可复用的动态结构：  
```typescript
type ApiResponse<T> = {
  data: T;
  status: number;
  error?: string;
};

const userResponse: ApiResponse<User> = {
  data: { id: 1, name: "Alice" },
  status: 200,
};
```

#### 2. **泛型工具类型**  
使用 `Partial`、`Readonly` 等工具类型快速转换：  
```typescript
interface Settings {
  theme: "light" | "dark";
  notifications: boolean;
}

// 所有属性变为可选
const partialSettings: Partial<Settings> = { theme: "light" };

// 所有属性变为只读
const lockedSettings: Readonly<Settings> = {
  theme: "dark",
  notifications: true,
};
lockedSettings.theme = "light"; // ❌ 错误：只读
```

---

### 五、对象类型操作技巧

#### 1. **类型断言（`as`）**  
临时覆盖类型推断（慎用）：  
```typescript
const unknownData = JSON.parse('{"name": "Alice"}') as { name: string };
console.log(unknownData.name.toUpperCase()); // "ALICE"
```

#### 2. **类型守卫（Type Guards）**  
运行时验证对象结构：  
```typescript
function isUser(obj: unknown): obj is User {
  return typeof obj === "object" 
    && obj !== null 
    && "id" in obj 
    && "name" in obj;
}

const data: unknown = { id: 1, name: "Alice" };
if (isUser(data)) {
  console.log(data.name); // 类型收窄为 User
}
```

#### 3. **解构与类型注解**  
保持解构后的变量类型安全：  
```typescript
const product = { sku: "P-001", price: 100, stock: 50 };

// 显式注解解构后的变量类型
const { sku, price }: { sku: string; price: number } = product;
```

---

### 六、对象与其他类型结合

#### 1. **联合类型对象**  
允许对象为多种可能的结构：  
```typescript
type Result = 
  | { status: "success"; data: string }
  | { status: "error"; message: string };

function handleResult(result: Result) {
  if (result.status === "success") {
    console.log(result.data); // 类型收窄
  } else {
    console.error(result.message);
  }
}
```

#### 2. **交叉类型对象**  
合并多个对象类型：  
```typescript
type Named = { name: string };
type Aged = { age: number };

type Person = Named & Aged;

const alice: Person = { name: "Alice", age: 30 };
```

---

### 七、常见问题与解决方案

#### 问题一：动态属性访问报错  
使用类型断言或索引签名解决 `Element implicitly has 'any' type` 错误：  
```typescript
// ❌ 错误：TS 无法推断动态属性类型
const value = obj[key];

// ✅ 方案一：索引签名定义对象类型
type SafeObject = { [key: string]: unknown };
const obj: SafeObject = { a: 1 };
const value = obj["a"]; // 类型为 unknown

// ✅ 方案二：类型断言
const value = obj[key] as number;
```

#### 问题二：嵌套对象类型复杂  
使用类型别名或接口分层定义：  
```typescript
type Address = {
  street: string;
  city: string;
};

type User = {
  id: number;
  address: Address;
};

const user: User = {
  id: 1,
  address: { street: "Main St", city: "NYC" },
};
```

---

### 八、最佳实践总结

1. **优先使用 `interface` 定义对象**  
   需要扩展或实现时更灵活（如类实现接口）。

2. **避免 `any` 和松散类型**  
   始终明确对象结构，必要时使用 `unknown` 替代 `any`。

3. **合理使用工具类型**  
   如 `Partial`、`Pick`、`Omit` 简化类型操作。

4. **类型守卫保障安全**  
   在操作未知对象时，通过类型守卫避免运行时错误。

5. **保持对象结构扁平**  
   避免过度嵌套，必要时拆分为多个类型。

---

### 完整示例：API 响应处理

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type ApiResponse<T> = {
  data: T;
  status: number;
  error?: string;
};

async function fetchUser(id: number): Promise<ApiResponse<User>> {
  const response = await fetch(`/api/users/${id}`);
  const data = await response.json();
  return { data, status: response.status };
}

// 使用
fetchUser(1).then((result) => {
  if (result.status === 200) {
    console.log(result.data.name); // 安全访问
  } else {
    console.error(result.error);
  }
});
```

---

通过严格的对象类型约束，TypeScript 能显著提升代码的健壮性和可维护性，减少因属性误用或缺失导致的运行时错误。
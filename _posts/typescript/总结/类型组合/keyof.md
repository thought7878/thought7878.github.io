### TypeScript 中的 `keyof` 详解

**`keyof`** 是 TypeScript 中的核心操作符，用于**获取对象类型（或接口）所有属性键的联合类型**。它通过静态分析对象的键集合，生成一个类型安全的联合类型，常用于动态访问对象属性、类型约束和高级类型操作。

---

#### 一、基础用法

##### 1. **基本语法**
```typescript
type Keys = keyof T; // T 是对象类型或接口
```

##### 2. **示例**
```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

type UserKeys = keyof User; 
// 等效于 "id" | "name" | "email"

const key: UserKeys = "name"; // ✅ 合法
const invalidKey: UserKeys = "age"; // ❌ 错误："age" 不存在于 User 中
```

---

#### 二、典型应用场景

##### 1. **动态安全访问对象属性**
通过泛型约束确保函数参数是合法的属性键：
```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user: User = { id: 1, name: "Alice", email: "alice@example.com" };
const name = getProperty(user, "name"); // ✅ 返回 string 类型
const invalid = getProperty(user, "age"); // ❌ 类型错误
```

##### 2. **映射类型（Mapped Types）**
结合 `keyof` 生成动态类型：
```typescript
type ReadonlyUser = {
  readonly [K in keyof User]: User[K];
};
/* 结果：
{
  readonly id: number;
  readonly name: string;
  readonly email: string;
}
*/
```

##### 3. **类型安全的配置对象**
限制配置键必须是对象已有的属性：
```typescript
type Config<T> = {
  [K in keyof T]: boolean; // 将每个属性映射为 boolean
};

const userConfig: Config<User> = {
  id: true,    // ✅ 必须包含所有 User 的键
  name: false,
  email: true,
};
```

---

#### 三、高级用法

##### 1. **联合类型键的提取**
处理复杂联合类型时，`keyof` 会生成所有可能的键的联合：
```typescript
type Shape = 
  | { kind: "circle"; radius: number }
  | { kind: "square"; side: number };

type ShapeKeys = keyof Shape; 
// 结果为 "kind"（所有分支的共有属性）
```

##### 2. **与 `typeof` 结合**
动态获取对象字面量的键类型：
```typescript
const colors = {
  red: "#FF0000",
  green: "#00FF00",
  blue: "#0000FF",
} as const; // 使用 as const 保留字面量类型

type ColorKeys = keyof typeof colors; // "red" | "green" | "blue"
```

##### 3. **索引签名（Index Signatures）**
处理带有索引签名的对象：
```typescript
interface Dictionary {
  [key: string]: number;
}

type DictKeys = keyof Dictionary; 
// 结果为 string | number（因为索引签名的键可以是 string 或 number）
```

##### 4. **泛型约束**
限制泛型参数必须是对象的键：
```typescript
function sortByKey<T, K extends keyof T>(items: T[], key: K): T[] {
  return items.sort((a, b) => a[key] > b[key] ? 1 : -1);
}

const users: User[] = [/* ... */];
sortByKey(users, "name"); // ✅ 合法
sortByKey(users, "age");  // ❌ 错误：键不存在
```

---

#### 四、特殊场景与注意事项

##### 1. **类类型中的 `keyof`**
`keyof` 会包含类的公共（public）属性和方法：
```typescript
class Animal {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  eat() { /* ... */ }
}

type AnimalKeys = keyof Animal; 
// "name" | "age" | "eat"
```

##### 2. **处理可选属性**
可选属性（`?`）的键仍会被包含在联合类型中：
```typescript
interface Product {
  id: string;
  name?: string;
}

type ProductKeys = keyof Product; // "id" | "name"
```

##### 3. **`never` 类型的处理**
若对象类型为空（如 `{}`），`keyof` 会返回 `never`：
```typescript
type EmptyKeys = keyof {}; // never
```

---

#### 五、常见问题解决

##### 问题：如何排除某些键？
使用 `Exclude` 工具类型过滤键：
```typescript
type SensitiveKeys = Exclude<keyof User, "email">; // "id" | "name"
```

##### 问题：如何获取值的类型？
结合 `T[K]` 访问属性值的类型：
```typescript
type UserNameType = User["name"]; // string
```

##### 问题：如何处理交叉类型？
`keyof` 会合并所有类型的键：
```typescript
type A = { x: number };
type B = { y: string };
type C = A & B;

type CKeys = keyof C; // "x" | "y"
```

---

#### 六、总结

- **核心作用**：将对象键转换为类型安全的联合类型。
- **典型用途**：动态属性访问、映射类型、泛型约束。
- **最佳实践**：
  - 优先用于需要严格限制键的场景（如 API 参数校验）。
  - 结合 `typeof` 动态推断对象字面量的键类型。
  - 避免与空对象或复杂联合类型混淆。

通过 `keyof`，开发者可以大幅提升代码的类型安全性，减少运行时错误！ 🛠️
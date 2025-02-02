
在 TypeScript 中，泛型（Generics）是一种创建*可复用代码*组件的工具，允许开发者编写*灵活且类型安全*的函数、类和接口。以下是泛型的核心概念及用法详解：

---

### 一、泛型的基本语法

#### 1. **泛型函数**

使用 `<T>` 声明**类型参数**，并在参数、返回值或函数体内使用：

```typescript
function identity<T>(arg: T): T {
  return arg;
}
```

- **调用方式**：
  ```typescript
  let output1 = identity<string>("Hello"); // 显式指定类型
  let output2 = identity(42); // 自动推断为 number
  ```

#### 2. **泛型接口**

定义接口时使用泛型参数，增强灵活性：

```typescript
interface KeyValuePair<K, V> {
  key: K;
  value: V;
}

const pair: KeyValuePair<string, number> = { key: "age", value: 30 };
```

#### 3. **泛型类**

类可以通过泛型支持多种数据类型：

```typescript
class Box<T> {
  private content: T;

  constructor(value: T) {
    this.content = value;
  }

  getValue(): T {
    return this.content;
  }
}

const stringBox = new Box<string>("Hello");
const numberBox = new Box<number>(42);
```

---

### 二、泛型约束

#### 1. **基本约束**

`extends`限制泛型参数必须满足特定接口或类型：

```typescript
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): void {
  console.log(arg.length);
}

logLength("text"); // ✅ 字符串有 length 属性
logLength([1, 2]); // ✅ 数组有 length 属性
logLength(42); // ❌ 错误：number 没有 length
```

#### 2. **使用 `keyof` 约束**
参考 [[_posts/typescript/总结/类型组合/keyof|keyof]]
确保访问对象属性的安全性：

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { name: "Alice", age: 30 };
const name = getProperty(user, "name"); // ✅ string
const age = getProperty(user, "age"); // ✅ number
const invalid = getProperty(user, "email"); // ❌ 错误：email 不存在
```

---

### 三、泛型应用场景

#### 1. **数据处理函数**

处理不同类型的数据集合：

```typescript
function reverseArray<T>(arr: T[]): T[] {
  return arr.reverse();
}

const numbers = reverseArray([1, 2, 3]); // number[]
const strings = reverseArray(["a", "b"]); // string[]
```

#### 2. **React 组件**

创建可复用的泛型组件：

```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return <div>{items.map(renderItem)}</div>;
}

// 使用示例
<List
  items={[
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
  ]}
  renderItem={(user) => <div key={user.id}>{user.name}</div>}
/>;
```

#### 3. **API 响应处理**

定义通用的 API 响应结构：

```typescript
interface ApiResponse<T> {
  data: T;
  code: number;
  message?: string;
}

async function fetchData<T>(url: string): Promise<ApiResponse<T>> {
  const response = await fetch(url);
  return response.json();
}

// 调用示例
const userResponse = await fetchData<User>("/api/user/1");
```

---

### 四、高级泛型技巧

#### 1. **默认泛型参数**

为泛型参数提供*默认类型*：

```typescript
function createArray<T = string>(length: number, value: T): T[] {
  return Array(length).fill(value);
}

const strArray = createArray(3, "x"); // string[]
const numArray = createArray<number>(3, 5); // number[]
```

#### 2. **条件类型（Conditional Types）**
参考 [[条件类型（Conditional Types）]]

根据条件动态确定类型：

```typescript
type IsString<T> = T extends string ? "Yes" : "No";
type A = IsString<"hello">; // "Yes"
type B = IsString<42>; // "No"
```

#### 3. **映射类型（Mapped Types）**
参考 [[映射类型（Mapped Types）]]

结合泛型生成新类型：

```typescript
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type User = { name: string; age: number };
type ReadonlyUser = Readonly<User>; // { readonly name: string; readonly age: number }
```

---

### 五、常见问题与解决方案

#### 1. **泛型与 `any` 的区别**

- **`any`**：放弃类型检查，不安全。
- **泛型**：保留类型信息，安全且灵活。

#### 2. **处理复杂类型推断**

当自动推断不准确时，显式指定类型参数：

```typescript
const mixedArray = [1, "two", 3];
// 显式指定为 (number | string)[]
const processed = reverseArray<number | string>(mixedArray);
```

#### 3. **泛型约束与类型守卫**

结合类型守卫处理泛型参数：

```typescript
function process<T extends string | number>(value: T): T {
  if (typeof value === "string") {
    return value.trim() as T;
  }
  return value.toFixed(2) as T;
}
```

---

### 六、总结

- **核心价值**：泛型提升*代码复用性*，同时保持*类型安全*。
- **适用场景**：函数、类、接口需要支持多种类型时。
- **最佳实践**：
  - 优先使用泛型替代 `any`。
  - 结合约束确保类型安全。
  - 利用高级类型（条件类型、映射类型）处理复杂逻辑。

掌握泛型，能显著提升 TypeScript 代码的灵活性和健壮性！ 🚀

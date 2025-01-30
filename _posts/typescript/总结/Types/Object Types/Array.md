### TypeScript 中的 `Array` 详解

TypeScript 的数组在 JavaScript 数组的基础上，通过**类型注解**和**泛型**提供了更严格的类型安全机制，确保数组元素的类型一致性。以下是 TypeScript 数组的核心用法和高级特性。

---

### 一、数组的基本定义

#### 1. **类型注解语法**  
使用 `类型[]` 或 `Array<类型>` 定义数组类型：
```typescript
// 数字数组
let numbers: number[] = [1, 2, 3];

// 字符串数组（泛型语法）
let names: Array<string> = ["Alice", "Bob"];

// 混合类型数组（不推荐，优先使用元组或联合类型）
let mixed: (string | number)[] = ["text", 123];
```

#### 2. **空数组初始化**  
明确类型，避免隐式 `any[]`：
```typescript
let empty1: number[] = [];      // ✅ 推荐
let empty2 = [] as number[];    // ✅ 类型断言
let empty3 = new Array<boolean>(); // ✅ 泛型构造函数
```

---

### 二、数组的常用操作

#### 1. **类型安全的增删改查**  
```typescript
// 添加元素（自动类型检查）
numbers.push(4);     // ✅ 允许
numbers.push("5");   // ❌ 错误：类型不匹配

// 修改元素
names[0] = "Carol";  // ✅ 允许
names[1] = 123;      // ❌ 错误：类型不匹配

// 删除元素
names.splice(0, 1);  // ✅ 删除第一个元素
```

#### 2. **遍历数组**  
保持元素类型推断：
```typescript
// forEach
numbers.forEach((num) => {
  console.log(num.toFixed(2)); // num 自动推断为 number
});

// map（返回新数组）
const doubled = numbers.map((num) => num * 2); // doubled 类型为 number[]
```

#### 3. **数组解构**  
支持类型注解：
```typescript
const [first, second]: number[] = numbers;
const [name, age]: [string, number] = ["Alice", 30]; // 元组解构
```

---

### 三、泛型数组的高级用法

#### 1. **自定义泛型数组**  
处理复杂数据结构：
```typescript
interface User {
  id: number;
  name: string;
}

const users: Array<User> = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
];

// 查找用户（自动推断返回类型为 User | undefined）
const user = users.find((u) => u.id === 1);
```

#### 2. **只读数组 (`ReadonlyArray`)**  
禁止修改数组内容：
```typescript
const readOnlyNumbers: ReadonlyArray<number> = [1, 2, 3];
readOnlyNumbers.push(4);      // ❌ 错误：只读属性
readOnlyNumbers[0] = 0;       // ❌ 错误：禁止修改
```

#### 3. **类型推断优化**  
利用 `as const` 断言固定类型和值：
```typescript
const routes = ["/home", "/about"] as const; // 类型为 readonly ["/home", "/about"]
const firstRoute: "/home" = routes[0];       // 精确推断字面量类型
```

---

### 四、元组（Tuple）  
处理**固定长度和类型**的数组，常见于函数多返回值、React Hooks 等场景。

#### 1. **基本定义**  
```typescript
// 定义元组类型
let person: [string, number] = ["Alice", 30];

// 错误示例
person = ["Bob", "25"];   // ❌ 第二个元素应为 number
person.push("extra");     // ⚠️ 允许但破坏元组结构（TypeScript 不检测越界操作）
```

#### 2. **可选元素（TS 4.0+）**  
```typescript
type OptionalTuple = [string, number?];
const data1: OptionalTuple = ["text"];       // ✅ 允许
const data2: OptionalTuple = ["text", 123];  // ✅ 允许
```

#### 3. **标记元组（TS 4.0+）**  
提升可读性：
```typescript
type HttpResponse = [status: number, data: string];
const response: HttpResponse = [200, "OK"];
```

---

### 五、数组类型守卫

#### 1. **类型谓词（Type Predicates）**  
自定义类型检查逻辑：
```typescript
function isNumberArray(arr: unknown[]): arr is number[] {
  return arr.every((item) => typeof item === "number");
}

const unknownArray: unknown[] = [1, 2, 3];
if (isNumberArray(unknownArray)) {
  const sum = unknownArray.reduce((a, b) => a + b, 0); // 类型收窄为 number[]
}
```

#### 2. **`Array.isArray` 检测**  
```typescript
function processInput(input: unknown) {
  if (Array.isArray(input) && input.every((item) => typeof item === "string")) {
    console.log(input.join(", ")); // 类型收窄为 string[]
  }
}
```

---

### 六、实用工具类型

#### 1. **`Partial<Array>`**  
将数组元素转为可选：
```typescript
type PartialUserArray = Partial<User>[];
const partialUsers: PartialUserArray = [{ id: 1 }, { name: "Bob" }]; // 允许缺少属性
```

#### 2. **`ReadonlyArray`**  
禁止修改数组：
```typescript
const readOnly: ReadonlyArray<string> = ["a", "b"];
readOnly[0] = "c"; // ❌ 错误：只读
```

---

### 七、最佳实践

1. **避免 `any[]`**  
   使用具体类型替代 `any[]`，确保类型安全。  
   ```typescript
   // ❌ 不推荐
   const riskyArray: any[] = [1, "text", {}];

   // ✅ 推荐：联合类型或明确结构
   const safeArray: (number | string)[] = [1, "text"];
   ```

2. **优先使用 `for..of` 遍历**  
   相比传统 `for` 循环，`for..of` 更简洁且类型安全。  
   ```typescript
   for (const num of numbers) {
     console.log(num * 2); // num 类型为 number
   }
   ```

3. **利用类型推断简化代码**  
   在初始化时省略显式类型注解，让 TypeScript 自动推断。  
   ```typescript
   const inferredNumbers = [1, 2, 3]; // 自动推断为 number[]
   ```

---

### 八、与其他类型的结合

#### 1. **数组与联合类型**  
```typescript
type StringOrNumberArray = (string | number)[];
const arr: StringOrNumberArray = ["text", 123];
```

#### 2. **数组与接口**  
```typescript
interface ApiResponse<T> {
  data: T[];
  total: number;
}

const response: ApiResponse<User> = {
  data: [{ id: 1, name: "Alice" }],
  total: 1,
};
```

---

### 总结  
- **核心优势**：通过类型约束避免无效操作，提升代码健壮性。  
- **灵活选择**：根据场景使用普通数组、元组或只读数组。  
- **高级技巧**：结合泛型、类型守卫和工具类型处理复杂逻辑。  

TypeScript 的数组系统将 JavaScript 的动态灵活与静态类型安全结合，是构建可靠应用的重要基础。
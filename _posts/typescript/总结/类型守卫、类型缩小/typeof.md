以下是 TypeScript 中 **`typeof` 操作符** 的核心用法、场景及注意事项的总结：

---

### **一、基本作用**
TypeScript 中的 `typeof` 有两种主要用途，分别作用于 **类型空间（Type Space）** 和 **值空间（Value Space）**，需严格区分上下文：

1. **类型空间**：在类型注解或类型声明中，`typeof` 用于 **获取变量或属性的类型**。  
2. **值空间**：在代码逻辑中，`typeof` 作为 JavaScript 原生操作符，返回值的 **运行时类型字符串**（如 `"string"`、`"object"`）。

---

### **二、类型空间中的 `typeof`**
#### **1. 获取变量类型**
直接引用变量类型，生成类型别名或接口：  
```typescript
const user = { name: "Alice", age: 30 };
type UserType = typeof user; 
// { name: string; age: number }

function greet() { return "Hello"; }
type GreetReturn = typeof greet; 
// () => string
```

#### **2. 结合 `keyof` 获取键集合**
动态生成对象键的联合类型：  
```typescript
const config = { width: 100, height: 200 };
type ConfigKeys = keyof typeof config; 
// "width" | "height"
```

#### **3. 获取函数/类构造类型**
引用函数或类的构造签名：  
```typescript
class User {}
type UserConstructor = typeof User; 
// new () => User

function createUser() { return new User(); }
type FactoryType = typeof createUser; 
// () => User
```

#### **4. 获取枚举类型**
结合枚举生成类型（需先通过 `typeof` 获取枚举类型本身）：  
```typescript
enum Direction { Up, Down }
type DirectionKeys = keyof typeof Direction; 
// "Up" | "Down"
```

---

### **三、值空间中的 `typeof`（JavaScript 行为）**
在代码逻辑中，`typeof` 返回值的运行时类型字符串：  
```typescript
const value = "Hello";
console.log(typeof value); // "string"

function fn() {}
console.log(typeof fn);    // "function"
```

---

### **四、高级用法**
#### **1. 类型守卫（Type Guard）**
结合 `typeof` 值检查实现类型收窄：  
```typescript
function printValue(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // value 收窄为 string
  } else {
    console.log(value.toFixed(2));    // value 收窄为 number
  }
}
```

#### **2. 动态映射类型**
结合 `keyof typeof` 遍历对象键生成类型：  
```typescript
const colors = { red: "#FF0000", green: "#00FF00" } as const;

type ColorKeys = keyof typeof colors; 
// "red" | "green"

type ColorValues = (typeof colors)[ColorKeys]; 
// "#FF0000" | "#00FF00"
```

#### **3. 模板字面量类型推断**
从 `const` 断言对象推断字面量类型：  
```typescript
const sizes = { small: "sm", large: "lg" } as const;

type SizeKeys = keyof typeof sizes; 
// "small" | "large"

type SizeValues = (typeof sizes)[SizeKeys]; 
// "sm" | "lg"
```

---

### **五、注意事项**
1. **类型与值的区分**  
   - **类型空间**的 `typeof` 仅在类型注解（如 `type`、`interface`）中有效。  
   - **值空间**的 `typeof` 仅在代码逻辑（如 `if`、表达式）中有效。

2. **不可用于类型参数**  
   `typeof` 不能直接用于泛型参数的类型推导：  
   ```typescript
   // 错误！无法推断 T 的类型
   type Example<T> = typeof T;
   ```

3. **联合类型的分发行为**  
   当 `typeof` 作用于联合类型时，可能需明确类型收窄逻辑：  
   ```typescript
   type T = string | number;
   type ValueType = T extends string ? "str" : "num"; 
   // 条件类型分发结果为 "str" | "num"
   ```

4. **`const` 断言的影响**  
   使用 `as const` 后，`typeof` 将推断出精确的字面量类型而非宽泛类型：  
   ```typescript
   const arr = [1, 2] as const;
   type ArrType = typeof arr; 
   // readonly [1, 2]（而非 number[]）
   ```

---

### **六、总结**
- **类型空间**：  
  `typeof` 是*类型推导的利器*，*用于动态生成类型别名、接口或联合类型*，提升代码复用性和类型安全性。  
- **值空间**：  
  保留 JavaScript 的运行时类型检查能力，结合类型守卫实现精确的类型收窄。  

熟练掌握 `typeof` 的双重角色，能更高效地编写类型安全的 TypeScript 代码。
以下是 TypeScript **映射类型（Mapped Types）** 的核心概念、语法及实际应用的总结：

---

### **一、映射类型的基本概念**
**映射类型**是一种通过 **遍历现有类型的属性并应用转换规则** 来生成新类型的机制。其核心是**通过 `in` 关键字遍历键的联合类型，结合类型操作符（如 `?`、`readonly`）或条件类型，动态创建新类型**。

---

### **二、基本语法**
#### **1. 基础结构**
```typescript
type NewType = {
  [Key in KeySource]: ValueType;
};
```
- **`KeySource`**：通常是 `keyof T` 或显式的联合类型（如 `"name" | "age"`）。
- **`ValueType`**：可以是原类型属性的类型（`T[Key]`）或转换后的新类型。

**示例**：复制对象类型
```typescript
type Copy<T> = { [K in keyof T]: T[K] };
type User = { name: string; age: number };
type CopiedUser = Copy<User>; // { name: string; age: number }
```

#### **2. 结合修饰符**
通过 `+` 或 `-` 控制属性修饰符（`?` 或 `readonly`）的添加或移除：
```typescript
// 添加 readonly 和可选修饰符
type ReadonlyPartial<T> = {
  +readonly [K in keyof T]+?: T[K];
};

// 移除 readonly 和可选修饰符
type MutableRequired<T> = {
  -readonly [K in keyof T]-?: T[K];
};
```

---

### **三、内置工具类型实现**
许多 TypeScript 内置工具类型基于映射类型实现：

#### **1. `Partial<T>`**
将所有属性变为可选：
```typescript
type Partial<T> = { [K in keyof T]?: T[K] };
```

#### **2. `Required<T>`**
将所有属性变为必填：
```typescript
type Required<T> = { [K in keyof T]-?: T[K] };
```

#### **3. `Readonly<T>`**
将所有属性变为只读：
```typescript
type Readonly<T> = { readonly [K in keyof T]: T[K] };
```

#### **4. `Pick<T, K>`**
选择指定键的子集：
```typescript
type Pick<T, K extends keyof T> = { [P in K]: T[P] };
```

#### **5. `Record<K, T>`**
创建键为 `K`、值为 `T` 的新对象类型：
```typescript
type Record<K extends keyof any, T> = { [P in K]: T };
```

---

### **四、高级用法**
#### **1. 键重映射（Key Remapping）**
通过 `as` 子句重命名或过滤键（TypeScript 4.1+）：
```typescript
// 添加前缀
type AddPrefix<T, Prefix extends string> = {
  [K in keyof T as `${Prefix}${K & string}`]: T[K];
};

type PrefixedUser = AddPrefix<User, "get_">; 
// { get_name: string; get_age: number }

// 过滤特定类型的键
type FilterStringKeys<T> = {
  [K in keyof T as T[K] extends string ? K : never]: T[K];
};

type UserStringKeys = FilterStringKeys<User>; // { name: string }
```

#### **2. 条件类型结合**
在映射过程中结合条件类型动态调整属性：
```typescript
// 将函数类型转换为返回 Promise 的类型
type Promisify<T> = {
  [K in keyof T]: T[K] extends (...args: any[]) => infer R 
    ? (...args: Parameters<T[K]>) => Promise<R> 
    : T[K];
};

type AsyncUser = Promisify<User & { fetch: () => number }>;
// { name: string; age: number; fetch: () => Promise<number> }
```

#### **3. 递归映射**
处理嵌套对象类型：
```typescript
type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object 
    ? DeepReadonly<T[K]> 
    : T[K];
};

type NestedData = { 
  a: number; 
  b: { c: string; d: { e: boolean } } 
};
type ReadonlyNested = DeepReadonly<NestedData>;
// { readonly a: number; readonly b: { readonly c: string; readonly d: { readonly e: boolean } } }
```

---

### **五、注意事项**
1. **联合类型的分发行为**  
   映射类型作用于联合类型时，会逐个成员处理（类似条件类型的分发行为）：
   ```typescript
   type UnionExample = { [K in "a" | "b"]: K }; // { a: "a"; b: "b" }
   ```

2. **性能开销**  
   对大型对象或深层嵌套类型使用递归映射可能导致类型检查变慢。

3. **保留可选修饰符**  
   默认情况下，映射类型会保留原属性的可选性。通过 `-?` 或 `+?` 可显式控制。

---

### **六、应用场景**
1. **动态生成类型**  
   根据输入类型自动推导新结构（如 API 响应格式转换）。
2. **类型安全的重构**  
   批量修改属性修饰符或类型，减少手动定义重复类型。
3. **模式标准化**  
   统一接口返回格式或表单数据类型。

---

### **总结**
映射类型是 TypeScript 类型编程的核心工具之一，通过遍历键集合并应用转换规则，可以实现：
- **属性修饰符的批量操作**（如全部只读、可选）。
- **动态键名生成**（如添加前缀、过滤键）。
- **复杂结构的递归处理**（如深度只读、深度可选）。

结合条件类型和类型推断（`infer`），映射类型能显著提升代码的类型安全性和可维护性。
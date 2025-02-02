
以下是 TypeScript **条件类型（Conditional Types）** 的核心概念与使用场景的总结：

---

### **一、基本语法**
条件类型的语法类似于*三元运算符*，基于 `T extends U ? X : Y` 的结构：  
- **作用**：根据类型 `T` 是否满足 `U` 的约束，选择类型 `X` 或 `Y`。  
- **本质**：*一种类型级别的条件分支逻辑*。  

**示例**：
```typescript
type IsString<T> = T extends string ? true : false;
type T1 = IsString<"hello">; // true
type T2 = IsString<42>;      // false
```

---

### **二、核心特性**
#### **1. 类型推断与 `infer` 关键字**  
在条件类型中，`infer` 用于动态推断类型，常见于提取嵌套类型（如函数参数、返回值）。  

**示例**：提取函数返回值类型  
```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type FnReturn = ReturnType<() => number>; // number
```

#### **2. 分布式条件类型（Distributive Conditional Types）**  
当条件类型作用于 **联合类型** 时，会按成员逐个分发计算，最终合并结果。  

**示例**：过滤联合类型中的特定类型  
```typescript
type Filter<T, U> = T extends U ? T : never;
type T = Filter<string | number | boolean, number | boolean>; // number | boolean
```

#### **3. 非分发模式**  
通过包裹 `T` 为元组或数组，可禁用默认的分布式行为。  

**示例**：  
```typescript
type IsUnion<T> = [T] extends [infer U] ? false : true;
type T1 = IsUnion<string | number>; // true
type T2 = IsUnion<string>;          // false
```

---

### **三、常见工具类型实现**
条件类型是 TypeScript 内置工具类型的核心实现基础：

#### **1. `Exclude<T, U>`**  
排除 `T` 中可赋值给 `U` 的类型。  
```typescript
type Exclude<T, U> = T extends U ? never : T;
type T = Exclude<"a" | "b" | 1, string>; // 1
```

#### **2. `Extract<T, U>`**  
提取 `T` 中可赋值给 `U` 的类型。  
```typescript
type Extract<T, U> = T extends U ? T : never;
type T = Extract<"a" | "b" | 1, string>; // "a" | "b"
```

#### **3. `NonNullable<T>`**  
排除 `T` 中的 `null` 和 `undefined`。  
```typescript
type NonNullable<T> = T extends null | undefined ? never : T;
type T = NonNullable<string | null>; // string
```

#### **4. `Parameters<T>`**  
获取函数的参数类型元组。  
```typescript
type Parameters<T> = T extends (...args: infer P) => any ? P : never;
type T = Parameters<(a: string, b: number) => void>; // [a: string, b: number]
```

---

### **四、实际应用场景**
#### **1. 动态类型映射**  
根据输入类型动态生成映射类型。  

**示例**：将对象属性转换为 `Promise` 类型  
```typescript
type Promisify<T> = {
  [K in keyof T]: T[K] extends (...args: any[]) => infer R 
    ? (...args: Parameters<T[K]>) => Promise<R> 
    : T[K];
};
```

#### **2. 类型守卫与收窄**  
结合 `infer` 实现复杂类型判断。  

**示例**：判断是否为数组类型  
```typescript
type IsArray<T> = T extends (infer Item)[] ? Item : never;
type ItemType = IsArray<string[]>; // string
```

#### **3. 递归类型操作**  
处理嵌套结构（如树形数据、深度 `Readonly`）。  

**示例**：深度递归 `Readonly`  
```typescript
type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object 
    ? DeepReadonly<T[K]> 
    : T[K];
};

type User = { 
  name: string; 
  address: { city: string };
};
type ReadonlyUser = DeepReadonly<User>;
// { readonly name: string; readonly address: { readonly city: string } }
```

---

### **五、注意事项**
1. **性能开销**  
   复杂条件类型（尤其是递归）可能导致类型检查变慢，需权衡可维护性与性能。  
2. **类型兼容性**  
   `extends` 的约束遵循类型兼容性规则（如结构类型系统）。  
3. **联合类型分发**  
   明确是否需要禁用分布式行为（通过包裹 `[T]`）。  

---

### **总结**  
条件类型是 TypeScript 类型系统的核心高级特性，通过条件分支、类型推断和分发机制，可以实现：  
- **动态类型生成**（如根据输入推导输出类型）。  
- **复杂类型操作**（如递归处理嵌套结构）。  
- **类型工具封装**（如内置工具类型的实现）。  

熟练掌握条件类型，能显著提升类型定义的灵活性与代码的可维护性。

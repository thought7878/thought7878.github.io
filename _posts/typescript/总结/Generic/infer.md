以下是 TypeScript 中 **`infer` 关键字** 的核心概念、用法及实际应用的总结：

---

### **一、基本概念**
`infer` 是 TypeScript 在 **条件类型（Conditional Types）** 中引入的关键字，用于 **声明一个待推断的类型变量**。**它的核心作用**是*从复杂类型中动态提取或推断某个子类型，为类型编程提供灵活的类型操作能力。*

---

### **二、核心语法**
**`infer` 只能在条件类型的 `extends` 子句中使用**，通常结合泛型约束和类型推断：
```typescript
type Example<T> = T extends infer U ? U : never;
```
- **`infer U`**：*声明一个类型变量 `U`，TypeScript 会根据 `T` 的实际结构推断 `U` 的具体类型*。

---

### **三、核心使用场景**
#### **1. 提取函数返回类型**
通过 `infer` 提取函数类型的返回值类型，实现类似 `ReturnType<T>` 的工具类型：
```typescript
type MyReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Fn = () => number;
type Result = MyReturnType<Fn>; // number
```

#### **2. 提取函数参数类型**
提取函数参数的类型元组，实现类似 `Parameters<T>`：
```typescript
type MyParameters<T> = T extends (...args: infer P) => any ? P : never;

type Fn = (a: string, b: number) => void;
type Params = MyParameters<Fn>; // [a: string, b: number]
```

#### **3. 提取数组/元组元素类型**
推断数组或元组的元素类型：
```typescript
type ElementType<T> = T extends (infer U)[] ? U : never;

type Arr = string[];
type Item = ElementType<Arr>; // string

// 推断元组首个元素类型
type First<T extends any[]> = T extends [infer F, ...any] ? F : never;
type FirstElement = First<[string, number]>; // string
```

#### **4. 提取 Promise 的解析类型**
递归解析 `Promise` 链，获取最终解析的类型（类似 `Awaited<T>`）：
```typescript
type MyAwaited<T> = T extends Promise<infer U> ? MyAwaited<U> : T;

type T1 = MyAwaited<Promise<number>>;       // number
type T2 = MyAwaited<Promise<Promise<string>>>; // string
```

#### **5. 递归类型推断**
处理嵌套结构（如链表、树）：
```typescript
type ListNode = { value: number; next: ListNode | null };

// 提取链表最后一个节点的类型
type LastNode<T> = T extends { next: infer N } 
  ? (N extends null ? T : LastNode<N>) 
  : never;

type Last = LastNode<ListNode>; // { value: number; next: null }
```

---

### **四、高级用法**
#### **1. 结合条件类型分发**
在联合类型中分发推断：
```typescript
type UnionToIntersection<U> = 
  (U extends any ? (arg: U) => void : never) extends (arg: infer I) => void 
    ? I 
    : never;

type T = UnionToIntersection<{ a: 1 } | { b: 2 }>; // { a: 1 } & { b: 2 }
```

#### **2. 模板字面量类型推断**
从字符串字面量类型中提取模式：
```typescript
type ExtractPrefix<S> = S extends `${infer Prefix}_${string}` ? Prefix : never;

type T = ExtractPrefix<"get_user">; // "get"
```

#### **3. 类型守卫与收窄**
结合类型守卫收窄复杂类型：
```typescript
type IsArray<T> = T extends (infer U)[] ? U : never;

type Item = IsArray<string[]>; // string
```

---

### **五、注意事项**
1. **`infer` 的位置限制**  
   - `infer` 只能在条件类型的 `extends` 子句中使用。
   - 同一类型变量（如 `infer U`）只能在单个条件分支中出现一次。

2. **联合类型的分发行为**  
   条件类型默认分发联合类型，可能导致 `infer` 的推断结果不符合预期：
   ```typescript
   type Example<T> = T extends { a: infer U } ? U : never;
   type T = Example<{ a: 1 } | { a: 2 }>; // 1 | 2
   ```

3. **避免过度复杂**  
   复杂的 `infer` 嵌套或递归可能显著降低类型检查性能。

4. **无法推断任意位置**  
   `infer` 只能推断特定位置（如函数参数、返回值、数组元素等），无法直接推断任意中间类型。

---

### **六、总结**
`infer` 关键字是 TypeScript 类型编程的“瑞士军刀”，通过动态推断类型变量，实现了以下能力：
- **解构复杂类型**：从函数、数组、Promise 等类型中提取子类型。
- **递归类型操作**：处理嵌套结构（如树、链表、深度 `Readonly`）。
- **类型模式匹配**：结合模板字面量、条件分发等实现高级类型逻辑。

熟练掌握 `infer` 可以显著提升类型系统的表达能力，但需注意合理控制复杂度，确保代码可维护性。
### DS的思考
好的，我现在需要总结TypeScript中的工具类型。首先，我得回忆一下TypeScript提供了哪些内置的工具类型，比如Partial、Required、Readonly这些。可能还有一些其他的，比如Pick、Omit、Record，Exclude、Extract，还有ReturnType、Parameters等等。不过用户可能想要一个比较全面的列表，并且每个工具类型的用途和用法。

接下来，我得逐个检查这些工具类型，确保它们的名称和功能正确。比如*Partial的作用是将所有属性变为可选，而Required则是相反，让所有属性变为必选*。Readonly是让所有属性只读。然后Pick是从类型中选择某些属性，Omit是排除某些属性。Record用于创建具有指定键类型和值类型的对象类型。

然后，还有一些用于联合类型的工具类型，比如Exclude和Extract。Exclude是从联合类型中排除某些类型，Extract是保留某些类型。NonNullable用来排除null和undefined。ReturnType获取函数返回值的类型，Parameters获取函数参数类型的元组。

还有比较新的，比如Awaited，用于获取Promise的返回值类型。可能还有OmitThisParameter，ThisParameterType这些处理函数this的类型。还有ConstructorParameters和InstanceType用于类构造函数和实例类型。

用户可能还需要知道这些工具类型的使用场景和例子。比如，当有一个接口，想要创建一个所有属性可选的新接口时，用Partial。或者从一个类型中排除某些属性生成新类型，用Omit。

不过，要注意不要遗漏或者混淆工具类型。例如，区分Pick和Omit，前者是保留某些键，后者是排除某些键。Exclude和Extract都是操作联合类型，但作用相反。ReturnType和Parameters处理函数类型的不同部分。

另外，可能需要提到这些工具类型的实现原理，比如使用映射类型、条件类型、infer关键字等。例如，Partial的实现大致是遍历所有属性，给每个属性加上问号。ReturnType则是通过infer关键字推断返回类型。

有没有可能遗漏了一些工具类型？比如Uppercase、Lowercase这些字符串操作类型，或者模板字面量类型相关的工具类型。不过这些可能属于其他类别，不是传统意义上的工具类型，但用户可能也会想知道。不过根据常见分类，这些可能不算工具类型，而是属于内置的字符串处理类型。

总结起来，我需要按照类别列出这些工具类型，给出每个的简短描述、语法和一个例子。确保覆盖所有主要的工具类型，并正确分类。可能需要分成几个部分，比如属性修饰、结构操作、联合类型操作、函数操作、类操作、Promise操作等。

检查TypeScript官方文档中的工具类型列表，确保没有遗漏。比如在lib.es5.d.ts中定义的工具类型。比如，最新的TypeScript版本中可能新增了某些工具类型，比如Awaited是在4.5版本引入的。需要确认这些是否要包含在内。

最后，组织内容，使其清晰易读，每个工具类型用名称、作用、语法和示例来展示。确保术语正确，例子简洁明了。可能还需要注意中文翻译的准确性，比如工具类型的名称通常不翻译，保持英文，但描述用中文。

---

TypeScript 提供了多种**工具类型（Utility Types）**，用于在类型系统中进行常见操作。以下是常见工具类型的分类总结：

---

### **一、属性修饰工具**
1. **`Partial<T>`**  
   - **作用**：将 `T` 的所有属性变为可选。  
   - **语法**：`type Partial<T> = { [P in keyof T]?: T[P] }`  
   - **示例**：
     ```typescript
     interface User { name: string; age: number; }
     type PartialUser = Partial<User>; // { name?: string; age?: number; }
     ```

2. **`Required<T>`**  
   - **作用**：将 `T` 的所有属性变为必选。  
   - **语法**：`type Required<T> = { [P in keyof T]-?: T[P] }`  
   - **示例**：
     ```typescript
     type PartialUser = { name?: string; age?: number; };
     type RequiredUser = Required<PartialUser>; // { name: string; age: number; }
     ```

3. **`Readonly<T>`**  
   - **作用**：将 `T` 的所有属性设为只读。  
   - **语法**：`type Readonly<T> = { readonly [P in keyof T]: T[P] }`  
   - **示例**：
     ```typescript
     type ReadonlyUser = Readonly<User>; // { readonly name: string; readonly age: number; }
     ```

---

### **二、结构操作工具**
4. **`Pick<T, K>`**  
   - **作用**：从 `T` 中选择 `K` 指定的属性。  
   - **语法**：`type Pick<T, K extends keyof T> = { [P in K]: T[P] }`  
   - **示例**：
     ```typescript
     type UserName = Pick<User, 'name'>; // { name: string; }
     ```

5. **`Omit<T, K>`**  
   - **作用**：从 `T` 中排除 `K` 指定的属性。  
   - **语法**：`type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>`  
   - **示例**：
     ```typescript
     type UserWithoutAge = Omit<User, 'age'>; // { name: string; }
     ```

6. **`Record<K, T>`**  
   - **作用**：创建一个对象类型，键为 `K`，值为 `T`。  
   - **语法**：`type Record<K extends keyof any, T> = { [P in K]: T }`  
   - **示例**：
     ```typescript
     type UserMap = Record<string, User>; // { [key: string]: User }
     ```

---

### **三、联合类型操作**
7. **`Exclude<T, U>`**  
   - **作用**：从 `T` 中排除可以赋值给 `U` 的类型。  
   - **语法**：`type Exclude<T, U> = T extends U ? never : T`  
   - **示例**：
     ```typescript
     type T = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
     ```

8. **`Extract<T, U>`**  
   - **作用**：从 `T` 中提取可以赋值给 `U` 的类型。  
   - **语法**：`type Extract<T, U> = T extends U ? T : never`  
   - **示例**：
     ```typescript
     type T = Extract<"a" | "b" | 1, string>; // "a" | "b"
     ```

9. **`NonNullable<T>`**  
   - **作用**：从 `T` 中排除 `null` 和 `undefined`。  
   - **语法**：`type NonNullable<T> = T extends null | undefined ? never : T`  
   - **示例**：
     ```typescript
     type T = NonNullable<string | null>; // string
     ```

---

### **四、函数操作工具**
10. **`ReturnType<T>`**  
    - **作用**：获取函数 `T` 的返回值类型。  
    - **语法**：`type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any`  
    - **示例**：
      ```typescript
      type FnReturn = ReturnType<() => string>; // string
      ```

11. **`Parameters<T>`**  
    - **作用**：获取函数 `T` 的参数类型元组。  
    - **语法**：`type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never`  
    - **示例**：
      ```typescript
      type FnParams = Parameters<(a: string, b: number) => void>; // [a: string, b: number]
      ```

---

### **五、类与构造函数工具**
12. **`ConstructorParameters<T>`**  
    - **作用**：获取构造函数 `T` 的参数类型元组。  
    - **语法**：`type ConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer P) => any ? P : never`  
    - **示例**：
      ```typescript
      class User { constructor(name: string, age: number) {} }
      type Params = ConstructorParameters<typeof User>; // [name: string, age: number]
      ```

13. **`InstanceType<T>`**  
    - **作用**：获取构造函数 `T` 的实例类型。  
    - **语法**：`type InstanceType<T extends new (...args: any) => any> = T extends new (...args: any) => infer R ? R : any`  
    - **示例**：
      ```typescript
      type UserInstance = InstanceType<typeof User>; // User
      ```

---

### **六、异步与Promise工具**
14. **`Awaited<T>`**  
    - **作用**：递归展开 `Promise`，获取最终解析的类型。  
    - **语法**：`type Awaited<T> = T extends PromiseLike<infer U> ? Awaited<U> : T`  
    - **示例**：
      ```typescript
      type T = Awaited<Promise<Promise<string>>>; // string
      ```

---

### **七、其他工具**
15. **`ThisParameterType<T>`**  
    - **作用**：提取函数 `T` 的 `this` 参数类型。  
    - **示例**：
      ```typescript
      function fn(this: { x: number }) {}
      type ThisType = ThisParameterType<typeof fn>; // { x: number }
      ```

16. **`OmitThisParameter<T>`**  
    - **作用**：移除函数 `T` 的 `this` 参数类型。  
    - **示例**：
      ```typescript
      type NewFn = OmitThisParameter<typeof fn>; // () => void
      ```

---

### **总结**
这些工具类型通过组合映射类型、条件类型和 `infer` 关键字实现，极大提升了类型编程的灵活性。掌握它们可以显著简化复杂类型的定义，提升代码的可维护性。


### 相关资料
- [TypeScript - Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
- 
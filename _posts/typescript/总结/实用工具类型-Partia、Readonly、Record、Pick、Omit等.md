TypeScript 提供了一系列泛型实用工具类型，这些类型可以帮助你更高效地编写类型安全的代码。下面是一些常用的泛型实用工具类型的总结：

### 1. `Partial<T>`
- **描述**：将对象类型 `T` 的所有属性变为可选。
- **示例**：
  ```typescript
  interface Todo {
    title: string;
    description: string;
    completed: boolean;
  }

  type TodoPreview = Partial<Todo>;

  const todo: TodoPreview = {
    title: "Clean room",
    description: undefined,
    completed: undefined,
  };
  ```

### 2. `Readonly<T>`
- **描述**：将对象类型 `T` 的所有属性变为只读。
- **示例**：
  ```typescript
  interface Point {
    x: number;
    y: number;
  }

  const p1: Readonly<Point> = { x: 1, y: 2 };
  p1.x = 2; // Error: Cannot assign to 'x' because it is a read-only property.
  ```

### 3. `Record<K, T>`
- **描述**：创建一个新类型，该类型具有由 `K` 指定的键和由 `T` 指定的值类型。
- **示例**：
  ```typescript
  type NameAgeMap = Record<string, number>;

  const names: NameAgeMap = {
    Alice: 21,
    Bob: 22,
  };
  ```

### 4. `Pick<T, K>`
- **描述**：从类型 `T` 中选择一组属性 `K`，并创建一个新的类型。
- **示例**：
  ```typescript
  interface Todo {
    title: string;
    description: string;
    completed: boolean;
  }

  type TodoTitle = Pick<Todo, "title">;

  const title: TodoTitle = { title: "Clean room" };
  ```

### 5. `Omit<T, K>`
- **描述**：从类型 `T` 中排除一组属性 `K`，并创建一个新的类型。
- **示例**：
  ```typescript
  interface Todo {
    title: string;
    description: string;
    completed: boolean;
  }

  type TodoNoDesc = Omit<Todo, "description">;

  const todo: TodoNoDesc = {
    title: "Clean room",
    completed: false,
  };
  ```

### 6. `Exclude<T, U>`
- **描述**：从类型 `T` 中排除可以赋值给类型 `U` 的类型。
- **示例**：
  ```typescript
  type T0 = Exclude<"a" | "b" | "c", "a">; // "b" | "c"
  ```

### 7. `Extract<T, U>`
- **描述**：从类型 `T` 中提取可以赋值给类型 `U` 的类型。
- **示例**：
  ```typescript
  type T0 = Extract<"a" | "b" | "c", "a" | "f">; // "a"
  ```

### 8. `NonNullable<T>`
- **描述**：从类型 `T` 中排除 `null` 和 `undefined`。
- **示例**：
  ```typescript
  type T0 = NonNullable<string | number | undefined>; // string | number
  ```

### 9. `Parameters<T>`
- **描述**：获取函数类型 `T` 的参数类型。
- **示例**：
  ```typescript
  function foo(x: { a: number; b: string }): void {}

  type T0 = Parameters<typeof foo>; // [{ a: number; b: string }]
  ```

### 10. `ReturnType<T>`
- **描述**：获取函数类型 `T` 的返回类型。
- **示例**：
  ```typescript
  function bar(s: string): number {
    return s.length;
  }

  type T0 = ReturnType<typeof bar>; // number
  ```

### 11. `InstanceType<T>`
- **描述**：获取构造函数类型 `T` 的实例类型。
- **示例**：
  ```typescript
  class C {
    x = 0;
  }

  function fn(t: InstanceType<typeof C>) {
    t.x = 1;
  }

  const c = new C();
  fn(c);
  ```

### 12. `ThisType<T>`
- **描述**：在类型断言中指定 `this` 类型。
- **示例**：
  ```typescript
  type Foo = {
    bar(): void;
  };

  declare function doSomethingWithThis<T>(this: ThisType<T>, arg: T): void;

  doSomethingWithThis<Foo>({ bar() {} }, { bar() {} });
  ```

### 总结
这些泛型实用工具类型可以帮助你更轻松地创建复杂的类型定义，并保持类型安全。它们简化了常见的类型操作，如将对象属性变为可选或只读、从类型中提取或排除特定类型等。这些工具类型在编写复杂的类型定义时非常有用，特别是当你需要处理复杂的类型转换或组合时。
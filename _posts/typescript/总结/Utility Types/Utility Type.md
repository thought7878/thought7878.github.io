在 TypeScript 里，Utility Type（工具类型）是一些非常实用的类型工具，它们基于现有的类型创建新的类型，能帮助开发者更高效地处理类型转换和操作，提升代码的可维护性和复用性。下面为你介绍几种常见的工具类型。

### 1. `Partial<T>`

- **作用**：将类型 `T` 的所有属性变为可选属性。也就是说，对于类型 `T` 中的每个属性，都添加了 `?` 标记。
- **示例**：

```typescript
interface User {
  name: string;
  age: number;
  email: string;
}

// 使用 Partial<User> 创建一个新类型，所有属性都变为可选
type PartialUser = Partial<User>;

const partialUser: PartialUser = {
  name: "John",
};
```

在这个例子中，`PartialUser` 类型的对象可以只包含 `User` 接口中的部分属性。

### 2. `Required<T>`

- **作用**：与 `Partial<T>` 相反，`Required<T>` 将类型 `T` 的所有可选属性变为必需属性。
- **示例**：

```typescript
interface Options {
  color?: string;
  size?: number;
}

// 使用 Required<Options> 创建一个新类型，所有属性都变为必需
type RequiredOptions = Required<Options>;

const requiredOptions: RequiredOptions = {
  color: "red",
  size: 10,
};
```

这里的 `RequiredOptions` 类型的对象必须包含 `Options` 接口中的所有属性。

### 3. `Readonly<T>`

- **作用**：将类型 `T` 的所有属性变为只读属性，一旦对象被创建，这些属性的值就不能被修改。
- **示例**：

```typescript
interface Point {
  x: number;
  y: number;
}

// 使用 Readonly<Point> 创建一个新类型，所有属性都变为只读
type ReadonlyPoint = Readonly<Point>;

const readonlyPoint: ReadonlyPoint = {
  x: 10,
  y: 20,
};

// 下面这行代码会报错，因为 x 是只读属性
// readonlyPoint.x = 30;
```

### 4. `Pick<T, K>`

- **作用**：从类型 `T` 中挑选出一组属性 `K`（`K` 是一个字符串字面量类型的联合），创建一个新的类型。
- **示例**：

```typescript
interface Person {
  name: string;
  age: number;
  address: string;
}

// 从 Person 类型中挑选出 name 和 age 属性，创建一个新类型
type PersonInfo = Pick<Person, "name" | "age">;

const personInfo: PersonInfo = {
  name: "Alice",
  age: 25,
};
```

### 5. `Omit<T, K>`

- **作用**：从类型 `T` 中移除一组属性 `K`（`K` 是一个字符串字面量类型的联合），创建一个新的类型。
- **示例**：

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  description: string;
}

// 从 Product 类型中移除 description 属性，创建一个新类型
type ProductWithoutDesc = Omit<Product, "description">;

const product: ProductWithoutDesc = {
  id: 1,
  name: "Phone",
  price: 999,
};
```

### 6. `Exclude<T, U>`

- **作用**：从类型 `T` 中排除可以赋值给类型 `U` 的类型，返回一个新的类型。
- **示例**：

```typescript
type AllColors = "red" | "green" | "blue" | "yellow";
type PrimaryColors = "red" | "blue" | "yellow";

// 从 AllColors 中排除 PrimaryColors 中的类型，得到新类型
type SecondaryColors = Exclude<AllColors, PrimaryColors>;
// SecondaryColors 的类型为 'green'
```

### 7. `Extract<T, U>`

- **作用**：从类型 `T` 中提取可以赋值给类型 `U` 的类型，返回一个新的类型。
- **示例**：

```typescript
type AllFruits = "apple" | "banana" | "cherry" | "date";
type TropicalFruits = "banana" | "date";

// 从 AllFruits 中提取 TropicalFruits 中的类型，得到新类型
type ExtractedTropicalFruits = Extract<AllFruits, TropicalFruits>;
// ExtractedTropicalFruits 的类型为 'banana' | 'date'
```

这些工具类型在 TypeScript 开发中非常实用，能够帮助开发者更灵活地处理类型，减少重复的类型定义，提高开发效率。

### 8.Record

在 TypeScript 里，`Record` 是一个很有用的工具类型，它可以帮助开发者**\*快速**定义一个具有特定属性键和属性值类型的**对象类型\***。

#### 基本语法

`Record` 是一个泛型工具类型，其语法形式为 `Record<K, T>`，其中：

- `K`：表示对象属性键的类型，通常是*字符串字面量类型、联合类型、枚举类型*等，它定义了对象可以拥有的所有属性名。
- `T`：表示对象属性值的类型，也就是对象中每个属性对应的值的类型。

#### 示例及用法

##### 简单示例

```typescript
// 定义一个 Record 类型，属性键为 'a'、'b'、'c'，属性值为 number 类型
type MyRecord = Record<"a" | "b" | "c", number>;

// 创建一个符合 MyRecord 类型的对象
const myObject: MyRecord = {
  a: 1,
  b: 2,
  c: 3,
};
```

在上述代码中，`MyRecord` 类型使用 `Record` 工具类型定义，它要求对象必须包含 `'a'`、`'b'`、`'c'` 这三个属性，并且每个属性的值都必须是 `number` 类型。

##### 结合枚举类型使用

```typescript
// 定义一个枚举类型
enum Fruit {
  Apple = "apple",
  Banana = "banana",
  Orange = "orange",
}

// 定义一个 Record 类型，属性键为 Fruit 枚举类型的值，属性值为 string 类型
type FruitDescriptions = Record<Fruit, string>;

// 创建一个符合 FruitDescriptions 类型的对象
const descriptions: FruitDescriptions = {
  [Fruit.Apple]: "A red or green fruit",
  [Fruit.Banana]: "A yellow curved fruit",
  [Fruit.Orange]: "A round citrus fruit",
};
```

这里通过 `Record` 结合枚举类型 `Fruit`，定义了一个对象类型 `FruitDescriptions`，该对象的属性键是 `Fruit` 枚举中的值，属性值是字符串类型，用于描述每种水果。

##### 在函数中使用

```typescript
function createRecord<T extends string | number | symbol, U>(
  keys: T[],
  value: U
): Record<T, U> {
  const result = {} as Record<T, U>;
  keys.forEach((key) => {
    result[key] = value;
  });
  return result;
}

const keys = ["x", "y", "z"];
const record = createRecord(keys, 10);
// record 的类型为 Record<'x' | 'y' | 'z', number>
```

在这个函数 `createRecord` 中，利用 `Record` 工具类型创建了一个对象，该对象的属性键来自传入的 `keys` 数组，属性值都为传入的 `value`。

#### 作用和优势

- **提高代码的可读性和可维护性**：使用 `Record` 可以清晰地定义对象的结构，让代码的意图更加明确。例如，在处理配置对象或者映射关系时，使用 `Record` 能使代码更易于理解和维护。
- **增强类型安全性**：`Record` 可以对对象的属性键和属性值进行严格的类型检查，在编译阶段就能发现类型不匹配的问题，减少运行时错误。

### 9.Awaited

`Awaited` 是 TypeScript 4.5 引入的一个内置工具类型，主要用于处理异步操作和 Promise 类型，它可以帮助开发者更方便地获取 Promise 解析后的值的类型。以下为你详细介绍 `Awaited` 的相关内容。

#### 基本语法和作用

`Awaited` 是一个泛型工具类型，其语法为 `Awaited<T>`，其中 `T` 是一个类型参数。它的作用是递归地展开 Promise 类型，返回 Promise 最终解析的值的类型。如果 `T` 不是 Promise 类型，`Awaited<T>` 就返回 `T` 本身。

#### 示例及用法

##### 简单的 Promise 类型处理

```typescript
// 定义一个返回 Promise<number> 的函数
async function getNumber(): Promise<number> {
  return 42;
}

// 使用 Awaited 提取 Promise 解析后的值的类型
type NumberType = Awaited<ReturnType<typeof getNumber>>;

// NumberType 的类型为 number
const num: NumberType = 10;
```

在上述代码中，`ReturnType<typeof getNumber>` 的结果是 `Promise<number>`，然后使用 `Awaited` 工具类型对其进行处理，最终得到的 `NumberType` 就是 `number` 类型。

##### 嵌套 Promise 类型处理

```typescript
// 定义一个返回嵌套 Promise 的函数
async function getNestedPromise(): Promise<Promise<string>> {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(
        new Promise((innerResolve) => {
          innerResolve("Hello, TypeScript!");
        })
      );
    }, 1000);
  });
}

// 使用 Awaited 提取最终解析的值的类型
type StringType = Awaited<ReturnType<typeof getNestedPromise>>;

// StringType 的类型为 string
const str: StringType = "Test";
```

这里 `getNestedPromise` 函数返回的是一个嵌套的 Promise（`Promise<Promise<string>>`），`Awaited` 会递归地展开这个嵌套的 Promise，最终得到的 `StringType` 就是 `string` 类型。

##### 非 Promise 类型处理

```typescript
// 定义一个普通的类型
type NonPromiseType = number;

// 使用 Awaited 处理非 Promise 类型
type ResultType = Awaited<NonPromiseType>;

// ResultType 的类型为 number，和 NonPromiseType 相同
const result: ResultType = 20;
```

当传入 `Awaited` 的类型不是 Promise 类型时，它会直接返回该类型本身。

#### 实际应用场景

- **异步函数返回值类型提取**：在处理复杂的异步逻辑时，尤其是涉及多层嵌套的 Promise 时，使用 `Awaited` 可以准确地获取最终结果的类型，避免手动逐层展开 Promise 类型带来的麻烦。
- **类型安全的异步数据处理**：在编写处理异步数据的代码时，通过 `Awaited` 可以确保在使用异步操作的结果时，类型是准确无误的，提高代码的类型安全性。

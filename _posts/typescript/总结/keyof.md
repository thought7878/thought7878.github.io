在 TypeScript 中，`keyof` 是一个类型运算符，用于从一个类型中提取出所有键（属性名）的联合类型。它可以帮助你获取一个对象类型的所有公共属性名。

### 语法
```typescript
keyof T
```

### 用途
1. **获取对象类型的键**：
   - 使用 `keyof` 可以获取一个对象类型的键的联合类型。
   - 这些键可以用来索引对象类型。

2. **限制函数参数**：
   - 你可以使用 `keyof` 来限制函数参数只能是某个类型中的一个键。

3. **创建新的类型**：
   - 结合其他类型运算符（如 `typeof`、`Pick`、`Record` 等）创建新的类型。

### 示例
假设我们有一个对象类型 `Person`：

```typescript
interface Person {
  name: string;
  age: number;
  location: string;
}
```

### 1. 获取对象类型的键
```typescript
type PersonKeys = keyof Person; // "name" | "age" | "location"
```

### 2. 限制函数参数
```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person: Person = { name: "Alice", age: 30, location: "New York" };
const name = getProperty(person, "name"); // 正确
const age = getProperty(person, "age"); // 正确
const location = getProperty(person, "location"); // 正确
const unknownProperty = getProperty(person, "unknown"); // 错误："unknown" 不是 Person 的键
```

### 3. 创建新的类型
```typescript
type StringKeysOfPerson = Extract<keyof Person, string>;
// "name" | "location"

type NumberKeysOfPerson = Extract<keyof Person, number>;
// "age"

type PickNameAndLocation = Pick<Person, "name" | "location">;
// { name: string; location: string; }

type RecordOfNames = Record<keyof Person, string>;
// { name: string; age: string; location: string; }
```

### 总结
`keyof` 是一个非常有用的类型运算符，它允许你从一个类型中提取出所有键的联合类型。这在编写类型安全的代码时特别有用，尤其是当你需要确保某个操作只针对对象的特定属性时。通过结合其他类型运算符，你可以创建更复杂的类型，以满足不同的需求。
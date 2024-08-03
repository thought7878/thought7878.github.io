在 TypeScript 中，`typeof` 是一个类型查询运算符，用于**获取变量或表达式的类型**。它类似于 JavaScript 中的 `typeof` 运算符，但 TypeScript 中的 `typeof` 用于类型推断而不是运行时类型检查。

### 用途
1. **获取变量的类型**：
   - 可以获取变量的类型，并将其用作类型注解或泛型约束的一部分。

2. **获取表达式的类型**：
   - 可以获取表达式的类型，这对于处理复杂的类型表达式非常有用。

3. **创建类型别名**：
   - 可以使用 `typeof` 创建类型别名，以方便地复用和维护类型。

### 语法
```typescript
typeof expression
```

### 示例
假设我们有一个变量 `person`：

```typescript
const person = { name: "Alice", age: 30, location: "New York" };
```

### 1. 获取变量的类型
```typescript
const person = { name: "Alice", age: 30, location: "New York" };

function logProperties(obj: typeof person) {
  for (const prop in obj) {
    if (obj.hasOwnProperty(prop)) {
      console.log(`${prop}: ${obj[prop]}`);
    }
  }
}

logProperties(person);
```

### 2. 获取表达式的类型
```typescript
function getPerson(): { name: string; age: number; location: string } {
  return { name: "Alice", age: 30, location: "New York" };
}

function logProperties(obj: typeof getPerson()): void {
  for (const prop in obj) {
    if (obj.hasOwnProperty(prop)) {
      console.log(`${prop}: ${obj[prop]}`);
    }
  }
}

logProperties(getPerson());
```

### 3. 创建类型别名
```typescript
const person = { name: "Alice", age: 30, location: "New York" };

type PersonType = typeof person;

function logProperties(obj: PersonType): void {
  for (const prop in obj) {
    if (obj.hasOwnProperty(prop)) {
      console.log(`${prop}: ${obj[prop]}`);
    }
  }
}

logProperties(person);
```

### 总结
`typeof` 在 TypeScript 中是一个非常有用的类型查询运算符，它可以帮助你获取变量或表达式的类型。这在创建类型别名、类型约束或泛型函数时非常有用，可以确保你的代码类型安全且易于维护。通过使用 `typeof`，你可以确保在处理复杂类型时保持代码的清晰性和可读性。
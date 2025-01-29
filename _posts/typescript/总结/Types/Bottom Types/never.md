在 TypeScript 里，`never` 类型是一种特殊且重要的类型，它有着独特的用途和含义。

### 1. 基本概念

`never` 类型表示*那些永远不会出现的值*的类型。换句话说，`never` *类型的值永远不会被返回，也不会被赋值*。它通常用于表示那些总是抛出异常或者永远不会结束执行的函数的返回值类型，以及*类型守卫中永远不会成立的分支*。

### 2. 应用场景

#### 永远不会返回的函数

当一个函数总是抛出异常，或者进入一个无限循环而永远不会正常返回时，其返回值类型可以指定为 `never`。

```typescript
// 总是抛出异常的函数
function throwError(message: string): never {
  throw new Error(message);
}

// 进入无限循环的函数
function infiniteLoop(): never {
  while (true) {
    // 循环体
  }
}
```

在上述例子中，`throwError` 函数总是抛出一个错误，不会有正常的返回值；`infiniteLoop` 函数进入一个无限循环，也不会返回任何值，所以它们的返回值类型都可以定义为 `never`。

#### 类型守卫中的不可能情况

在使用类型守卫进行类型窄化时，如果出现了*永远不会成立的分支*，该分支的类型可以被推断为 `never`。

```typescript
type Shape =
  | { kind: "circle"; radius: number }
  | { kind: "square"; sideLength: number };

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      // 这里的 shape 类型会被推断为 never
      const _exhaustiveCheck: never = shape;
      return _exhaustiveCheck;
  }
}
```

在这个例子中，`Shape` 类型定义了两种可能的形状：圆形和正方形。在 `getArea` 函数的 `switch` 语句中，已经处理了所有可能的 `kind` 值。如果 `Shape` 类型将来被扩展，添加了新的形状，而 `switch` 语句没有相应的处理分支，那么 `default` 分支中的 `shape` 类型就会被推断为 `never`。通过定义 `_exhaustiveCheck` 变量并将其类型设置为 `never`，可以确保代码的完整性，如果有未处理的情况，编译器会报错。

### 3. `never` 类型的特性

#### 作为子类型

`never` 类型是*所有类型的子类型*，这意味着 `never` 类型的值*可以赋值给任何其他类型的变量*。

```typescript
let neverValue: never;
let num: number = neverValue;
let str: string = neverValue;
```

#### 没有子类型

除了它自身之外，没有其他类型是 `never` 类型的子类型，也就是说，不能将其他类型的值赋值给 `never` 类型的变量。

### 4. 与 `void` 类型的区别

`void` 类型表示*没有任何返回值*，通常用于函数没有显式返回值的情况。而 `never` 类型表示永远不会返回，与 `void` 不同，`never` 强调的是执行流永远不会到达返回值的那一步。

```typescript
// 返回值类型为 void 的函数
function printMessage(message: string): void {
  console.log(message);
}

// 返回值类型为 never 的函数
function alwaysThrow(): never {
  throw new Error("Something went wrong");
}
```

综上所述，`never` 类型在 TypeScript 中用于精确描述那些永远不会发生的情况，它有助于增强代码的类型安全性和完整性检查。

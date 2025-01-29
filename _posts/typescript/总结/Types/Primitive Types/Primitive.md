在 TypeScript 里，**原始类型是构建复杂数据结构的基础**，它们代表了最基本的数据类型。TypeScript 支持多种原始类型，下面为你详细介绍。

### 1. `number` 类型

- **描述**：用于表示*整数和浮点数*，和 JavaScript 一样，TypeScript 里的 `number` 类型使用 IEEE 754 双精度 64 位浮点数来存储数值。
- **示例**：

```typescript
let integer: number = 10;
let decimal: number = 3.14;
let hex: number = 0xf00d; // 十六进制
let binary: number = 0b1010; // 二进制
let octal: number = 0o744; // 八进制

console.log(integer);
console.log(decimal);
console.log(hex);
console.log(binary);
console.log(octal);
```

### 2. `string` 类型

- **描述**：用于表示文本数据，你可以使用*单引号*（`'`）、*双引号*（`"`）或者*模板字符串*（使用反引号 `` ` ``）来定义字符串。
- **示例**：

```typescript
let singleQuoted: string = "Hello";
let doubleQuoted: string = "World";
let templateString: string = `Welcome to ${singleQuoted} ${doubleQuoted}`;

console.log(singleQuoted);
console.log(doubleQuoted);
console.log(templateString);
```

### 3. `boolean` 类型

- **描述**：只有两个可能的值，即 `true` 和 `false`，用于表示*逻辑上的真和假*。
- **示例**：

```typescript
let isDone: boolean = false;
let hasError: boolean = true;

console.log(isDone);
console.log(hasError);
```

### 4. `null` 和 `undefined` 类型

- **描述**：`null` 表示一个有意的空值，而 `undefined` 通常表示*变量已声明但未赋值*，或者*函数没有返回值*。在 TypeScript 里，`null` 和 `undefined` 是所有其他类型的子类型，不过当开启 `strictNullChecks` 选项时，它们只能赋值给自身或者 `any` 类型。
- **示例**：

```typescript
let nullValue: null = null;
let undefinedValue: undefined = undefined;

let num: number | null | undefined;
num = 10;
num = null;
num = undefined;

console.log(nullValue);
console.log(undefinedValue);
```

### 5. `bigint` 类型

- **描述**：在 TypeScript 3.2 及以后版本中引入，用于表示任意大的整数。可以通过在整数字面量后面加 `n` 来创建 `bigint` 类型的值。
- **示例**：

```typescript
let big: bigint = 100n;
let anotherBig: bigint = BigInt(9007199254740991);

console.log(big);
console.log(anotherBig);
```

### 6. `symbol` 类型

- **描述**：`symbol` 是 ES6 引入的一种新的原始数据类型，表示独一无二的值。每个通过 `Symbol()` 创建的 `symbol` 都是唯一的。
- **示例**：

```typescript
let sym1: symbol = Symbol("key");
let sym2: symbol = Symbol("key");

console.log(sym1 === sym2);
```

这些原始类型为 TypeScript 提供了强大的类型系统基础，帮助开发者在编码阶段就能发现很多潜在的类型错误，提升代码的健壮性和可维护性。

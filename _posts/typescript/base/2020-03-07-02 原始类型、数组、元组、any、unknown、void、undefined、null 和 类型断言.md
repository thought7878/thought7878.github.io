---
layout: post
title: 数据类型和类型断言
subtitle: 
categories: TypeScript
tags:
  - TypeScript
---

## 基本语法

```javascript
let num = 1;

let num: number = 1;
```

特殊说明：number 表示数字类型，**":"** 用来<u>分割变量和类型的分隔符</u>。

TypeScript 语法与 JavaScript 语法的区别在于，我们可以在 TypeScript 中显式声明变量 num 仅仅是数字类型，也就是说只需在变量 num 后添加: number 类型注解即可

number 换成其他的类型（比如 JavaScript 原始类型：number、string、boolean、null、undefined、symbol 等），此时，num 变量也就拥有了 TypeScript 同名的原始类型定义。

## 原始类型

### 字符串

```javascript
let firstname: string = "Captain"; // 字符串字面量
let familyname: string = String("S"); // 显式类型转换
let fullname: string = `my name is ${firstname}.${familyname}`; // 模板字符串
```

### 数字

```javascript
/** 十进制整数 */
let integer: number = 6;
/** 十进制整数 */
let integer2: number = Number(42);
/** 十进制浮点数 */
let decimal: number = 3.14;
/** 二进制整数 */
let binary: number = 0b1010;
/** 八进制整数 */
let octal: number = 0o744;
/** 十六进制整数 */
let hex: number = 0xf00d;
```

如果使用较少的大整数，那么我们可以使用 bigint 类型来表示

```javascript
let big: bigint = 100n;
```

请注意：虽然 number 和 bigint 都表示数字，但是这两个类型不兼容。

### 布尔值

使用 boolean 表示 True 或者 False

```javascript
/** TypeScript 真香 为 真 */
let TypeScriptIsGreat: boolean = true;
/** TypeScript 太糟糕了 为 否 */
let TypeScriptIsBad: boolean = false;
```

### Symbol

自 ECMAScript 6 起，TypeScript 开始支持新的 Symbol 原始类型， 即我们可以通过 Symbol 构造函数，创建一个独一无二的标记；同时，还可以使用 symbol 表示如下代码所示的类型。

```javascript
let sym1: symbol = Symbol();
let sym2: symbol = Symbol("42");
```

当然，TypeScript 还包含 Number、String、Boolean、Symbol 等类型（注意区分大小写）。

实际上，我们压根使用不到 Number、String、Boolean、Symbol 类型，因为它们并没有什么特殊的用途。这就像我们不必使用 JavaScript Number、String、Boolean 等构造函数 new 一个相应的实例一样。

## 数组

### 数组类型（Array）

在 TypeScript 中，我们也可以像 JavaScript 一样定义数组类型，并且指定数组元素的类型。

#### 使用 [] 的形式定义

```javascript
/** 子元素是数字类型的数组 */
let arrayOfNumber: number[] = [1, 2, 3];
/** 子元素是字符串类型的数组 */
let arrayOfString: string[] = ["x", "y", "z"];
```

#### 使用 Array 泛型定义

```javascript
/** 子元素是数字类型的数组 */
let arrayOfNumber: Array<number> = [1, 2, 3];
/** 子元素是字符串类型的数组 */
let arrayOfString: Array<string> = ["x", "y", "z"];
```

以上两种定义数组类型的方式虽然本质上没有任何区别，但是更推荐使用 [] 这种形式来定义。**一方面可以避免与 JSX 的语法冲突，另一方面可以减少不少代码量。**

如果我们明确指定了数组元素的类型，以下所有操作都将因为不符合类型约定而提示错误。

```javascript
let arrayOfNumber: number[] = ["x", "y", "z"]; // 提示 ts(2322)
arrayOfNumber[3] = "a"; // 提示 ts(2322)
arrayOfNumber.push("b"); // 提示 ts(2345)
let arrayOfString: string[] = [1, 2, 3]; // 提示 ts(2322)
arrayOfString[3] = 1; // 提示 ts(2322)
arrayOfString.push(2); // 提示 ts(2345)
```

### 元组类型（Tuple）

#### Why

元组最重要的特性是**可以限制数组元素的个数和类型，它特别适合用来实现多值返回**。

我们熟知的<u>一个使用元组的场景是 React Hooks</u>，例如 useState 示例：

```javascript
import { useState } from 'react';
function useCount() {
  const [count, setCount] = useState(0);
  return ....;
}
```
#### How
对于 TypeScript 而言，如下所示的两个元组类型其实并不相同：

```ts
// [State, SetState]
// [(SetState, State)];
```

所以添加了不同元组类型注解的数组后，在 TypeScript 静态类型检测层面就变成了两个不相同的元组，如下代码所示：

```javascript
const x: [State, SetState] = [state, setState];
const y: [SetState, State] = [setState, state];
```

比如 useState 的返回值类型是一个元组类型，如下代码所示（以下仅是简单的例子，事实上 useState 的类型定义更为复杂）：

```javascript
(state: State) => [State, SetState];
```

## 特殊类型

### any

#### Why
any 指的是一个**任意类型**，它是官方提供的一个选择性**绕过静态类型检测**的作弊方式。

#### How

我们可以对被注解为 any 类型的变量进行任何操作，包括获取事实上并不存在的属性、方法，并且 TypeScript 还**无法检测其属性是否存在、类型是否正确**。

比如我们可以**把任何类型的值赋值给 any 类型的变量，也可以把 any 类型的值赋值给任意类型（除 never 以外）的变量**。

```javascript
let anything: any = {};
anything.doAnything(); // 不会提示错误
anything = 1; // 不会提示错误
anything = "x"; // 不会提示错误
let num: number = anything; // 不会提示错误
let str: string = anything; // 不会提示错误
```

- any 使用场景 1

如果我们**不想花费过高的成本为复杂的数据添加类型注解，或者已经引入了缺少类型注解的第三方组件库**，这时就可以把这些值全部注解为 any 类型，并告诉 TypeScript 选择性地忽略静态类型检测。

- any 使用场景 2

尤其是在**将一个基于 JavaScript 的应用改造成 TypeScript 的过程中**，我们不得不借助 any 来选择性添加和忽略对某些 JavaScript 模块的静态类型检测，直至逐步替换掉所有的 JavaScript。

- any 的传导性

any 类型会在对象的调用链中进行传导，即所有 any 类型的任意属性的类型都是 any，如下代码所示：

```javascript
let anything: any = {};
let z = anything.x.y.z; // z 类型是 any，不会提示错误
z(); // 不会提示错误
```

这里我们需要明白且记住：Any is Hell（Any 是地狱）。

- any 的缺点

从长远来看，使用 any 绝对是一个坏习惯。**如果一个 TypeScript 应用中充满了 any，此时静态类型检测基本起不到任何作用，也就是说与直接使用 JavaScript 没有任何区别**。因此，除非有充足的理由，否则我们应该**尽量避免使用 any** ，并且**开启禁用隐式 any 的设置**。

### unknown

unknown 是 TypeScript 3.0 中添加的一个类型，它**主要用来描述类型并不确定的变量**。

#### unknown 的使用场景：动态类型

比如在多个 if else 条件分支场景下，它可以用来接收不同条件下类型各异的返回值的临时变量，如下代码所示：

```javascript
let result: unknown;
if (x) {
  result = x();
} else if (y) {
  result = y();
}
```

在 3.0 以前的版本中，只有使用 any 才能满足这种动态类型场景。

#### 与 any 的对比

**与 any 不同的是，unknown 在类型上更安全**。比如我们可以将任意类型的值赋值给 unknown，但 unknown 类型的值只能赋值给 unknown 或 any

```javascript
let result: unknown;
let num: number = result; // 提示 ts(2322)
let anything: any = result; // 不会提示错误
```

#### unknown 的使用场景：缩小类型（Type Narrowing）

使用 unknown 后，TypeScript 会对它做类型检测。但是，如果不缩小类型（Type Narrowing），我们对 unknown 执行的任何操作都会出现如下所示错误：

```javascript
let result: unknown;
result.toFixed(); // 提示 ts(2571)
```

而所有的类型缩小手段对 unknown 都有效，如下代码所示：

```javascript
let result: unknown;
if (typeof result === "number") {
  result.toFixed(); // 此处 hover result 提示类型是 number，不会提示错误
}
```

### void、undefined、null

依照官方的说法，**它们实际上并没有太大的用处**。尤其是强烈推荐的 strict 模式下，它们是**名副其实的“废柴”**。

#### void 使用场景

它**仅适用于表示没有返回值的函数**。即如果该函数没有返回值，那它的类型就是 void。

在 strict 模式下，声明一个 void 类型的变量几乎没有任何实际用处，因为我们不能把 void 类型的变量值再赋值给除了 any 和 unkown 之外的任何类型变量。

#### undefined 和 null 

它们是 TypeScript **值与类型关键字同名**的唯二例外。但这并不影响它们被称为“废柴”，因为单纯声明 undefined 或者 null 类型的变量也是无比鸡肋，示例如下所示：

```javascript
let undeclared: undefined = undefined; // 鸡肋
let nullable: null = null; // 鸡肋
```

#### undefined 使用场景

undefined 的最大价值**主要使用在接口上，它表示一个可缺省、未定义(没有赋值)的属性**。

这里分享一个稍微有点费解的设计：我们可以把 undefined 值或类型是 undefined 的变量赋值给 void 类型变量，反过来，类型是 void 但值是 undefined 的变量不能赋值给 undefined 类型。

```javascript
const userInfo: {
  id?: number,
} = {};
let undeclared: undefined = undefined;
let unusable: void = undefined;
unusable = undeclared; // ok
undeclared = unusable; // ts(2322)
```

#### null 使用场景

**主要使用在接口上，它表明对象或属性可能是空值**。**尤其是在前后端交互的接口**，比如 Java Restful、Graphql，任何涉及查询的属性、对象都可能是 null 空对象，如下代码所示：

```javascript
const userInfo: {
  name: null | string,
} = { name: null };
```

#### undefined 和 null 的使用场景：非空判断，保障 JavaScript 运行时的安全性

除此之外，undefined 和 null 类型还具备警示意义，它们可以提醒我们针对可能操作这两种类型值的情况做容错处理。

我们需要类型守卫在操作之前判断值的类型是否支持当前的操作。**类型守卫**既能通过类型缩小影响 TypeScript 的类型检测，也能保障 JavaScript 运行时的安全性，如下代码所示：

```javascript
const userInfo: {
  id?: number,
  name?: null | string,
} = { id: 1, name: "Captain" };
if (userInfo.id !== undefined) {// Type Guard
  userInfo.id.toFixed(); // id 的类型缩小成 number
}
```

我们不建议随意使用**非空断言**来排除值可能为 null 或 undefined 的情况，因为这样很不安全。

```javascript
userInfo.id!.toFixed(); // ok，但不建议
userInfo.name!.toLowerCase() // ok，但不建议
```

TODO: 总结目录 [资料](https://github.com/e2tox/blog/issues/9)

而比非空断言更安全、类型守卫更方便的做法是使用**单问号（Optional Chain）、双问号（空值合并）**，我们可以使用它们来保障代码的安全性，如下代码所示：

```javascript
userInfo.id?.toFixed(); // Optional Chain
const myName = userInfo.name ?? `my name is ${info.name}`; // 空值合并
```

### never
#### Why

**never 表示永远不会发生值的类型**。

#### 发生never的情况

- 抛出错误的函数

这里我们举一个实际的场景进行说明。首先，我们定义一个统一*抛出错误的函数*，代码示例如下：

```js
function ThrowError(msg: string): never {
  throw Error(msg);
}
```

**以上函数因为永远不会有返回值，所以它的返回值类型就是 never**。

- 死循环的函数

同样，如果*函数代码中是一个死循环*，那么这个函数的返回值类型也是 never，如下代码所示。

```ts
function InfiniteLoop(): never {
  while (true) {}
}
```

#### 与其他类型的关系

**never 是所有类型的子类型，它可以给所有类型赋值**。如下代码所示：

```ts
let Unreachable: never = 1; // ts(2322)
Unreachable = 'string'; // ts(2322)
Unreachable = true; // ts(2322)
let num: number = Unreachable; // ok
let str: string = Unreachable; // ok
let bool: boolean = Unreachable; // ok
```

但是反过来，**除了 never 自身以外，其他类型（包括 any 在内的类型）都不能为 never 类型赋值**。

#### 类型缩小时的问题

在恒为 false 的类型守卫条件判断下，变量的类型将缩小为 never（never 是所有其他类型的子类型，所以是类型缩小为 never，而不是变成 never）。因此，条件判断中的相关操作始终会报无法更正的错误（我们可以把这理解为一种基于静态类型检测的 Dead Code 检测机制），如下代码所示：

```ts
const str: string = 'string';
if (typeof str === 'number') {
  str.toLowerCase(); // Property 'toLowerCase' does not exist on type 'never'.ts(2339)
}
```

#### 特殊用例：设置只读属性

基于 never 的特性，我们还可以使用 never 实现一些有意思的功能。比如我们可以把 never 作为接口类型下的属性类型，**用来禁止写接口下特定的属性**，示例代码如下：

```ts
const props: {
  id: number,
  name?: never
} = {
  id: 1
}
props.name = null; // ts(2322))
props.name = 'str'; // ts(2322)
props.name = 1; // ts(2322)
```

此时，无论我们给 props.name 赋什么类型的值，它都会提示类型错误，**实际效果等同于 name 只读** 。

### object

**object 类型表示非原始类型的类型**，即非  number、string、boolean、bigint、symbol、null、undefined 的类型。然而，它也是个**没有什么用武之地**的类型。如下所示的一个应用场景是用来表示 Object.create 的类型。

```ts
declare function create(o: object | null): any;
create({}); // ok
create(() => null); // ok
create(2); // ts(2345)
create('string'); // ts(2345)
```

## 类型断言（Type Assertion）

### Why & 解决的问题

**TypeScript 类型检查无法做到绝对智能**，毕竟程序不能像人一样思考。**有时会碰到我们比 TypeScript 更清楚实际类型的情况，因为静态类型检查对运行时的逻辑无能为力**。

比如下面的例子：

```ts
const arrayNumber: number[] = [1, 2, 3, 4];
const greaterThan2: number = arrayNumber.find(num => num > 2); // 提示 ts(2322)
```

其中，greaterThan2 一定是一个数字（确切地讲是 3），因为 arrayNumber 中明显有大于 2 的成员，但静态类型检查对运行时的逻辑无能为力。

*在 TypeScript 看来，greaterThan2 的类型既可能是数字，也可能是 undefined*，所以上面的示例中提示了一个 ts(2322) 错误，此时我们不能把类型 undefined 分配给类型 number。不过，我们可以使用一种<u>笃定</u>的方式——**类型断言**（类似仅作用在类型层面的强制类型转换）**告诉 TypeScript 按照我们的方式做类型检查**。

### as 语法

使用 as 语法做类型断言，如下代码所示：

```ts
const arrayNumber: number[] = [1, 2, 3, 4];
const greaterThan2: number = arrayNumber.find(num => num > 2) as number;
```

### 尖括号 + 类型

又或者是使用**尖括号 + 类型**的格式做类型断言，如下代码所示：

```ts
const arrayNumber: number[] = [1, 2, 3, 4];
const greaterThan2: number = <number>arrayNumber.find(num => num > 2);
```

### 更推荐使用 as 语法

以上两种方式虽然没有任何区别，但是**尖括号格式会与 JSX 产生语法冲突，因此我们更推荐使用 as 语法**。

### 使用类型断言的条件：父子、子父类型之间可以使用类型断言进行转换

> 注意：类型断言的操作对象必须满足某些约束关系，否则我们将得到一个 ts(2352) 错误，即从“源类型”到“目标类型”的转换是错误的，因为**这两种类型不能充分重叠**。

我一度喜欢用“指鹿为马”来形容类型断言，但其实也不够准确。从物种类型上看，鹿和马肯定不能转换，虽然它们都是动物（继承自同一个父类），但是鹿有“角属性”，马有“鬃毛属性”，所以<u>两者不能充分重叠</u>。

**如果我们把它换成“指白马为马”“指马为白马”，就可以很贴切地体现类型断言的约束条件：父子、子父类型之间可以使用类型断言进行转换。**

> **注意**：**这个结论完全适用于复杂类型**，但是对于 number、string、boolean 原始类型来说，*不仅父子类型可以相互断言，父类型相同的类型也可以相互断言*，比如 1 as 2、'a' as 'b'、true as false（这里的 2、'b'、false 被称之为字面量类型），反过来 2 as 1、'b' as 'a'、false as true 也是被允许的（这里的 1、'a'、true 是字面量类型），*尽管这样的断言没有任何意义*。

### any 和 unknown 的断言

另外，any 和 unknown 这两个特殊类型属于万金油，因为**它们既可以被断言成任何类型，反过来任何类型也都可以被断言成 any 或 unknown**。因此，如果我们想强行“指鹿为马”，就可以先把“鹿”断言为 any 或 unknown，然后再把 any 和 unknown 断言为“马”，比如鹿 as any as 马。

### 常量断言

使用 *“字面量值 + as const”* 语法结构进行常量断言，具体示例如下所示：

```ts
/** str 类型是 '"str"' */
let str = 'str' as const;
/** readOnlyArr 类型是 'readonly [0, 1]' */
const readOnlyArr = [0, 1] as const;
```


### 非空断言

此外还有一种特殊非空断言，即*在值（变量、属性）的后边添加 '!' 断言操作符*，它可以**用来排除值为 null、undefined 的情况**，具体示例如下：

```ts
let mayNullOrUndefinedOrString: null | undefined | string;
mayNullOrUndefinedOrString!.toString(); // ok
mayNullOrUndefinedOrString.toString(); // ts(2531)
```

对于非空断言来说，**我们同样应该把它视作和 any 一样危险的选择**。在复杂应用场景中，如果我们使用非空断言，就要保证一定非空，比如页面中一定存在 id 为 feedback 的元素，数组中一定有满足 > 2 条件的数字。*而一旦无法保证，错误只会在运行环境中抛出，而静态类型检测是发现不了这些错误的*。

所以，**建议使用类型守卫来代替非空断言**，比如如下所示的条件判断：

```ts
let mayNullOrUndefinedOrString: null | undefined | string;
if (typeof mayNullOrUndefinedOrString === 'string') {
  mayNullOrUndefinedOrString.toString(); // ok
}
```

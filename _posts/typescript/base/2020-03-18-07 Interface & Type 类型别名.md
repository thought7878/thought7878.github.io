---
layout: post
title: Interface & Type 类型别名
subtitle: 
categories: TypeScript
tags:
  - TypeScript
---

前面我们学习了 TypeScript 的基础类型、函数类型和类类型，它们都有一个共性——在 JavaScript 中都有对应的语法。

## Why

这一讲将学习 TypeScript 与 JavaScript 不一样却堪称精华之一的特性——<u>接口类型与类型别名</u>。**这些特性让 TypeScript 具备了 JavaScript 所缺少的、描述较为复杂数据结构的能力**。**在使用 TypeScript 之前，可能我们只能通过文档或大量的注释来做这件事**。
*TypeScript 不仅能帮助前端改变思维方式，还能强化面向接口编程的思维和能力，而这正是得益于 Interface 接口类型*。通过接口类型，可以清晰地定义模块内、跨模块、跨项目代码的**通信规则**。
## Interface 接口类型

### 类型检查的规则

TypeScript 对对象的类型检测遵循一种被称之为“**鸭子类型**”（duck typing）或者“**结构化类型（structural subtyping）**”的准则，即**只要两个对象的结构一致，属性和方法的类型一致，则它们的类型就是一致的**。

### 内联接口类型

初识一下接口类型。如下代码所示：

```ts
function Study(language: { name: string; age: () => number }) {
  console.log(
    `ProgramLanguage ${language.name} created ${language.age()} years ago.`
  );
}
Study({
  name: "TypeScript",
  age: () => new Date().getFullYear() - 2012,
});
```

在上述代码中，我们定义了一个拥有 string 类型属性 name、函数类型属性 age 的对象 language 作为参数（形参 Parameter）的函数。同时，我们还*使用类似定义 JavaScript 对象字面量的语法*定义了一个*内联接口类型*来约束参数对象的类型。然后，我们传递了一个 name 属性为 'TypeScript' 的字符串、age 属性为计算年份差函数的对象字面量作为参数（argument）来调用这个函数。在调用函数的过程中，TypeScript 静态类型检测到传递的对象字面量类型为 string 的 name 属性和类型为() => number 的 age 属性与函数参数定义的类型一致，于是不会抛出一个类型错误。

#### 缺少属性

*如果传入一个 name 属性是 number 类型或者缺少 age 属性的对象字面量*，如下代码所示：

```ts
Study({
  name: 2,
  age: () => new Date().getFullYear() - 2012,
});
Study({
  name: "TypeScript",
});
```

这时，第 2 行会提示错误： ts(2322) number 不能赋值给 string，第 7 行也会提示错误： ts(2345) 实参(Argument)与形参(Parameter)类型不兼容，缺少必需的属性 age。

#### 多余属性（对象字面量报错）

*如果传入一个包含了形参类型定义里没有的 id 属性的对象字面量作为实参*，也会得到一个类型错误 ts(2345)，实参（Argument）与形参（Parameter）类型不兼容，不存在的属性 id，如下代码所示：

```ts
/** ts(2345) 实参(Argument)与形参(Parameter)类型不兼容，不存在的属性 id */
Study({
  id: 2,
  name: "TypeScript",
  age: () => new Date().getFullYear() - 2012,
});
```

#### 多余属性（变量不报错）

有意思的是，在上边的示例中，<u>如果我们先把这个对象字面量赋值给一个变量，然后再把变量传递给函数进行调用，那么 TypeScript 静态类型检测就会仅仅检测形参类型中定义过的属性类型，而包容地忽略任何多余的属性</u>，此时也不会抛出一个 ts(2345) 类型错误。

如下代码所示，第 6 行不会提示错误。

```ts
let ts = {
  id: 2,
  name: "TypeScript",
  age: () => new Date().getFullYear() - 2012,
};
Study(ts); // ok
```

**这并非一个疏忽或 bug，而是有意为之地将对象字面量和变量进行区别对待**，把这种情况称之为**对象字面量的 freshness**（在 12 “类型兼容”中有详细介绍）。

#### 内联接口类型 对比 解构语法

因为这种**内联形式的接口类型定义**在语法层面与熟知的 JavaScript 解构颇为神似，所以很容易让我们产生混淆。下面我们通过如下示例，<u>对比一下解构语法与内联接口类型混用</u>的效果。

```ts
/** 纯 JavaScript 解构语法 */
function StudyJavaScript({ name, age }) {
  console.log(name, age);
}
/** TypeScript 里解构与内联类型混用 */
function StudyTypeScript({ name, age }: { name: string; age: () => number }) {
  console.log(name, age);
}
/** 纯 JavaScript 解构语法，定义别名 */
function StudyJavaScript({ name: aliasName }) {
  // 定义name的别名
  console.log(aliasName);
}
/** TypeScript */
function StudyTypeScript(language: { name: string }) {
  // console.log(name); // 不能直接打印name
  console.log(language.name);
}
```

上述代码的函数中，对象解构和定义接口类型的语法很类似（如第 12 行和 17 行所示），注意不要混淆。实际上，*定义内联的接口类型是不可复用的，所以我们应该更多地使用`interface`关键字来抽离可复用的接口类型*。

### Interface 定义语法

在 TypeScript 中，接口的定义：

```ts
/ ** 关键字 接口名称 */;
interface ProgramLanguage {
  /** 属性名：属性值的类型。语言名称：string类型 */
  name: string;
  /** 属性名：属性值的类型。使用年限：函数类型 */
  age: () => number;
}
```

在上述代码中，我们定义了一个描述编程语言的接口，它包含一个字符类型的属性 name 和一个函数类型的属性 age 。 从中发现接口的语法格式是在 interface 关键字的空格后+接口名字，然后属性名与属性值类型的定义用花括弧包裹。

内联接口定义的 Study 函数，修改为 ProgramLanguage 接口定义的参数 language 的类型：

```ts
function NewStudy(language: ProgramLanguage) {
  console.log(
    `ProgramLanguage ${language.name} created ${language.age()} years ago.`
  );
}
```

#### 复用接口

通过<u>复用接口类型</u>来约束其他变量。比如，通过如下所示代码定义了一个类型为 ProgramLanguage 的变量 TypeScript 。把满足接口类型的一个对象字面量赋值给了这个变量。

```ts
let TypeScript: ProgramLanguage;

TypeScript = {
  name: "TypeScript",
  age: () => new Date().getFullYear() - 2012,
};
```


#### 各种错误用法

- **缺少属性**

而任何不符合约定的情况，都会提示类型错误。比如我们通过如下所示代码输入了一个空对象字面量，此时也会提示一个对象字面量类型 {} 缺少 name 和 age 属性的 ts(2739) 错误。

```ts
TypeScript = {};
```

按照如下所示代码添加 name 属性后，还是会提示一个对象字面量类型 { name: string; } 缺少必需的 age 属性的 ts( 2741) 错误。

```ts
TypeScript = {
  name: "TypeScript",
};
```

- **属性值的类型错误**

此外，如下代码所示，如果我们把一个 name 属性是 2、age 属性是 'Wrong Type' 的对象赋值给 TypeScript ，在第 2 行会提示错误：ts(2322) number 类型不能赋值给 string，第 3 行会提示错误：ts(2322)string 不能赋值给函数类型。

```ts
TypeScript = {
  name: 2,
  age: "Wrong Type",
};
```

- **多余属性**

又或者如以下示例中额外多出了一个接口并未定义的属性 id，也会提示一个 ts(2322) 错误：对象字面量不能赋值给 ProgramLanguage 类型的变量 TypeScript。

```ts
TypeScript = {
  name: "TypeScript",
  age: () => new Date().getFullYear() - 2012,
  id: 1,
};
```

### 可缺省属性

- **Why / 问题** 

在前边的例子中，如果希望缺少 age 属性的变量也能符合接口类型，不抛出类型错误。

- **语法**

在接口中定义 age 属性可缺省，**在属性名之后添加 `?`**，标注可缺省的属性或方法。如以下示例中，OptionalProgramLanguage 接口就拥有一个可缺省的函数类型的 age 属性：

```ts
/** 关键字 接口名称 */
interface OptionalProgramLanguage {
  /** 语言名称 */
  name: string;
  /** 使用年限 */
  age?: () => number;
}
let OptionalTypeScript: OptionalProgramLanguage = {
  name: "TypeScript",
}; // ok
```

- **可缺省属性的类型**

当属性被标注为可缺省后，它的类型就变成了**显式指定的类型与 undefined 类型组成的联合类型**。比如示例中 OptionalTypeScript 的 age 属性类型就变成了如下所示内容：

```ts
(() => number) | undefined;
```

- **对比**：`age?: () => number;` `age: (() => number) | undefined;`

既然如此，我们就来发散思考一下：你觉得如下所示的接口类型 OptionalTypeScript2 和 OptionalTypeScript 等价吗？

```ts
interface OptionalProgramLanguage2 {
  
  name: string;
  /** 对比 */
  age: (() => number) | undefined;
  age?: () => number;
}
```

答案当然是不等价，**可缺省意味着可以不设置属性键名，类型是 undefined 意味着属性键名不可缺省**。这与 05 讲中提到函数可缺省参数和参数类型为 undefined 是一样的。

- **使用类型守卫或 Optional Chain**

既然值可能是 undefined ，需要对该对象的属性或方法进行操作，就可以使用类型守卫或 Optional Chain。如下代码所示：

```ts
// 类型守卫
if (typeof OptionalTypeScript.age === "function") {
  OptionalTypeScript.age();
}
// Optional Chain：更简洁
OptionalTypeScript.age?.();
```

通过 typeof 条件判断，在确保了 age 属性是函数的情况下我们才会调用，*这样就避免了运行时提示 age 不是函数的错误*。

### 只读属性

- **Why / 问题 / 场景**

*这样的场景，不希望对某个属性或方法进行写操作*。比如前面例子中，定义了 TypeScriptLanguage 变量之后，name 属性的值肯定是稳定不可变更的 'TypeScript' ，而不能再被变更为 'JavaScript' 或 'AnyScript' 。

- **语法**

这时，可以在属性名前通过添加 `readonly` 修饰符的语法来标注 name 为只读属性。

```ts
interface ReadOnlyProgramLanguage {
  /** 语言名称 */
  readonly name: string;
  /** 使用年限 */
  readonly age: (() => number) | undefined;
}

let ReadOnlyTypeScript: ReadOnlyProgramLanguage = {
  name: "TypeScript",
  age: undefined,
};
/** ts(2540)错误，name 只读 */
ReadOnlyTypeScript.name = "JavaScript";
```

需要注意的是，**这仅仅是静态类型检测层面的只读，实际上并不能阻止对对象的篡改。因为在转译为 JavaScript 之后，readonly 修饰符会被抹除**。因此，任何时候与其直接修改一个对象，不如返回一个新的对象，这会是一种比较安全的实践。

### 定义函数类型

接口不仅能用来定义对象的类型，**还可以用来定义函数的类型**（如 05 中提到） 。仅仅是定义函数的类型，而不包含函数的实现。具体示例如下：

```ts
interface StudyInterface {
  (language: ProgramLanguage): void;
}
/** 单独的函数实践 */
let studyLanguage: StudyInterface = (language) =>
  console.log(`${language.name} ${language.age()}`);
```

定义了一个接口类型 StudyInterface，<u>它有一个函数类型的匿名成员，函数参数类型是 ProgramLanguage，返回值的类型是 void</u>，通过这样的格式定义的接口类型又被称为**可执行类型**，也就是**函数类型**。StudyInterface 类型的变量studyLanguage，并赋给它一个箭头函数作为值。根据**上下文类型推断**（04 讲中提到的），赋值操作左侧的 StudyInterface 类型是可以约束箭头函数的类型，所以即便我们没有显式指定函数参数 language 的类型，TypeScript 也能推断出它的类型就是 ProgramLanguage。

#### 很少使用接口定义函数的类型

实际上，**很少使用接口来定义函数的类型**，**更多使用内联类型或类型别名配合箭头函数语法来定义函数类型**。具体示例如下：

```ts
type StudyLanguageType = (language: ProgramLanguage) => void;
```

给箭头函数类型指定了一个**别名** StudyLanguageType，在其他地方就可以直接复用 StudyLanguageType，而不用重新声明新的箭头函数类型定义。

### 定义属性名的类型 / 索引签名

在实际工作中，使用接口较多的地方是对象，比如 React 组件的 Props & State、HTMLElement 的 Props。*这些对象有一个共性，即所有的属性名、方法名都确定*。

- **Why / 问题**

**我们经常会把对象当 Map 映射使用**。比如下边代码示例中定义了索引是任意数字的对象 LanguageRankMap 和索引是任意字符串的对象 LanguageMap。

```ts
let LanguageRankMap = {
  1: 'TypeScript',
  2: 'JavaScript',
  ...
};
let LanguageMap = {
  TypeScript: 2012,
  JavaScript: 1995,
  ...
};
```

- **语法**

这个时候，我们需要**使用索引签名来定义上边提到的对象映射结构**，并通过 **“[索引名: 类型]”** 的格式**约束索引的类型**。**索引名称的类型**分为 `string` 和 `number` 两种。通过如下定义的 LanguageRankInterface 和 LanguageYearInterface 两个接口，可以用来描述，索引、属性名的类型，是任意数字或任意字符串的对象。

```ts
interface LanguageRankInterface {
  [rank: number]: string;
}
interface LanguageYearInterface {
  [name: string]: number;
}
{
  let LanguageRankMap: LanguageRankInterface = {
    1: "TypeScript", // ok
    2: "JavaScript", // ok
    WrongINdex: "2012", // ts(2322) 不存在的属性名
  };

  let LanguageMap: LanguageYearInterface = {
    TypeScript: 2012, // ok
    JavaScript: 1995, // ok
    1: 1970, // ok
  };
}
```

**注意：在上述示例中，数字作为对象索引时，它的类型既可以是数字，也可以是字符串，这与 JavaScript 的行为一致。因此，使用 0 或 '0' 索引对象时，这两者等价。**

- **readonly**

同样，我们可以使用 readonly 注解索引签名，此时将对应属性设置为只读就行，如下代码所示：

```ts
{
  interface LanguageRankInterface {
    readonly [rank: number]: string;
  }

  interface LanguageYearInterface {
    readonly [name: string]: number;
  }
}
```

在上述示例中，LanguageRankInterface 和 LanguageYearInterface 任意的数字或者字符串类型的**属性都是只读的**。

- **属性的定义与索引签名混用**

*虽然属性的定义可以与索引签名进行混用，但是属性值的类型必须是对应的数字索引或字符串索引的值类型的子集*，否则会出现错误提示。示例如下：

```ts
{
  interface StringMap {
    [prop: string]: number;
    age: number; // ok
    name: string; // ts(2411) name 属性的 string 类型不能赋值给字符串索引类型 number
  }
  interface NumberMap {
    [rank: number]: string;
    1: string; // ok
    0: number; // ts(2412) 0 属性的 number 类型不能赋值给数字索引类型 string
  }
  interface LanguageRankInterface {
    name: string; // ok
    0: number; // ok
    [rank: number]: string;
    [name: string]: number;
  }
}
```

在上述示例中，因为接口 StringMap 属性 name 的值类型 string 不是它所对应的字符串索引（第 3 行定义的 prop: string）的值类型 number 的子集，所以会提示一个错误。同理，因为接口 NumberMap 属性 0 的值类型 number 不是它所对应的数字索引（第 8 行定义的 rank: number）的值类型 string 的子集，所以也会提示一个错误。

由于上边提到了数字类型索引的特殊性，所以*不能约束数字索引属性与字符串索引属性拥有截然不同的类型*。具体示例如下：

```ts
{
  interface LanguageRankInterface {
    [rank: number]: string; // ts(2413) 数字索引类型 string 类型不能赋值给字符串索引类型 number
    [prop: string]: number;
  }
}
```

*LanguageRankInterface 的数字索引 rank 的值类型是 string，与定义的字符串索引 prop 的值类型 number 不兼容*，所以会提示一个 ts(2413) 错误。

*这里埋个伏笔*：如果确实需要使用 age 是 number 类型、其他属性类型是 string 的对象数据结构，应该如何定义它的类型且不提示错误呢？如下示例中定义的 age 属性是数字、其他任意属性是字符串的对象，我们应该怎么定义它的类型呢？

```ts
{
  age: 1, // 数字类型
  anyProperty: 'str', // 字符串
  ...
}
```

由于属性和索引签名的类型限制，使得我们不能通过单一的接口来描述这个对象，这时我们该怎么办呢？请继续保持你的好奇心，08 讲中我们会解决这个问题。

### 继承：子接口 继承 父接口的属性

#### 语法

在 TypeScript 中，**接口类型之间，可以继承和被继承**。**使用 `extends` 关键字实现接口的继承**。如下所示：

```ts
{
  interface ProgramLanguage {
    name: string;
    age: () => number;
  }
  
  interface DynamicLanguage extends ProgramLanguage {
    rank: number; // 定义新属性
  }
  
  interface TypeSafeLanguage extends ProgramLanguage {
    typeChecker: string; // 定义新的属性
  }
  
  /** 继承多个 */
  interface TypeScriptLanguage extends DynamicLanguage, TypeSafeLanguage {
    name: "TypeScript"; // 用原属性类型的兼容的类型(比如子集)重新定义属性
  }
}
```

在上述示例中，定义了两个继承 ProgramLanguage 的接口 DynamicLanguage 和 TypeSafeLanguage，*它们会继承 ProgramLanguage 所有的属性定义*。定义了同时继承了 DynamicLanguage 和 TypeSafeLanguage 的接口 TypeScriptLanguage，*它会继承 DynamicLanguage 和 TypeSafeLanguage 所有的属性定义*，并且**使用同名的 name 属性定义覆盖了继承过来的 name 属性定义**。

#### 覆盖继承的属性

**仅能使用兼容的类型（如子类型）覆盖继承的属性**。如下代码所示：

```ts
{
  interface ProgramLanguage {
    name: string;
    age: () => number;
  }
  
  /** ts(6196) 错误的继承，name 属性不兼容 */
  interface WrongTypeLanguage extends ProgramLanguage {
    name: number;
  }
}
```

*因为 ProgramLanguage 的 name 属性是 string 类型，WrongTypeLanguage 的 name 属性是 number，二者不兼容，所以不能继承*，也会提示一个 ts(6196) 错误。

### 实现：类 实现 接口定义的属性

#### 语法

既可以使用接口类型来约束类，反过来也可以**使用类实现接口定义的属性**，那两者之间的关系到底是什么呢？**通过 implements 关键字描述一下类和接口之间的关系**。

```ts
/** 类实现接口 */
{
  class LanguageClass implements ProgramLanguage {
    name: string = "";
    age = () => new Date().getFullYear() - 2012;
  }
}
```

*类 LanguageClass 实现了 ProgramLanguage 接口约定的 name、age 等属性和方法*。如果我们移除 name 或者 age 的实现，将会提示一个类型错误。

## Type 类型别名

### Why / 问题

接口类型的一个作用是将内联接口类型抽离出来，从而实现类型可复用。
**使用“type”定义一个新的类名（类型别名），接收抽离出来的内联类型，实现复用**。

### 语法

*类型别名，诚如其名，即仅仅是给类型取了一个新的名字，并不是创建了一个新的类型（不严谨，也可以创建新的类型，类型运算）*。**类型别名使得类型像值一样赋值给另外一个变量（别名）。大大提升了类型复用性，最终也提升了我们的编程效率。**

通过`type 别名名字 = 类型定义`的格式，定义类型别名。

```ts
/** 类型别名 */
{
  type LanguageType = {
    /** 以下是接口属性 */
    /** 语言名称 */
    name: string;
    /** 使用年限 */
    age: () => number;
  };
}
```

有点像在定义变量，只不过把 let 、const 、var 关键字换成了 type 。

### Why / 场景

**针对接口类型无法覆盖的场景，比如联合类型、交叉类型**（详见 08 讲），**只能使用类型别名来接收**。如下代码所示：

```ts
{
  /** 联合 */
  type MixedType = string | number;
  /** 交叉 */
  type IntersectionType = { id: number; name: string } & {
    age: number;
    name: string;
  };
  /** 提取接口属性类型 */
  type AgeType = ProgramLanguage["age"];
}
```

定义了一个 IntersectionType 类型别名，表示两个匿名接口类型交叉出的类型；定义了一个 AgeType 类型别名，表示抽取的 ProgramLanguage age 属性的类型。

## Interface 与 Type 的区别

### 相同点

*适用接口类型标注的地方大都可以使用类型别名进行替代，在大多数的情况下使用接口类型和类型别名的效果等价*。

### 不同点

- **type可以进行类型计算，产生新的类型；接口不行**

- **重复定义的同名接口，属性叠加**

**在某些特定的场景下，这两者还是存在很大区别：** 比如，重复定义的接口类型，它的*属性会叠加*，这个特性使得我们可以极其方便地对全局变量、第三方库的类型做扩展（感觉会导致混乱，为何不用extends）。如下代码所示：

```ts
{
  interface Language {
    id: number;
  }

  interface Language {
    name: string;
  }
  let lang: Language = {
    id: 1, // ok
    name: "name", // ok
  };
}
```

在上述代码中，先后定义的两个 Language 接口*属性被叠加在了一起*，此时我们可以赋值给 lang 变量一个同时包含 id 和 name 属性的对象。

- **重复定义的type，报错**

如果重复定义类型别名，如下代码所示，则会提示一个 ts(2300) 错误。

```ts
{
  /** ts(2300) 重复的标志 */
  type Language = {
    id: number;
  };

  /** ts(2300) 重复的标志 */
  type Language = {
    name: string;
  };
  let lang: Language = {
    id: 1,
    name: "name",
  };
}
```

重复定义了一个类型别名 Language ，此时就提示了一个错误。

## 小结

接口类型是 TypeScript 最核心的知识点之一，掌握好接口类型，养成面向接口编程思维方式和惯性，将让我们的编程之路愈发顺利、高效。

类型别名使得类型可以像值一样能赋予另外一个变量（别名），大大提升了类型复用性，最终也提升了我们的编程效率。


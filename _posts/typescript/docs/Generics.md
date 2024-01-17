A major part of software engineering is building components that not only have well-defined and consistent APIs, but are also reusable. Components that are capable of working on the data of today as well as the data of tomorrow will give you the most flexible capabilities for building up large software systems.  
软件工程的一个主要部分是构建不仅具有明确定义且一致的 API，而且可重用的组件。能够处理当前和未来数据的组件将为您提供构建大型软件系统的最灵活的功能。

In languages like C# and Java, one of the main tools in the toolbox for creating reusable components is *generics*, that is, being able to create a component that can work over a variety of types rather than a single one. This allows users to consume these components and use their own types.  
在 C# 和 Java 等语言中，工具箱中用于创建可重用组件的主要工具之一是泛型，即能够创建可以在多种类型而不是单一类型上工作的组件。这允许用户使用这些组件并使用他们自己的类型。

## Hello World of Generics

你好泛型世界

To start off, let’s do the “hello world” of generics: the identity function. The identity function is a function that will return back whatever is passed in. You can think of this in a similar way to the `echo` command.  
首先，让我们做一下泛型的“hello world”：恒等函数。身份函数是一个将返回传入的任何内容的函数。您可以将其视为与 `echo` 命令类似的方式。

Without generics, we would either have to give the identity function a specific type:  
如果没有泛型，我们要么必须为恒等函数指定一个特定的类型：

`   function identity(arg: number): number {    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAFAQwCcBzALkTBAFsAjNQgSnMtvsQG8AoRRQtKEISRFiAbk4BfIA)

Or, we could describe the identity function using the `any` type:  
或者，我们可以使用 `any` 类型来描述恒等函数：

`   function identity(arg: any): any {    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAFAQwCcBzALkXzGwEpzLtEBvAKEUULShEKSOIG5mAXyA)

While using `any` is certainly generic in that it will cause the function to accept any and all types for the type of `arg`, we actually are losing the information about what that type was when the function returns. If we passed in a number, the only information we have is that any type could be returned.  
虽然使用 `any` 当然是通用的，因为它将导致函数接受 `arg` 类型的任何和所有类型，但我们实际上正在丢失有关该类型的信息。函数返回。如果我们传入一个数字，我们所拥有的唯一信息就是可以返回任何类型。

Instead, we need a way of capturing the type of the argument in such a way that we can also use it to denote what is being returned. Here, we will use a *type variable*, a special kind of variable that works on types rather than values.  
相反，我们需要一种捕获参数类型的方法，以便我们也可以使用它来表示返回的内容。在这里，我们将使用类型变量，这是一种作用于类型而不是值的特殊变量。

`   function identity<Type>(arg: Type): Type {    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAPAFWwAc0A+ACgEMAnAcwC5FCSBKR5tRAbwChFFqaKCGpIatANw8AvkA)

We’ve now added a type variable `Type` to the identity function. This `Type` allows us to capture the type the user provides (e.g. `number`), so that we can use that information later. Here, we use `Type` again as the return type. On inspection, we can now see the same type is used for the argument and the return type. This allows us to traffic that type information in one side of the function and out the other.  
我们现在向恒等函数添加了一个类型变量 `Type` 。这个 `Type` 允许我们捕获用户提供的类型（例如 `number` ），以便我们稍后可以使用该信息。在这里，我们再次使用 `Type` 作为返回类型。经过检查，我们现在可以看到参数和返回类型使用相同的类型。这允许我们在函数的一侧传输该类型的信息，并从另一侧传输出去。

We say that this version of the `identity` function is generic, as it works over a range of types. Unlike using `any`, it’s also just as precise (i.e., it doesn’t lose any information) as the first `identity` function that used numbers for the argument and return type.  
我们说这个版本的 `identity` 函数是通用的，因为它适用于一系列类型。与使用 `any` 不同，它也与第一个使用数字作为参数和返回类型的 `identity` 函数一样精确（即，它不会丢失任何信息）。

Once we’ve written the generic identity function, we can call it in one of two ways. The first way is to pass all of the arguments, including the type argument, to the function:  
一旦我们编写了通用恒等函数，我们就可以通过两种方式之一调用它。第一种方法是将所有参数（包括类型参数）传递给函数：

`   let output = identity<string>("myString");          let output: string   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAPAFWwAc0A+ACgEMAnAcwC5FCSBKR5tRAbwChFFqaKCGpIatANw8AvjwD0cxAFoVEEFBVKeAGyGI46ousQBeZOkwwcuAM5RqMMLQoAiALbYAyvce0XLKQV+YP4APQB+IA)

Here we explicitly set `Type` to be `string` as one of the arguments to the function call, denoted using the `<>` around the arguments rather than `()`.  
这里我们显式地将 `Type` 设置为 `string` 作为函数调用的参数之一，表示使用参数周围的 `<>` 而不是 `()`

The second way is also perhaps the most common. Here we use *type argument inference* — that is, we want the compiler to set the value of `Type` for us automatically based on the type of the argument we pass in:  
第二种方式也许也是最常见的。这里我们使用类型参数推断——也就是说，我们希望编译器根据我们传入的参数类型自动设置 `Type` 的值：

`   let output = identity("myString");          let output: string   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAPAFWwAc0A+ACgEMAnAcwC5FCSBKR5tRAbwChFFqaKCGpIatANw8AvjwD0cxAFoVEEFBVKeAGyGI46ousQBeZOkwwc5AEQBbbAGUo1GGFo2WUhf1-8AegD8QA)

Notice that we didn’t have to explicitly pass the type in the angle brackets (`<>`); the compiler just looked at the value `"myString"`, and set `Type` to its type. While type argument inference can be a helpful tool to keep code shorter and more readable, you may need to explicitly pass in the type arguments as we did in the previous example when the compiler fails to infer the type, as may happen in more complex examples.  
请注意，我们不必在尖括号 ( `<>` ) 中显式传递类型；编译器只是查看值 `"myString"` ，并将 `Type` 设置为其类型。虽然类型参数推断是使代码更短、更具可读性的有用工具，但当编译器无法推断类型时，您可能需要显式传入类型参数，就像我们在前面的示例中所做的那样，这在更复杂的示例中可能会发生。

## [](https://www.typescriptlang.org/docs/handbook/2/generics.html#working-with-generic-type-variables)Working with Generic Type Variables

使用通用类型变量

When you begin to use generics, you’ll notice that when you create generic functions like `identity`, the compiler will enforce that you use any generically typed parameters in the body of the function correctly. That is, that you actually treat these parameters as if they could be any and all types.  
当您开始使用泛型时，您会注意到，当您创建像 `identity` 这样的泛型函数时，编译器将强制您在函数体中正确使用任何泛型类型参数。也就是说，您实际上将这些参数视为可以是任何类型。

Let’s take our `identity` function from earlier:  
让我们采用之前的 `identity` 函数：

`   function identity<Type>(arg: Type): Type {    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAPAFWwAc0A+ACgEMAnAcwC5FCSBKR5tRAbwChFFqaKCGpIatANw8AvkA)

What if we want to also log the length of the argument `arg` to the console with each call? We might be tempted to write this:  
如果我们还想在每次调用时将参数 `arg` 的长度记录到控制台怎么办？我们可能会想这样写：

`   function loggingIdentity<Type>(arg: Type): Type {    console.log(arg.length);  Property 'length' does not exist on type 'Type'.Property 'length' does not exist on type 'Type'.    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/PTAEAEFMCdoe2gZwFygEwGYME4BQAzAVwDsBjAFwEs5jQAbOAc0cuMYEkATSYq8gTwA8AFX4AHSAD4AFAENojVKIkBKJeMigA3rlChSNRHDqQAdA0ZyF5no3IALFQG5doaJHKFoteYxcBfIA)

When we do, the compiler will give us an error that we’re using the `.length` member of `arg`, but nowhere have we said that `arg` has this member. Remember, we said earlier that these type variables stand in for any and all types, so someone using this function could have passed in a `number` instead, which does not have a `.length` member.  
当我们这样做时，编译器会给我们一个错误，表明我们正在使用 `arg` 的 `.length` 成员，但我们没有说过 `arg` 有这个成员。请记住，我们之前说过这些类型变量代表任何和所有类型，因此使用此函数的人可以传入 `number` 来代替，它没有 `.length` 成员。

Let’s say that we’ve actually intended this function to work on arrays of `Type` rather than `Type` directly. Since we’re working with arrays, the `.length` member should be available. We can describe this just like we would create arrays of other types:  
假设我们实际上想让这个函数作用于 `Type` 数组，而不是直接作用于 `Type` 数组。由于我们使用的是数组，因此 `.length` 成员应该可用。我们可以像创建其他类型的数组一样描述这一点：

`   function loggingIdentity<Type>(arg: Type[]): Type[] {    console.log(arg.length);    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABAGzgczTMaCSATAUzFigE8AeAFVIAcCA+ACgEMAnNALkWroG0BdAJRceBAYgDeAKESIICAM5xkBAHSo0LduqJooAC0EBuGYlYEoIVkjZoTAXyA)

You can read the type of `loggingIdentity` as “the generic function `loggingIdentity` takes a type parameter `Type`, and an argument `arg` which is an array of `Type`s, and returns an array of `Type`s.” If we passed in an array of numbers, we’d get an array of numbers back out, as `Type` would bind to `number`. This allows us to use our generic type variable `Type` as part of the types we’re working with, rather than the whole type, giving us greater flexibility.  
您可以将 `loggingIdentity` 的类型解读为“泛型函数 `loggingIdentity` 接受类型参数 `Type` 和参数 `arg` ，该参数是 `Type` 数组，并返回 `Type` 数组。”如果我们传入一个数字数组，我们会得到一个数字数组，因为 `Type` 将绑定到 `number` 。这允许我们使用泛型类型变量 `Type` 作为我们正在使用的类型的一部分，而不是整个类型，从而为我们提供了更大的灵活性。

We can alternatively write the sample example this way:  
我们也可以这样编写示例：

`   function loggingIdentity<Type>(arg: Array<Type>): Array<Type> {    console.log(arg.length); // Array has a .length, so no more error    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABAGzgczTMaCSATAUzFigE8AeAFVIAcCA+ACgEMAnNALkQEFXXmK1OvQCUXXv0G0GiAN4AoRIggIAznGQEAdKjQt2OomigALEQG5EAeis8+AxCearEzRIeymANInWIwcIgAtnCsBIgEfKGKiGFQIKxIbGjm8gC+QA)

You may already be familiar with this style of type from other languages. In the next section, we’ll cover how you can create your own generic types like `Array<Type>`.  
您可能已经熟悉其他语言中的这种类型。在下一节中，我们将介绍如何创建自己的泛型类型，例如 `Array<Type>` 。

## [](https://www.typescriptlang.org/docs/handbook/2/generics.html#generic-types)Generic Types 通用类型

In previous sections, we created generic identity functions that worked over a range of types. In this section, we’ll explore the type of the functions themselves and how to create generic interfaces.  
在前面的部分中，我们创建了适用于多种类型的通用标识函数。在本节中，我们将探讨函数本身的类型以及如何创建通用接口。

The type of generic functions is just like those of non-generic functions, with the type parameters listed first, similarly to function declarations:  
泛型函数的类型与非泛型函数的类型相同，首先列出类型参数，与函数声明类似：

`   function identity<Type>(arg: Type): Type {    return arg;  }  let myIdentity: <Type>(arg: Type) => Type = identity;   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAPAFWwAc0A+ACgEMAnAcwC5FCSBKR5tRAbwChFFqaKCGpIatANw8Avjx4AbIYgC22AJLpMMHIwLEyVOu30tEAXlJN955JqzYJQA)

We could also have used a different name for the generic type parameter in the type, so long as the number of type variables and how the type variables are used line up.  
我们还可以为类型中的泛型类型参数使用不同的名称，只要类型变量的数量和类型变量的使用方式一致即可。

`   function identity<Input>(arg: Input): Input {    return arg;  }  let myIdentity: <Input>(arg: Input) => Input = identity;   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAPASTAAcQoA+ACgEMAnAcwC5FCSoBKJl0xAbwChEiGmiggaSWnQDcfAL58+AGxGIAttnzpMMHEwLFSlSZwPtEAXjLNTF5FqzYpQA)

We can also write the generic type as a call signature of an object literal type:  
我们还可以将泛型类型编写为对象字面量类型的调用签名：

`   function identity<Type>(arg: Type): Type {    return arg;  }  let myIdentity: { <Type>(arg: Type): Type } = identity;   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABDAJgUzLKBPAPAFWwAc0A+ACgEMAnAcwC5FCSBKR5tRAbwChFFqaKCGpIatANw8Avjx4AbIYgC22AJLpMMHIy6ICxMlTrtDbJocTTEAXmSas2CUA)

Which leads us to writing our first generic interface. Let’s take the object literal from the previous example and move it to an interface:  
这导致我们编写了第一个通用接口。让我们将前面示例中的对象文字移至接口：

`   interface GenericIdentityFn {    <Type>(arg: Type): Type;  }  function identity<Type>(arg: Type): Type {    return arg;  }  let myIdentity: GenericIdentityFn = identity;   `[Try](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgOIRNYCCSATDMYMATwDERkBvAKGWQB4AVEgBwgD4AKOKAcwBcyFuwCUQkRADcNAL40aMAK4gERAPaVgBcMRLM2nHvwmHxww9TrIoEMEqiVefGfJoAbO8gC2JfIT0hdEwobH9dUgpkAF5kbQDSKSA)

In a similar example, we may want to move the generic parameter to be a parameter of the whole interface. This lets us see what type(s) we’re generic over (e.g. `Dictionary<string>` rather than just `Dictionary`). This makes the type parameter visible to all the other members of the interface.  
在类似的示例中，我们可能希望将通用参数移动为整个接口的参数。这让我们可以看到我们通用的类型（例如 `Dictionary<string>` 而不仅仅是 `Dictionary` ）。这使得类型参数对接口的所有其他成员可见。

`   interface GenericIdentityFn<Type> {    (arg: Type): Type;  }  function identity<Type>(arg: Type): Type {    return arg;  }  let myIdentity: GenericIdentityFn<number> = identity;   `[Try](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgOIRNYCCSATDMYMATwDEQAeAFRIAcIA+ZAbwChlkAKOKAcwBcyWgwCUQkRADcbAL5s2MAK4gERAPYhkwAuGIka9Jj34Sj44UdYdkUCGCVQtvPjPlsANveQBbEvkJ9IXRMKGwAvVIKShAlHwAjaGYAXm1dIlIpIA)

Notice that our example has changed to be something slightly different. Instead of describing a generic function, we now have a non-generic function signature that is a part of a generic type. When we use `GenericIdentityFn`, we now will also need to specify the corresponding type argument (here: `number`), effectively locking in what the underlying call signature will use. Understanding when to put the type parameter directly on the call signature and when to put it on the interface itself will be helpful in describing what aspects of a type are generic.  
请注意，我们的示例已更改为略有不同。我们现在不再描述泛型函数，而是作为泛型类型的一部分的非泛型函数签名。当我们使用 `GenericIdentityFn` 时，我们现在还需要指定相应的类型参数（此处： `number` ），从而有效锁定底层调用签名将使用的内容。了解何时将类型参数直接放在调用签名上以及何时将其放在接口本身上将有助于描述类型的哪些方面是通用的。

In addition to generic interfaces, we can also create generic classes. Note that it is not possible to create generic enums and namespaces.  
除了泛型接口之外，我们还可以创建泛型类。请注意，无法创建通用枚举和命名空间。

## [](https://www.typescriptlang.org/docs/handbook/2/generics.html#generic-classes)Generic Classes 通用类

A generic class has a similar shape to a generic interface. Generic classes have a generic type parameter list in angle brackets (`<>`) following the name of the class.  
泛型类与泛型接口具有相似的形状。泛型类在类名称后面的尖括号 ( `<>` ) 中具有泛型类型参数列表。

`   class GenericNumber<NumType> {    zeroValue: NumType;    add: (x: NumType, y: NumType) => NumType;  }  let myGenericNumber = new GenericNumber<number>();  myGenericNumber.zeroValue = 0;  myGenericNumber.add = function (x, y) {    return x + y;  };   `[Try](https://www.typescriptlang.org/play/#code/PTAEAEGcBcCcEsDG0BcoBmBDANpApgFCLaaSSgDieAdngogHICuAtgEZ0A8zLAKgJ4AHPAD5QAbwKhQALzoB7AGo4meNDwHCA3FNCYAJvrQAKAB7rWmvABpQ-C3yF4AlKAC8YjU50BfAgWw8aFAWfipaeh4OWHdQWgB3Sho6JCiualZokWNnHVDwlMZMugA6OVglFTxYgAY8sOTI4tgSg31Y9CZqZHh5alAzW35XSWlYIKZYftNQAGo7Xy0gA)

This is a pretty literal use of the `GenericNumber` class, but you may have noticed that nothing is restricting it to only use the `number` type. We could have instead used `string` or even more complex objects.  
这是 `GenericNumber` 类的字面用法，但您可能已经注意到没有什么限制它只能使用 `number` 类型。我们可以使用 `string` 或更复杂的对象。

`   let stringNumeric = new GenericNumber<string>();  stringNumeric.zeroValue = "";  stringNumeric.add = function (x, y) {    return x + y;  };  console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));   `[Try](https://www.typescriptlang.org/play/#code/PTAEAEGcBcCcEsDG0BcoBmBDANpApgFCLaaSSgDieAdngogHICuAtgEZ0A8zLAKgJ4AHPAD5QAbwKhQALzoB7AGo4meNDwHCA3FNCYAJvrQAKAB7rWmvABpQ-C3yF4AlKAC8YjU50BfAiFAAWmDEJmhgwIJsPGhQGARqAHMeOiR3UFoAd0oaVMZWDlhOePgkkWNnHRKklPoAOjlYJRU8dIAiNqq4UuTWPLqDfXT0JmpkeHlqUDNbfldJaVgYplgp01AAajtfHSJJyHlouux5RONq3pZ+wfPumr76xubsVVs26DwYNudKoA)

Just as with interface, putting the type parameter on the class itself lets us make sure all of the properties of the class are working with the same type.  
就像接口一样，将类型参数放在类本身上可以让我们确保类的所有属性都使用相同的类型。

As we cover in [our section on classes](https://www.typescriptlang.org/docs/handbook/2/classes.html), a class has two sides to its type: the static side and the instance side. Generic classes are only generic over their instance side rather than their static side, so when working with classes, static members can not use the class’s type parameter.  
正如我们在类部分中介绍的那样，类的类型有两个方面：静态方面和实例方面。泛型类仅在其实例端而不是其静态端是通用的，因此在使用类时，静态成员不能使用类的类型参数。

## [](https://www.typescriptlang.org/docs/handbook/2/generics.html#generic-constraints)Generic Constraints 通用约束

If you remember from an earlier example, you may sometimes want to write a generic function that works on a set of types where you have *some* knowledge about what capabilities that set of types will have. In our `loggingIdentity` example, we wanted to be able to access the `.length` property of `arg`, but the compiler could not prove that every type had a `.length` property, so it warns us that we can’t make this assumption.  
如果您还记得前面的示例，您有时可能想要编写一个适用于一组类型的通用函数，其中您对该组类型将具有哪些功能有一定的了解。在我们的 `loggingIdentity` 示例中，我们希望能够访问 `arg` 的 `.length` 属性，但编译器无法证明每种类型都有 `.length` 属性，所以它警告我们不能做出这个假设。

`   function loggingIdentity<Type>(arg: Type): Type {    console.log(arg.length);  Property 'length' does not exist on type 'Type'.Property 'length' does not exist on type 'Type'.    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/PTAEAEFMCdoe2gZwFygEwGYME4BQAzAVwDsBjAFwEs5jQAbOAc0cuMYEkATSYq8gTwA8AFX4AHSAD4AFAENojVKIkBKJeMigA3rlChSNRHDqQAdA0ZyF5no3IALFQG5doaJHKFoteYxcBfIA)

Instead of working with any and all types, we’d like to constrain this function to work with any and all types that *also*  have the `.length` property. As long as the type has this member, we’ll allow it, but it’s required to have at least this member. To do so, we must list our requirement as a constraint on what `Type` can be.  
我们不想使用任何和所有类型，而是希望限制此函数使用也具有 `.length` 属性的任何和所有类型。只要类型有这个成员，我们就允许，但要求至少有这个成员。为此，我们必须将我们的要求列为对 `Type` 的限制。

To do so, we’ll create an interface that describes our constraint. Here, we’ll create an interface that has a single `.length` property and then we’ll use this interface and the `extends` keyword to denote our constraint:  
为此，我们将创建一个描述约束的接口。在这里，我们将创建一个具有单个 `.length` 属性的接口，然后使用该接口和 `extends` 关键字来表示我们的约束：

`   interface Lengthwise {    length: number;  }  function loggingIdentity<Type extends Lengthwise>(arg: Type): Type {    console.log(arg.length); // Now we know it has a .length property, so no more error    return arg;  }   `[Try](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgDIRAczACwO7ADOKA3gFDLIA2G2OAXMiAK4C2ARtANxkC+ZZGMxAIwwAPYhq4zJlCYAkgBMMYsAE8APABV1ABxQQAHpBBLCaWrgLEAfAAo4UTI10GAlK-2kKyBJMJxGgA6KhlHZ1CrHHcuZAB6eOQAOXE8ZDwUAGsQNORgMGQcOAs4ZCisXGQ9KHEDKA0AGmRApnFkVnEoQyhaqF9usGYoKSdMHl4gA)

Because the generic function is now constrained, it will no longer work over any and all types:  
由于泛型函数现在受到限制，因此它将不再适用于任何和所有类型：

`  loggingIdentity(3);  Argument of type 'number' is not assignable to parameter of type 'Lengthwise'.Argument of type 'number' is not assignable to parameter of type 'Lengthwise'.`[Try](https://www.typescriptlang.org/play/#code/PTAEAEFMCdoe2gZwFygEwGYAsBWAUAJYB2ALjAGYCGAxpKADKREDmJAFgO4GJ0DeeoUABsmrNqiIBXALYAjGAG48AXzx5ykotRIE4RYXGbNizAJIATJjpIBPADwAVGwAc6kAB5ki5xA1HsuHgA+AApKaGZUJ1cASiiXPgFQaj1EOBEAOiFDMIis-zYYpUFoSBJJaH1w5iVVEFAAWibqSRImhrxsoxMLKwJbEIwioA)

Instead, we need to pass in values whose type has all the required properties:  
相反，我们需要传入其类型具有所有必需属性的值：

`   loggingIdentity({ length: 10, value: 3 });   `[Try](https://www.typescriptlang.org/play/#code/JYOwLgpgTgZghgYwgAgDIRAczACwO7ADOKA3gFDLIA2G2OAXMiAK4C2ARtANxkC+ZZGMxAIwwAPYhq4zJlCYAkgBMMYsAE8APABV1ABxQQAHpBBLCaWrgLEAfAAo4UTI10GAlK-2kKyBJMJxGgA6KhlHZ1CrHHceSigIMGYoKSdMHn4AekzkAFp8hGYwfNyyMNl5ZVVgDXsSamjGAEYABgAaZAA3OCpmCEYAZmReWKA)

## [](https://www.typescriptlang.org/docs/handbook/2/generics.html#using-type-parameters-in-generic-constraints)Using Type Parameters in Generic Constraints

在通用约束中使用类型参数

You can declare a type parameter that is constrained by another type parameter. For example, here we’d like to get a property from an object given its name. We’d like to ensure that we’re not accidentally grabbing a property that does not exist on the `obj`, so we’ll place a constraint between the two types:  
您可以声明一个受另一个类型参数约束的类型参数。例如，在这里我们想从给定名称的对象中获取属性。我们希望确保我们不会意外获取 `obj` 上不存在的属性，因此我们将在两种类型之间放置约束：

`  function getProperty<Type, Key extends keyof Type>(obj: Type, key: Key) {    return obj[key];  }  let x = { a: 1, b: 2, c: 3, d: 4 };  getProperty(x, "a");  getProperty(x, "m");  Argument of type '"m"' is not assignable to parameter of type '"a" | "b" | "c" | "d"'.Argument of type '"m"' is not assignable to parameter of type '"a" | "b" | "c" | "d"'.`[Try](https://www.typescriptlang.org/play/#code/PTAEAEFMCdoe2gZwFygEwGYAsBWAUAGYCuAdgMYAuAlnCaAOaQUAK8ADjBQJ4A8AKlw4AaUAGlIXUJAAeFSCQAmiUAGsJcAqAEcAfAAo4AIwBWqbZBFquqcVwCUoAN55QoaEyLQ6R4wG0rALoA3HgAvnh4ADZMoNKgALxOoACGqACMIoaoaCJkqBgiCqhYoKEheIws7JxcetIiAETJDXYhlaxwHNDcdY0Ati1BQA)

## [](https://www.typescriptlang.org/docs/handbook/2/generics.html#using-class-types-in-generics)Using Class Types in Generics

在泛型中使用类类型

When creating factories in TypeScript using generics, it is necessary to refer to class types by their constructor functions. For example,  
当使用泛型在 TypeScript 中创建工厂时，需要通过类的构造函数来引用类类型。例如，

`   function create<Type>(c: { new (): Type }): Type {    return new c();  }   `[Try](https://www.typescriptlang.org/play/#code/GYVwdgxgLglg9mABBATgUwIZTQHgCoCeADmgHwAUEAXIgN6JhoDui5AlDYSYgL4eJc0dAFCJE6KCBRJGLCOwDcwnkA)

A more advanced example uses the prototype property to infer and constrain relationships between the constructor function and the instance side of class types.  
更高级的示例使用原型属性来推断和约束构造函数与类类型的实例端之间的关系。

`   class BeeKeeper {    hasMask: boolean = true;  }  class ZooKeeper {    nametag: string = "Mikle";  }  class Animal {    numLegs: number = 4;  }  class Bee extends Animal {    numLegs = 6;    keeper: BeeKeeper = new BeeKeeper();  }  class Lion extends Animal {    keeper: ZooKeeper = new ZooKeeper();  }  function createInstance<A extends Animal>(c: new () => A): A {    return new c();  }  createInstance(Lion).keeper.nametag;  createInstance(Bee).keeper.hasMask;   `[Try](https://www.typescriptlang.org/play/#code/PTAEAEGcBcCcEsDG0BcoBmBDANpApgFCLaaSSgBCeeA0tQA56ygDeBooAFqQLKkDWaAEYB7EdjyYAdqAC8oOAFc8AbgIBfAkRJlQALTF08jZmw5TMAWzzRMAczQwEUu3NAAiHvH4T3azdqk5ACCUvCWOKzsoFKKlgAyeHaQaLGWQkxuACz+WsRBlNSgeAAe0HhSACYhYRHYUeZxicluAGxqHPwMTGhUtN3M8lJ4AO6F-cZMABQAlLmBuvHwIjKl5VU14ZFmoF2TsGgGIkYmbsNjRyfTcxpa6IpSyMsyiLCS5QCSUjDSiHgAPMFimUKtVQKEttgAHxTRCpUagWZyKHgmZoIE7N7QRSwGTnUCIWbzV7vPBfH6PPBTJYrGYAOj2JjpFmstjsahJmE+31slKmfXpjKYdO4kD4kH4KiAA)

This pattern is used to power the [mixins](https://www.typescriptlang.org/docs/handbook/mixins.html) design pattern.  
该模式用于为 mixins 设计模式提供支持。

## [](https://www.typescriptlang.org/docs/handbook/2/generics.html#generic-parameter-defaults)Generic Parameter Defaults

通用参数默认值

By declaring a default for a generic type parameter, you make it optional to specify the corresponding type argument. For example, a function which creates a new `HTMLElement`. Calling the function with no arguments generates a `HTMLDivElement`; calling the function with an element as the first argument generates an element of the argument’s type. You can optionally pass a list of children as well. Previously you would have to define the function as:  
通过声明泛型类型参数的默认值，您可以选择指定相应的类型参数。例如，创建新 `HTMLElement` 的函数。调用不带参数的函数会生成 `HTMLDivElement` ；使用元素作为第一个参数调用函数会生成参数类型的元素。您也可以选择传递子项列表。以前您必须将函数定义为：

`   declare function create(): Container<HTMLDivElement, HTMLDivElement[]>;  declare function create<T extends HTMLElement>(element: T): Container<T, T[]>;  declare function create<T extends HTMLElement, U extends HTMLElement>(    element: T,    children: U[]  ): Container<T, U[]>;   `[Try](https://www.typescriptlang.org/play/#code/C4TwDgpgBAwg9gO2AQwJYIgJwDwBUA0UAqgHxQC8UA3gFBRQQA2EAthEgFxS4DcdUAYwAWqRgBNM7LkT4BfPjQD0iqAFp1AgK7B1qmmIgDGySVABmmhAOCpEgycmAQAFAEou8JGgw4AErgBZABkAEVQANwBRZjYkQn9gsKiY9mAAbQBdEj4DIxNoCysbOwEHJzwGAA8nBDEAZygEoOjWVJJnJlbObndYRBR0LDxCXEzs-UNjU0LrWwR7CEcICohq9nrGwOaUuOIqmo2mltjgdv5Ok64CfmFRCSliTJpezwGfYcesniA)

With generic parameter defaults we can reduce it to:  
使用通用参数默认值，我们可以将其简化为：

`   declare function create<T extends HTMLElement = HTMLDivElement, U = T[]>(    element?: T,    children?: U  ): Container<T, U>;  const div = create();          const div: Container<HTMLDivElement, HTMLDivElement[]>  const p = create(new HTMLParagraphElement());         const p: Container<HTMLParagraphElement, HTMLParagraphElement[]>   `[Try](https://www.typescriptlang.org/play/#code/C4TwDgpgBAwg9gO2AQwJYIgJwDwBUA0UAqgHxQC8UA3gFBRQQA2EAthEgFxS4DcdUAYwAWqRgBNM7LkT4BfPjQD0iqAFp1AgK7B1qmmIgDGySVABmmhAOCpEgycmAQ8DAB5OEYgM5QAErgBZABkAUWY2JAo-QKCAEVQANzDWdmBCIijcAG0AXRIACn4mFKQAfi4CfmFRCXZy4hoASi54JDQMHAJiEgUBRC9gKDFEqIEHJ3zGvmV6egA9UpoaPoQBqDBR8Yh8jAB3aOCABRNkAHNMZDAhZIjgSamlFVmFoA)

A generic parameter default follows the following rules:  
通用参数默认值遵循以下规则：

- A type parameter is deemed optional if it has a default.  
  如果类型参数有默认值，则该类型参数被视为可选。
- Required type parameters must not follow optional type parameters.  
  必需的类型参数不得位于可选类型参数之后。
- Default types for a type parameter must satisfy the constraint for the type parameter, if it exists.  
  类型参数的默认类型必须满足类型参数的约束（如果存在）。
- When specifying type arguments, you are only required to specify type arguments for the required type parameters. Unspecified type parameters will resolve to their default types.  
  指定类型实参时，只需为所需的类型参数指定类型实参。未指定的类型参数将解析为其默认类型。
- If a default type is specified and inference cannot choose a candidate, the default type is inferred.  
  如果指定了默认类型并且推理无法选择候选类型，则将推断默认类型。
- A class or interface declaration that merges with an existing class or interface declaration may introduce a default for an existing type parameter.  
  与现有类或接口声明合并的类或接口声明可能会引入现有类型参数的默认值。
- A class or interface declaration that merges with an existing class or interface declaration may introduce a new type parameter as long as it specifies a default.  
  与现有类或接口声明合并的类或接口声明可以引入新的类型参数，只要它指定默认值即可。

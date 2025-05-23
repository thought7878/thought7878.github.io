## Introduction

JavaScript是一种面向对象的原型语言，这意味着对象可以直接继承其他对象。这与Java等经典语言形成对比，在Java中，子类继承自超类，对象是类的实例。
JavaScript里的继承关系并不是`copy`而是 `link`，当你创建一个新的实例之后，它并不会复制父对象里的方法，而是`link`到它的原型对象，所有实例会共享
一个原型对象。在这篇文章里我将向你娓娓道来。

### 什么是prototype,\[\[prototype\]\]和constructor

我们经常看到`prototype`, `constructor`, `[[prototyope]]`, `__proto__`这样的东西，不要慌张，我们通过这个例子慢慢了解：

```javascript
function MyConstructor() {}
var myobject = new MyConstructor()
myobject.constructor == MyConstructor // true
```

第一行定义了一个函数（构造函数），这个函数会自带一个`prototype`属性指向一个原型对象，这个原型对象没有名字，我们就叫它`xx.prototype`，在这个例子中，
它叫`MyConstructor.prototyope`。`MyConstructor.prototyope`对象也自带一个`constructor`属性，指回`MyConstructor`构造函数。如下图：

[[ablog/media/d8af92a682e1dbab11238dfd0ca0b684_MD5.png|Open: Pasted image 20241113134516.png]]
![[ablog/media/d8af92a682e1dbab11238dfd0ca0b684_MD5.png]]

第二行当我们`new`了一个`MyConstructor`，它会做以下5件事情：

1. 创建一个新对象
2. 将这个对象内部的、不可访问的`[[prototype]]`设置为构造函数的`prototype`对象，还是看这张图。

> [!Caution] >[[Prototype & Inheritance]] 是对象内维护其对应原型对象的属性，但它不可直接被外界访问和修改；**proto** 是浏览器厂商实现的访问和修改对象内部属性 [[Prototype & Inheritance]] 的访问器属性(getter/setter)，不规范，现多用ECMAScript 定义的 Object.getPrototypeOf 和 Object.setPrototypeOf 代替。

3. 它将this变量指向新创建的对象。
4. 执行构造函数，每当提到this时，使用新创建的对象作为this。
5. 返回新创建的对象，如果构造函数显式返回非null的对象引用（例如返回`{a: 'haha'}`），那就返回这个对象引用。

第三行当我们读取`myobject.constructor`时，实际上在`myobject`自己身上是没有`constructor`属性的，它会沿着原型链找到
`MyConstructor.prototype`，在它身上找到`constructor`然后指向MyConstructor，所以最后一行`myobject.constructor == MyConstructor`结果是`true`。

### 原型链

```javascript
ObjMaker = function () {
  this.a = 'first'
}
ObjMaker.prototype.b = 'second'

obj1 = new ObjMaker()

obj1.a // 'first'
obj1.b // 'second'
```

我们的构造函数`ObjMaker`的`prototype`(`ObjMaker.prototype`)上有一个`b`属性，其值为`second`

当我们尝试访问`obj1`上的`a`属性，没有找到，就会检查`obj1`的`[[prototype]]`属性所指的对象，如果再没有找到，就会继续检查
当前找到的这个对象的`[[prototyope]]`，直到指向`null`。如下图：

[[ablog/media/4e1e099729a3579494904af9565ae362_MD5.png|Open: Pasted image 20241113134601.png]]
![[ablog/media/4e1e099729a3579494904af9565ae362_MD5.png]]

### Object.create()

我们如果想创建一个对象，很简单，可以

```javascript
var obj = {}
```

或者`Object`构造函数。

```javascript
var obj = Object()
```

但这些方法都不允许我们指定所创建对象的原型，通过使用`Object.create()`我们获得了
这种能力。我们能够创建一个对象，并从另一个对象那快速地继承一些东西。例如：

我们有一个对象`a`

```javascript
var a = {
  someFunction: function () {},
}
```

我想要创建一个对象`b`，它能够继承`a`的`someFunction`，就可以使用`Object.create`

```javascript
b = Object.create(a)
b.someOtherFunction = function () {}
```

### new和Object.create()的区别

我们已经在上文介绍过`new`在背后做的5件事。

```javascript
const Honda = new Car()
const Maruti = Object.create(Car.prototype)
```

对比`new`和`Object.create`，它们似乎都能创建一个新对象，区别在于，你可以在`Object.create`里指定对象的原型，而
`new`会把原型默认指向`Car.prototype`。当我们不想要任何原型的时候，可以通过`Object.create(null)`把
原型指向`null`。

它们本质的区别可以看作是`Pseudoclassical Inheritance`伪经典遗传和`Prototypal Inheritance`原型继承的区别，JavaScript内部
使用的`prototypal Inheritance`，`pseudoclassical`例如`class`和`new`只是一些语法糖。

### Pseudoclassical Inheritance （伪经典遗传）

伪经典继承模式使用“构造函数”和“new”操作符来创建对象，并使用“prototype”属性来构建继承链。构造函数被赋予一个“prototype”属性;这个属性被所有实例继承。

下面的代码创建一个构造函数（伪类）“Point”，然后使用“new”运算符创建一个实例。它还向“Point”的“prototype”属性添加了一个方法;该方法在实例中继承。

```javascript
function Point(x, y) {
  this.x = x || 0
  this.y = y || 0
}
Point.prototype.add = function () {
  return this.x + this.y
}
var p = new Point(3, 4)
console.log(p instanceof Point) // true
console.log(p.add()) // 7
```

当我们调用`new Point(3,4)`的时候，本质上我们在做以下事情：

1. 创建一个新对象，它继承自Point.prototype。
2. 在新对象的上下文中调用Point（）。这将创建新对象的属性并将其重新命名。

> [!Caution]
> 按照惯例，我们通过将第一个字母大写来命名构造函数。构造函数应始终使用new运算符调用。如果我们调用一个没有new操作符的构造函数，“this”指针将指向全局对象窗口，这是没有意义的。注意，JavaScript本身并没有任何构造函数的语法区别;让函数成为构造函数只是程序员的意图。

### Prototypal Inheritance 原型继承

在原型继承中，我们直接从现有对象创建一个新对象，没有任何类的概念。这里我们使用“Object.create”来创建一个新对象;它接受一个参数对象，该对象将成为新对象的原型。

```javascript
var point = {
  x: 0,
  y: 0,
  add: function () {
    return this.x + this.y
  },
}
var p = Object.create(point)
p.x = 3
p.y = 4
console.log(p.add()) // 7

var point_3d = Object.create(point)
point_3d.z = 0
point_3d.add_3d = function () {
  return this.x + this.y + this.z
}
var q = Object.create(point_3d)
q.x = 3
q.y = 4
q.z = 5
console.log(q.add()) // 7
console.log(q.add_3d()) // 12
```

### 实现new和Object.create

1. `Object.create`
   其本质就是以下代码：

```javascript
Object.create = function (o) {
  function F() {}
  F.prototype = o
  return new F()
}
```

它也可以接收第二个可选参数`propertiesObject`，这个传入的对象的可枚举的自有属性(own properties)将会被
加到你正在创建的这个对象属性中。

```javascript
Object.create = function (o, propertiesObj) {
  function F() {}
  F.prototyope = o
  if (propertiesObj) {
    Object.defineProperties(F, propertiesObj)
  }
  return new F()
}
```

2. `new`

```javascript
function myNew(constructor, ...args) {
  // 创建空对象
  const newObj = {}
  // [[prototype]]指向传入的构造函数的原型
  newObj.__proto__ = constructor.prototyope
  // 跑一下这个构造函数，修改this为newObj
  let result = constructor.apply(newObj, args)
  // 若结果为非null的object，则使用这个结果，否则返回newObj
  return result instanceof Object ? result : newObj
}
```

## isPrototypeOf和getPrototypeOf

1. isPrototypeOf
   > The isPrototypeOf() method of Object instances checks if this object exists in another object's prototype chain.
   > Object实例们的`isPrototypeOf`方法检查此对象是否存在于另一个对象的原型链中。

```javascript
function Foo() {}
function Bar() {}

Bar.prototype = Object.create(Foo.prototype)

const bar = new Bar()

console.log(Foo.prototype.isPrototypeOf(bar))
// Expected output: true，因为bar.__proto__.__proto__ = Foo.prototype
console.log(Bar.prototype.isPrototypeOf(bar))
// Expected output: true  因为bar.__proto__ = Bar.prototype
```

2. getPrototypeOf
   > The Object.getPrototypeOf() static method returns the prototype (i.e. the value of the internal [[Prototype & Inheritance]] property) of the specified object.

```javascript
const prototype1 = {}
const object1 = Object.create(prototype1) //指定了[[prototype]]的值

console.log(Object.getPrototypeOf(object1) === prototype1)
// Expected output: true
```

## 参考资料

- [1] https://stackoverflow.com/questions/1646698/what-is-the-new-keyword-in-javascript
- [2] https://zeekat.nl/articles/constructors-considered-mildly-confusing.html
- [3] http://theoryapp.com/javascript-inheritance-pseudoclassical-vs-prototypal/
- [4] https://stackoverflow.com/questions/4166616/understanding-the-difference-between-object-create-and-new-somefunction
- [5] https://github.com/jtwang7/JavaScript-Note/issues/52
- [6] https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create
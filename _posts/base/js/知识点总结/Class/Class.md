在 JavaScript 中，`Class` 是 ES6（ES2015）引入的一个新特性，**它为创建对象和实现继承提供了一种更清晰、更简洁的语法糖**，_本质上仍然基于原型继承_。下面从定义、构造函数、方法、继承等方面详细介绍 JavaScript 的 `Class`。

### 类的定义

使用 `class` 关键字来定义一个类，类名通常采用*大写字母开头的驼峰命名法*。

```javascript
class Person {
  // 类的主体
}
```

### 构造函数（`constructor`）

- **作用**：`constructor` 是类的一个*特殊方法*，_用于创建和初始化类的实例_。每个类只能有一个 `constructor` 方法，如果没有显式定义，JavaScript _会默认提供一个空的 `constructor` 方法_。
- **示例**：

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
}

// 创建类的实例
const person1 = new Person("John", 30);
console.log(person1.name);
console.log(person1.age);
```

### 类的方法

类中可以定义各种方法，**这些方法会被添加到类的原型上**。方法可以分为*实例方法*和*静态方法*。

#### 实例方法

- **作用**：`实例方法`是*定义在类的原型上的方法*，通过类的实例来调用。
- **示例**：

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // 实例方法
  introduce() {
    console.log(`My name is ${this.name} and I am ${this.age} years old.`);
  }
}

const person2 = new Person("Jane", 25);
person2.introduce();
```

#### 静态方法

- **作用**：`静态方法`是使用 `static` 关键字定义的方法，_它属于类本身，而不是类的实例_。可以直接通过类名来调用，不需要创建类的实例。
- **示例**：

```javascript
class MathUtils {
  static add(a, b) {
    return a + b;
  }
}

const result = MathUtils.add(3, 5);
console.log(result);
```

### 类的属性

在 JavaScript 的 `Class` 里定义属性，有多种方式，下面分别从传统方式、类字段声明、私有属性等方面为你详细介绍：

#### 1. 构造函数中定义实例属性

这是较为传统且常见的方式，在 `constructor` 方法里借助 `this` 关键字来定义实例属性，每个实例都拥有自己独立的属性副本。

```javascript
class Person {
  constructor(name, age) {
    // 定义实例属性 name 和 age
    this.name = name;
    this.age = age;
  }
}

const person1 = new Person("John", 30);
console.log(person1.name);
console.log(person1.age);

const person2 = new Person("Jane", 25);
console.log(person2.name);
console.log(person2.age);
```

#### 2. 类字段声明（Class Fields）

*这是 ES2022 引入的新特性*，允许在类的主体中直接定义**实例属性**，无需在构造函数中初始化。这种方式让代码更简洁，且属性的定义位置更清晰。

```javascript
class Rectangle {
  // 定义实例属性 width 和 height
  width = 0;
  height = 0;

  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

const rectangle = new Rectangle(5, 10);
console.log(rectangle.getArea());
```

#### 3. 静态属性

静态属性*属于类本身，而非类的实例*，通过 `static` 关键字来定义。可以直接使用类名访问静态属性。

```javascript
class Car {
  // 定义静态属性 numberOfCars
  static numberOfCars = 0;

  constructor() {
    // 每次创建新的 Car 实例时，静态属性 numberOfCars 加 1
    Car.numberOfCars++;
  }
}

const car1 = new Car();
const car2 = new Car();
console.log(Car.numberOfCars);
```

#### 4. 私有属性

从 ES2022 开始，JavaScript 支持定义私有属性，使用 `#` 符号作为属性名的前缀。私有属性只能在类的内部访问，外部无法直接访问。

```javascript
class BankAccount {
  // 定义私有属性 #balance
  #balance = 0;

  constructor(initialBalance) {
    this.#balance = initialBalance;
  }

  deposit(amount) {
    if (amount > 0) {
      this.#balance += amount;
    }
  }

  getBalance() {
    return this.#balance;
  }
}

const account = new BankAccount(1000);
account.deposit(500);
console.log(account.getBalance());

// 下面这行代码会报错，因为无法直接访问私有属性
// console.log(account.#balance);
```

#### 5. 使用 Getter 和 Setter 管理属性

Getter 和 Setter 方法能让你在获取或设置属性值时执行额外的逻辑，同时还能控制属性的访问权限。

```javascript
class Temperature {
  constructor(celsius) {
    this._celsius = celsius;
  }

  // Getter 方法，用于获取华氏温度
  get fahrenheit() {
    return (this._celsius * 9) / 5 + 32;
  }

  // Setter 方法，用于通过华氏温度设置摄氏温度
  set fahrenheit(value) {
    this._celsius = ((value - 32) * 5) / 9;
  }

  // 直接访问摄氏温度的 Getter 方法
  get celsius() {
    return this._celsius;
  }
}

const temp = new Temperature(25);
console.log(temp.fahrenheit);

temp.fahrenheit = 86;
console.log(temp.celsius);
```


### 类的继承

使用 `extends` 关键字实现类的继承，_子类可以继承父类的属性和方法，并且可以重写父类的方法或添加新的方法_。

```javascript
// 父类
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

// 子类
class Dog extends Animal {
  constructor(name) {
    // 调用父类的构造函数
    super(name);
  }

  // 重写父类的方法
  speak() {
    console.log(`${this.name} barks.`);
  }
}

const dog = new Dog("Buddy");
dog.speak();
```

### Getter 和 Setter

- **作用**：Getter 和 Setter 方法允许你定义类的属性访问器，通过它们可以在获取或设置属性值时执行额外的逻辑。
- **示例**：

```javascript
class Circle {
  constructor(radius) {
    this._radius = radius;
  }

  // Getter 方法
  get area() {
    return Math.PI * this._radius * this._radius;
  }

  // Setter 方法
  set radius(newRadius) {
    if (newRadius < 0) {
      throw new Error("Radius cannot be negative.");
    }
    this._radius = newRadius;
  }
}

const circle = new Circle(5);
console.log(circle.area);

circle.radius = 10;
console.log(circle.area);
```

### 注意事项

- **`class` 是语法糖**：JavaScript 的 `class` _本质上是基于原型的继承，只是提供了更简洁的语法_。
- **严格模式**：类中的代码会自动在严格模式下运行，这有助于捕获一些常见的编程错误。
- **`super` 关键字**：在子类的构造函数中，_必须先调用 `super()` 方法，才能使用 `this` 关键字，因为 `super()` 会调用父类的构造函数来初始化 `this`。_


在 JavaScript 中，`继承`是*面向对象编程的重要特性之一*。通过继承，子类可以**复用父类的属性和方法**。

以下是几种常见的继承方式及其优缺点：

---

## 1. 原型链继承

### 原理
通过*将子类的原型指向父类的实例*，实现继承。

### 实现
```javascript
function Parent() {
  this.name = "Parent";
}

Parent.prototype.greet = function () {
  console.log(`Hello, I am ${this.name}`);
};

function Child() {
  this.age = 25;
}

// 子类的原型指向父类的实例
Child.prototype = new Parent();

const child = new Child();
child.greet(); // 输出: Hello, I am Parent
console.log(child.age); // 输出: 25
```

### 优点
- 简单直观。
- 子类可以*通过原型链访问父类的方法和属性*。

### 缺点
- **引用类型共享问题**：父类实例的属性会被所有子类实例共享，可能导致意外修改。
- **无法向父类传递参数**：无法通过子类构造函数向父类构造函数传递参数。

---

## 2. 构造函数继承

### 原理
通过*在子类构造函数中调用父类构造函数*（使用 `call` 或 `apply`），实现继承。

### 实现
```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.greet = function () {
  console.log(`Hello, I am ${this.name}`);
};

function Child(name, age) {
  Parent.call(this, name); // 调用父类构造函数
  this.age = age;
}

const child = new Child("Alice", 25);
console.log(child.name); // 输出: Alice
console.log(child.age);  // 输出: 25
```

### 优点
- **解决引用类型共享问题**：每个子类实例都有自己的属性。
- **支持向父类传递参数**：可以在子类构造函数中动态传递参数。

### 缺点
- **无法继承父类原型上的方法**：只能继承父类实例的属性，无法继承父类原型上的方法。

---

## 3. 组合继承

### 原理
*结合原型链继承和构造函数继承*，既继承父类实例的属性，又继承父类原型上的方法。

### 实现
```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.greet = function () {
  console.log(`Hello, I am ${this.name}`);
};

function Child(name, age) {
  Parent.call(this, name); // 调用父类构造函数
  this.age = age;
}

// 继承父类原型
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

const child = new Child("Alice", 25);
child.greet(); // 输出: Hello, I am Alice
console.log(child.age); // 输出: 25
```

### 优点
- **解决引用类型共享问题**：每个子类实例都有自己的属性。
- **支持继承父类原型上的方法**：子类可以访问父类原型上的方法。

### 缺点
- **父类构造函数被调用了两次**：
  - 第一次在 `Parent.call(this, name)`。
  - 第二次在 `Child.prototype = Object.create(Parent.prototype)`。

---

## 4. 寄生组合继承

### 原理
*优化组合继承，避免父类构造函数被调用两次，通过直接操作原型链实现继承*。

### 实现
```javascript
function Parent(name) {
  this.name = name;
}

Parent.prototype.greet = function () {
  console.log(`Hello, I am ${this.name}`);
};

function Child(name, age) {
  Parent.call(this, name); // 调用父类构造函数
  this.age = age;
}

// 寄生组合继承的核心：直接继承父类原型
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;

const child = new Child("Alice", 25);
child.greet(); // 输出: Hello, I am Alice
console.log(child.age); // 输出: 25
```

### 优点
- **高效**：避免了父类构造函数被调用两次。
- **功能完整**：既继承了父类实例的属性，也继承了父类原型上的方法。

### 缺点
- 实现稍显复杂。

---

## 5. Class 继承

### 原理
ES6 引入了 `class` 和 `extends` 关键字，提供了一种*更简洁*、现代的方式来*实现继承*。

### 实现
```javascript
class Parent {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, I am ${this.name}`);
  }
}

class Child extends Parent {
  constructor(name, age) {
    super(name); // 调用父类构造函数
    this.age = age;
  }

  introduce() {
    console.log(`I am ${this.name}, and I am ${this.age} years old.`);
  }
}

const child = new Child("Alice", 25);
child.greet();      // 输出: Hello, I am Alice
child.introduce();  // 输出: I am Alice, and I am 25 years old.
```

### 优点
- **语法简洁**：比传统继承方式更易读、易维护。
- **内置支持**：*支持 `super` 关键字，方便调用父类方法*。
- **符合现代标准**：推荐用于 ES6 及更高版本的项目。

### 缺点
- **浏览器兼容性**：旧版浏览器可能不支持 `class`。

---

## 6. 总结对比

| **继承方式**       | **优点**                                                                 | **缺点**                                                                 |
|--------------------|------------------------------------------------------------------------|------------------------------------------------------------------------|
| **原型链继承**     | 简单直观，子类可以通过原型链访问父类的方法和属性。                                         | 引用类型共享问题，无法向父类传递参数。                                                |
| **构造函数继承**   | 解决引用类型共享问题，支持向父类传递参数。                                                 | 无法继承父类原型上的方法。                                                       |
| **组合继承**       | 解决引用类型共享问题，支持继承父类原型上的方法。                                              | 父类构造函数被调用了两次，效率较低。                                                  |
| **寄生组合继承**   | 高效，避免父类构造函数被调用两次，同时支持继承父类实例属性和原型方法。                                    | 实现稍显复杂，但仍然是最推荐的传统继承方式。                                           |
| **Class 继承**     | 语法简洁，内置支持 `super`，符合现代标准，推荐用于 ES6 及更高版本的项目。                             | 需要浏览器支持 ES6+，不适合老旧环境。                                             |

---

## 7. 推荐选择

- 如果使用 ES6+，推荐使用 **Class 继承**，它语法简洁且功能强大。
- 如果需要兼容旧版浏览器，推荐使用 **寄生组合继承**，它是传统继承方式中最优的选择。
- 在简单场景下，可以选择 **原型链继承** 或 **构造函数继承**，但需要注意它们的局限性。
### TypeScript 中的 `class` 详解

TypeScript 的 `class` *在 JavaScript 类的基础上*，增加了**类型注解**、**访问修饰符**等特性，使得面向对象编程（OOP）更安全、更易维护。以下是 TypeScript 类的核心概念和用法。

---

### 一、TypeScript 类的基础特性

#### 1. **基本语法**  
与 JavaScript 类语法*相似，但支持类型注解：*
```typescript
class Person {
  name: string;  // 声明属性类型
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  greet(): void {  // 方法返回值类型
    console.log(`Hello, I'm ${this.name}`);
  }
}

const alice = new Person("Alice", 30);
alice.greet();  // 输出: "Hello, I'm Alice"
```

#### 2. **访问修饰符**  
TypeScript 新增三种访问控制修饰符：
- **`public`**（默认）：公开属性，任意位置可访问。
- **`private`**：私有属性，仅类内部可访问。
- **`protected`**：受保护属性，类内部及子类可访问。
```typescript
class Animal {
  public name: string;        // 公开属性
  private secret: string;     // 私有属性（仅类内访问）
  protected age: number;      // 受保护属性（子类可访问）

  constructor(name: string, secret: string, age: number) {
    this.name = name;
    this.secret = secret;
    this.age = age;
  }
}

class Dog extends Animal {
  bark() {
    console.log(this.age);    // ✅ 允许访问 protected 属性
    console.log(this.secret); // ❌ 错误：私有属性不可访问
  }
}

const dog = new Dog("Buddy", "password", 3);
console.log(dog.name);        // ✅ 允许访问 public 属性
console.log(dog.secret);      // ❌ 错误：私有属性不可访问
```

#### 3. **只读属性 (`readonly`)**  
初始化后不可修改：
```typescript
class Config {
  readonly apiKey: string;

  constructor(apiKey: string) {
    this.apiKey = apiKey;
  }
}

const config = new Config("123456");
config.apiKey = "newKey";  // ❌ 错误：不可修改
```

#### 4. **静态成员 (`static`)**  
属于类本身而非实例：
```typescript
class MathUtils {
  static PI: number = 3.14159;  // 静态属性

  static circleArea(radius: number): number {  // 静态方法
    return this.PI * radius ** 2;
  }
}

console.log(MathUtils.circleArea(5));  // 78.53975
```

---

### 二、TypeScript 类的进阶特性

#### 1. **抽象类 (`abstract`)**  
定义未实现的抽象方法，强制子类实现：
```typescript
abstract class Shape {
  abstract area(): number;  // 抽象方法（无实现）

  printArea(): void {
    console.log("Area:", this.area());
  }
}

class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }

  area(): number {  // 必须实现抽象方法
    return Math.PI * this.radius ** 2;
  }
}

const circle = new Circle(5);
circle.printArea();  // 输出: "Area: 78.53981633974483"
```

#### 2. **实现接口 (`implements`)**  
类可实现多个接口，确保符合特定结构：
```typescript
interface Loggable {
  log(message: string): void;
}

interface Serializable {
  serialize(): string;
}

class AppLogger implements Loggable, Serializable {
  log(message: string) {
    console.log(message);
  }

  serialize() {
    return JSON.stringify(this);
  }
}
```

#### 3. **属性参数简写**  
在构造函数参数中直接声明属性：
```typescript
class User {
  constructor(
    public id: number,     // 自动声明为 public 属性
    private name: string   // 自动声明为 private 属性
  ) {}
}

const user = new User(1, "Alice");
console.log(user.id);   // 1
console.log(user.name); // ❌ 错误：私有属性不可访问
```

#### 4. **Getter/Setter**  
控制属性的访问逻辑：
```typescript
class Temperature {
  private _celsius: number = 0;

  get celsius(): number {
    return this._celsius;
  }

  set celsius(value: number) {
    if (value < -273.15) throw new Error("Invalid temperature");
    this._celsius = value;
  }

  get fahrenheit(): number {
    return this._celsius * 1.8 + 32;
  }
}

const temp = new Temperature();
temp.celsius = 25;
console.log(temp.fahrenheit);  // 77
```

---

### 三、类与接口的关系

#### 1. **类作为接口**  
类定义会同时创建两种类型：
- **实例类型**：类的实例结构（如 `Person` 类型）。
- **构造函数类型**：类的构造函数类型（如 `typeof Person`）。
```typescript
class Point {
  x: number;
  y: number;
}

// 使用类作为实例类型
const p1: Point = { x: 1, y: 2 };

// 使用类的构造函数类型
function createPoint(ctor: typeof Point, x: number, y: number): Point {
  return new ctor(x, y);
}
```

#### 2. **类实现接口**  
通过 `implements` 确保类符合接口规范：
```typescript
interface Movable {
  move(dx: number, dy: number): void;
}

class Circle implements Movable {
  constructor(public x: number, public y: number) {}

  move(dx: number, dy: number) {
    this.x += dx;
    this.y += dy;
  }
}
```

---

### 四、继承与多态

#### 1. **继承 (`extends`)**  
子类继承父类属性和方法：
```typescript
class Animal {
  constructor(public name: string) {}

  move(distance: number = 0) {
    console.log(`${this.name} moved ${distance}m`);
  }
}

class Snake extends Animal {
  constructor(name: string) {
    super(name);  // 必须调用 super()
  }

  override move(distance = 5) {  // TS 4.3+ 支持 override 关键字
    console.log("Slithering...");
    super.move(distance);
  }
}

const snake = new Snake("Python");
snake.move();  // 输出: "Slithering... Python moved 5m"
```

#### 2. **多态**  
子类可重写父类方法，实现不同行为：
```typescript
class Shape {
  area(): number {
    return 0;
  }
}

class Square extends Shape {
  constructor(private side: number) {
    super();
  }
  // 
  override area(): number {
    return this.side ** 2;
  }
}

class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }
  // 
  override area(): number {
    return Math.PI * this.radius ** 2;
  }
}

const shapes: Shape[] = [new Square(5), new Circle(3)];
shapes.forEach(shape => console.log(shape.area()));  // 25 和 28.2743...
```

---

### 五、实际应用示例

#### 1. **实现一个简单的状态管理类**
```typescript
type Listener<T> = (state: T) => void;

class StateManager<T> {
  private state: T;
  private listeners: Listener<T>[] = [];

  constructor(initialState: T) {
    this.state = initialState;
  }

  getState(): T {
    return this.state;
  }

  setState(newState: T): void {
    this.state = newState;
    this.listeners.forEach(listener => listener(this.state));
  }

  subscribe(listener: Listener<T>): void {
    this.listeners.push(listener);
  }
}

// 使用示例
const store = new StateManager({ count: 0 });
store.subscribe(state => console.log("State updated:", state));
store.setState({ count: 1 });  // 输出: "State updated: { count: 1 }"
```

#### 2. **结合接口和类实现策略模式**
```typescript
interface PaymentStrategy {
  pay(amount: number): void;
}

class CreditCardPayment implements PaymentStrategy {
  pay(amount: number) {
    console.log(`Paid $${amount} via Credit Card`);
  }
}

class PayPalPayment implements PaymentStrategy {
  pay(amount: number) {
    console.log(`Paid $${amount} via PayPal`);
  }
}

class ShoppingCart {
  constructor(private paymentStrategy: PaymentStrategy) {}

  checkout(amount: number) {
    this.paymentStrategy.pay(amount);
  }
}

// 使用示例
const cart1 = new ShoppingCart(new CreditCardPayment());
cart1.checkout(100);  // 输出: "Paid $100 via Credit Card"

const cart2 = new ShoppingCart(new PayPalPayment());
cart2.checkout(50);   // 输出: "Paid $50 via PayPal"
```

---

### 六、总结

#### TypeScript 类的核心优势：
1. **类型安全**：通过类型注解和访问控制减少运行时错误。
2. **面向对象支持**：完整的继承、封装、多态特性。
3. **与现代框架集成**：完美适配 Angular、NestJS 等强类型框架。
4. **可维护性**：清晰的类结构提升代码可读性。

#### 何时使用类？
- 需要封装复杂逻辑和状态。
- 实现继承或多态行为。
- 与 OOP 设计模式结合（如工厂、策略模式）。
- 开发大型应用时管理组件或服务。
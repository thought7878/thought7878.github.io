`设计模式（Design Patterns）`是**在特定上下文中解决*常见软件设计问题*的*可复用方案***。它们不是代码片段，而是***经过验证的最佳实践模板***，帮助开发者写出**更灵活、可维护、可扩展**的代码。

在前端开发中，合理使用设计模式**能显著提升架构质量**，尤其在复杂交互、状态管理、组件通信等场景中。

下面系统介绍**前端最常用的设计模式**，结合 JavaScript/TypeScript 和现代框架（React/Vue）给出实战示例。

---

## 一、创建型模式（Creational Patterns）  
> *关注**对象的创建机制**，**解耦**对象创建与使用（例如，改变时，**只需修改工厂函数，不需修改业务代码**）*。

### 1. 工厂模式（Factory Pattern）
- **问题**：直接 `new` 对象***导致代码紧耦合***（改变时，***需修改业务代码***），难以扩展。
- **方案**：用工厂函数/类**封装对象创建逻辑**（改变时，***只需修改工厂函数，不需修改业务代码***）。

```ts
// 场景：创建不同类型的图表组件
interface Chart {
  render(): void;
}

class BarChart implements Chart {
  render() { console.log('Rendering Bar Chart'); }
}

class LineChart implements Chart {
  render() { console.log('Rendering Line Chart'); }
}

// 工厂函数
function createChart(type: 'bar' | 'line'): Chart {
  switch (type) {
    case 'bar': return new BarChart();
    case 'line': return new LineChart();
    default: throw new Error('Unknown chart type');
  }
}

// 使用
const chart = createChart('bar');
chart.render();
```

✅ **前端应用**：
- 动态创建组件（如根据配置生成表单控件）
- *封装第三方库实例*（如不同环境的 Logger）

---

### 2. 单例模式（Singleton Pattern）
- **问题**：确保一个类**只有一个实例**，并*提供全局访问点*。
- **注意**：前端需谨慎使用（*避免全局污染*），但在特定场景有用。

```ts
class AppState {
  private static instance: AppState;
  private data: any = {};

  private constructor() {} // 禁止外部 new

  static getInstance(): AppState {
    if (!AppState.instance) {
      AppState.instance = new AppState();
    }
    return AppState.instance;
  }

  setData(key: string, value: any) {
    this.data[key] = value;
  }
}

// 全局唯一状态
const appState = AppState.getInstance();
```

⚠️ **替代方案**：现代前端*更推荐使用 **ES 模块单例**（模块天然单例）* 或 **状态管理库（如 Zustand）**。

```js
// utils/state.js
const state = { user: null };
export const getState = () => state;
export const setUser = (user) => { state.user = user; };
```

---

## 二、结构型模式（Structural Patterns）  
> 关注**类或对象的组合**，构建更灵活的结构。

### 3. 装饰器模式（Decorator Pattern）
- **问题**：***动态**地给对象**添加职责、功能***，避免子类爆炸。
- **前端天然支持**：JavaScript 的高阶函数、React 的高阶组件（HOC）、Vue 的 mixin（已不推荐）。

```jsx
// React HOC 示例：添加 loading 状态
function withLoading(WrappedComponent) {
  return function WithLoading(props) {
    const [loading, setLoading] = useState(false);
    return (
      <WrappedComponent {...props} loading={loading} setLoading={setLoading} />
    );
  };
}

const MyButton = withLoading(({ loading, setLoading }) => (
  <button onClick={() => setLoading(true)} disabled={loading}>
    {loading ? 'Loading...' : 'Click me'}
  </button>
));
```

✅ **现代替代**：React Hooks（`useLoading`）更轻量：
```js
function useLoading() {
  const [loading, setLoading] = useState(false);
  return { loading, setLoading };
}
```

---

### 4. 适配器模式（Adapter Pattern）
- **问题**：***让不兼容的接口协同工作***。
- **前端典型场景**：封装第三方库 API，统一调用方式。

```ts
// 第三方库 A 的 API
class OldAnalytics {
  trackEvent(eventName: string) { /* ... */ }
}

// 第三方库 B 的 API
class NewAnalytics {
  log(event: { name: string }) { /* ... */ }
}

// 适配器：统一为新接口
class AnalyticsAdapter {
  private analytics: NewAnalytics;
  constructor() {
    this.analytics = new NewAnalytics();
  }
  trackEvent(eventName: string) {
    this.analytics.log({ name: eventName }); // 适配调用
  }
}

// 业务代码只依赖适配器
const tracker = new AnalyticsAdapter();
tracker.trackEvent('button_click');
```

✅ **优势**：未来切换分析平台***只需改适配器，业务代码零修改***。

---

## 三、行为型模式（Behavioral Patterns）  
> 关注**对象之间的职责分配和通信**。

### 5. **观察者模式（Observer Pattern）**
- **问题**：对象状态变化时，自动通知依赖它的对象。
- **前端基石**：Vue/React 响应式系统、事件总线、RxJS 的核心。

```js
// 简易实现
class EventEmitter {
  events = {};
  on(event, callback) {
    if (!this.events[event]) this.events[event] = [];
    this.events[event].push(callback);
  }
  emit(event, data) {
    (this.events[event] || []).forEach(cb => cb(data));
  }
}

// 使用
const bus = new EventEmitter();
bus.on('user:login', (user) => console.log('Welcome', user.name));
bus.emit('user:login', { name: 'Alice' });
```

✅ **现代替代**：
- React：Context + useReducer / Zustand
- Vue：`watch` / `computed` / Pinia
- 通用：RxJS（`Subject`）

---

### 6. **策略模式（Strategy Pattern）**
- **问题**：在运行时选择算法或行为。
- **前端高频场景**：表单验证、支付方式、主题切换。

```ts
// 表单验证策略
interface ValidationStrategy {
  validate(value: string): boolean;
}

class EmailValidator implements ValidationStrategy {
  validate(value) {
    return /\S+@\S+\.\S+/.test(value);
  }
}

class PasswordValidator implements ValidationStrategy {
  validate(value) {
    return value.length >= 8;
  }
}

class FormValidator {
  constructor(private strategy: ValidationStrategy) {}
  validate(value: string) {
    return this.strategy.validate(value);
  }
}

// 使用
const emailValidator = new FormValidator(new EmailValidator());
emailValidator.validate('test@example.com'); // true
```

✅ **函数式简化**（更符合前端习惯）：
```js
const validators = {
  email: (v) => /\S+@\S+\.\S+/.test(v),
  password: (v) => v.length >= 8
};

const validate = (type, value) => validators[type](value);
```

---

### 7. **命令模式（Command Pattern）**
- **问题**：将请求封装为对象，支持撤销、重做、队列。
- **前端场景**：编辑器操作（如富文本）、撤销/重做功能。

```js
interface Command {
  execute(): void;
  undo(): void;
}

class AddTextCommand implements Command {
  constructor(private editor, private text) {}
  execute() {
    this.editor.addText(this.text);
  }
  undo() {
    this.editor.removeLastText();
  }
}

// 使用
const history = [];
const cmd = new AddTextCommand(editor, 'Hello');
cmd.execute();
history.push(cmd); // 支持 undo
```

---

### 8. **状态模式（State Pattern）**
- **问题**：对象行为随内部状态改变而改变。
- **前端场景**：复杂 UI 状态机（如订单状态、播放器状态）。

```ts
abstract class PlayerState {
  protected player: AudioPlayer;
  constructor(player: AudioPlayer) { this.player = player; }
  abstract clickPlay(): void;
  abstract clickLock(): void;
}

class PlayingState extends PlayerState {
  clickPlay() { this.player.pause(); }
  clickLock() { this.player.changeState(new LockedState(this.player)); }
}

class AudioPlayer {
  private state: PlayerState;
  constructor() {
    this.state = new ReadyState(this);
  }
  changeState(state: PlayerState) { this.state = state; }
  clickPlay() { this.state.clickPlay(); }
}
```

✅ **现代替代**：使用 **XState**（基于状态机的声明式状态管理库）。

---

## 四、前端特别推荐的模式

### 9. **复合模式（Composite Pattern）**
- **问题**：以树形结构处理“部分-整体”层次。
- **前端体现**：**组件树本身就是复合模式**！
  ```jsx
  <App>
    <Header />
    <Main>
      <Sidebar />
      <Content />
    </Main>
    <Footer />
  </App>
  ```

### 10. **模块模式（Module Pattern）**
- **问题**：组织代码，避免全局污染。
- **现代实现**：ES Modules（`import`/`export`）天然支持。

```js
// mathUtils.js
const privateVar = 'secret';
export const add = (a, b) => a + b;
export const PI = 3.14159;
```

---

## 五、如何选择设计模式？

| 场景                     | 推荐模式               |
|--------------------------|-----------------------|
| 动态创建对象             | 工厂模式              |
| 全局唯一状态             | 单例（谨慎）/ 模块    |
| 扩展组件功能             | 装饰器 / Hooks        |
| 统一第三方 API           | 适配器                |
| 事件通知                 | 观察者 / 发布-订阅    |
| 多种算法切换             | 策略模式              |
| 撤销/重做                | 命令模式              |
| 复杂状态流转             | 状态模式 / XState     |
| 树形 UI 结构             | 复合模式              |

---

## 六、重要提醒

1. **不要为了用模式而用模式**：先有问题，再找模式。
2. **现代前端框架已内置许多模式**：
   - React 的 Hooks ≈ 装饰器 + 策略
   - Vue 的响应式系统 ≈ 观察者
   - 组件化 ≈ 复合模式
3. **函数式风格可简化传统 OOP 模式**：如用高阶函数替代策略类。

---

## 总结

> 设计模式是**经验的结晶，不是银弹**。  
> 优秀的前端开发者应：
> - **理解模式背后的意图**
> - **结合现代框架特性灵活应用**
> - **优先选择简单、可读的方案**

掌握这些模式，你不仅能写出更健壮的代码，还能在技术方案讨论中精准表达设计思想。

是否需要我针对某个具体场景（如“实现一个可撤销的画布操作”或“设计插件化表单系统”）给出完整设计模式实现？
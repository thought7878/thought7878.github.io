### 深入 Qiankun 微前端框架：核心知识点与实战指南

Qiankun（乾坤）是**蚂蚁金服开源的微前端解决方案**，基于 Single-SPA 封装，解决了微前端落地中最棘手的 **JS 沙箱隔离、样式隔离、通信机制** 问题。它已成为国内企业级微前端的**首选方案**（尤其适合 React/Vue 技术栈）。下面我将用 **「核心概念→关键机制→实战技巧→避坑指南」** 的结构，帮你高效掌握 Qiankun。

---

## 一、Qiankun 核心定位与设计思想
### ✅ 为什么选 Qiankun？（vs 其他方案）
| 对比项          | Qiankun                          | Single-SPA                     | Webpack Module Federation      |
|-----------------|----------------------------------|--------------------------------|--------------------------------|
| **技术定位**     | **生产级解决方案**（开箱即用）   | 基础框架（需自行实现沙箱等）   | 构建层方案（依赖 Webpack 5）   |
| **隔离能力**     | ✅ 内置沙箱 + 样式隔离           | ❌ 需手动实现                   | ⚠️ 依赖框架自身隔离（较弱）    |
| **子应用类型**   | 支持任意框架（React/Vue/Angular）| 支持任意框架                   | 通常限同技术栈                 |
| **学习成本**     | 中等（封装了复杂逻辑）           | 高（需理解底层机制）           | 低（但功能有限）               |
| **适用场景**     | **企业级复杂系统**               | 技术能力强的团队               | 技术栈统一的轻量级场景         |

> 💡 **关键结论**：  
> - 若你需要**快速落地生产环境** → **选 Qiankun**  
> - 若团队技术深度强且需完全控制底层 → 选 Single-SPA + 自研沙箱

---

## 二、Qiankun 核心知识点（附代码示例）
### 🔰 第一层：基础架构与工作流程
#### 1. 两大核心角色
| 角色         | 职责                                                                 | 配置示例                                                                 |
|--------------|----------------------------------------------------------------------|--------------------------------------------------------------------------|
| **主应用**   | - 负责路由分发、加载子应用<br>- 提供全局通信/状态管理                  | ```js // main.js registerMicroApps([{ name: 'app1', entry: '//localhost:7100', container: '#container' }]); start(); ``` |
| **子应用**   | - 独立开发部署的业务模块<br>- 需导出生命周期钩子（`bootstrap/mount/unmount`） | ```js // app1.js export async function bootstrap() { ... } export async function mount(props) { ReactDOM.render(<App />, props.container) } ``` |

#### 2. 生命周期钩子（子应用必须实现）
```javascript
// 子应用入口文件（关键！）
export async function bootstrap() {
  console.log('子应用初始化');
}

export async function mount(props) {
  console.log('子应用挂载', props); // props 包含主应用传递的数据
  ReactDOM.render(<App />, props.container); // 必须挂载到主应用提供的 container
}

export async function unmount() {
  ReactDOM.unmountComponentAtNode(document.getElementById('container'));
}
```
> ⚠️ **致命错误**：子应用未导出 `mount` 钩子 → 主应用无法加载！  
> ✅ **最佳实践**：用 `loadMicroApp` 动态加载时，确保子应用导出标准生命周期

---

### ⚙️ 第二层：核心机制深度解析（Qiankun 灵魂所在）
#### 1. JS 沙箱隔离 —— 防止全局变量污染
| 沙箱类型       | 原理                                                                 | 适用场景                     |
|----------------|----------------------------------------------------------------------|------------------------------|
| **SnapshotSandbox** | 快照沙箱：应用卸载时对比全局对象变化，还原修改                         | 不支持 Proxy 的旧浏览器      |
| **LegacySandbox**  | 代理沙箱：用 `Proxy` 劫持 `window` 对象（现代浏览器）                 | **默认方案**（性能更好）     |
| **StrictSandbox**  | 严格沙箱：`iframe + Proxy` 双重隔离（实验性）                        | 高安全需求场景               |

```javascript
// 主应用启动时配置沙箱（默认已开启）
start({
  sandbox: {
    strictStyleIsolation: true, // 开启影子DOM样式隔离
    experimentalStyleIsolation: true // 开启 scoped 样式隔离
  }
});
```

#### 2. 样式隔离 —— 解决 CSS 冲突
| 方案                          | 实现方式                                                                 | 效果                             |
|-------------------------------|--------------------------------------------------------------------------|----------------------------------|
| **strictStyleIsolation**      | 为子应用包裹 `<div style="display: contents">` + **Shadow DOM**          | ✅ 完全隔离（但子应用内弹窗可能出界） |
| **experimentalStyleIsolation**| 为所有样式自动添加 **[data-qiankun=子应用名]** 属性选择器                | ✅ 轻量级隔离（推荐）            |

```css
/* 开启 experimentalStyleIsolation 后，子应用样式自动编译为： */
[data-qiankun="app1"] .btn { color: red; }
```

#### 3. 应用间通信 —— 数据共享方案
| 方案                | 代码示例                                                                 | 适用场景                         |
|---------------------|--------------------------------------------------------------------------|----------------------------------|
| **主应用下发 props** | ```js // 主应用 registerMicroApps([{ props: { user: { id: 1 } } }]); ``` | 父子应用通信（单向）             |
| **globalState**     | ```js // 主应用 initGlobalState({ user: {} }); // 子应用 onGlobalStateChange((state) => { ... }) ``` | **跨应用双向通信**（推荐）       |
| **CustomEvent**     | ```js // 子应用A window.dispatchEvent(new CustomEvent('user-login', { detail: user })); ``` | 简单场景（需手动解耦）           |

> ✨ **黄金法则**：  
> - 优先使用 **`initGlobalState`**（Qiankun 内置方案，自动清理监听器）  
> - 避免直接修改对方状态（用事件驱动）

---

### 🛠 第三层：关键配置与实战技巧
#### 1. 主应用配置详解
```javascript
// main.js
import { registerMicroApps, start, initGlobalState } from 'qiankun';

// 1. 初始化全局状态（通信基石）
const { onGlobalStateChange, setGlobalState } = initGlobalState({
  user: null,
  token: localStorage.getItem('token')
});

// 2. 监听全局状态变化（主应用可响应）
onGlobalStateChange((state, prev) => {
  console.log('主应用收到状态变更:', state);
});

// 3. 注册子应用
registerMicroApps(
  [
    {
      name: 'app1',
      entry: '//localhost:7100', // 子应用地址
      container: '#container',   // 挂载点
      activeRule: '/app1',       // 路由匹配规则
      props: { globalState: { onGlobalStateChange, setGlobalState } } // 传递通信能力
    },
    {
      name: 'app2',
      entry: { scripts: ['//cdn.com/app2.js'] }, // 动态加载资源
      loader: (loading) => { /* 自定义加载状态 */ }
    }
  ],
  {
    beforeLoad: [async (app) => { /* 加载前 */ }],
    afterMount: [async (app) => { /* 挂载后 */ }]
  }
);

// 4. 启动微前端
start({
  prefetch: true, // 预加载子应用（路由切换前加载）
  sandbox: { strictStyleIsolation: true } // 强制样式隔离
});
```

#### 2. 子应用改造要点（适配 Qiankun）
```javascript
// webpack.config.js（关键！）
module.exports = {
  output: {
    library: `${process.env.VUE_APP_NAME}-[name]`, // 必须设置
    libraryTarget: 'umd', // 必须 umd
    jsonpFunction: `webpackJsonp_${process.env.VUE_APP_NAME}`
  }
};

// main.js（入口文件）
let instance = null;

function render(props = {}) {
  const { container } = props;
  instance = new Vue({
    render: h => h(App),
  }).$mount(container ? container.querySelector('#app') : '#app');
}

// 1. 判断是否在微前端环境
if (!window.__POWERED_BY_QIANKUN__) {
  render(); // 独立运行
}

// 2. 必须导出生命周期
export async function bootstrap() {}
export async function mount(props) {
  render(props);
}
export async function unmount() {
  instance.$destroy();
}
```
> ⚠️ **高频踩坑点**：  
> - 子应用端口冲突：开发时用 **不同端口**（如 app1:7100, app2:7101）  
> - 跨域问题：子应用需配置 `Access-Control-Allow-Origin: *`  
> - 路由冲突：子应用必须用 **Hash 路由**（避免与主应用 History 路由冲突）

---

## 三、Qiankun 高阶实战技巧
### 🔥 解决 90% 生产环境问题的方案
#### 1. 预加载优化首屏性能
```javascript
// 主应用启动时预加载
start({
  prefetch: (apps) => 
    apps.filter(app => app.name !== 'heavy-app') // 排除重型应用
});
```
> ✨ 效果：子应用在**空闲时预加载**，路由切换时秒开

#### 2. 子应用独立访问方案
```javascript
// 子应用入口文件
if (window.__POWERED_BY_QIANKUN__) {
  // 微前端环境：延迟挂载
  __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
} else {
  // 独立运行：立即挂载
  render();
}
```
> ✅ 价值：子应用可单独开发测试，无需启动主应用

#### 3. 共享依赖减少体积
```javascript
// 主应用 webpack 配置
module.exports = {
  externals: {
    react: 'React', // 告诉 webpack 不打包 react
    'react-dom': 'ReactDOM'
  }
};

// 子应用 index.html
<script src="//cdn.com/react/17.0.0/react.development.js"></script>
```
> ✨ 效果：避免多个子应用重复加载 React，**包体积减少 30%+**

---

## 四、避坑指南：Qiankun 常见问题解决方案
### 🚫 高频问题清单（附解决方案）
| 问题现象                          | 根本原因                     | 解决方案                                                                 |
|-----------------------------------|------------------------------|--------------------------------------------------------------------------|
| **子应用白屏**                    | 样式隔离导致子应用容器高度为0 | ```css [data-qiankun] { height: 100%; } ```                              |
| **子应用路由失效**                | 未使用 Hash 路由             | 子应用路由配置：`new VueRouter({ mode: 'hash' })`                        |
| **JS 报错：Cannot redefine property** | 多次加载相同库（如 Vue）     | 主应用 externals 声明 + 子应用移除重复依赖                                |
| **子应用卸载后全局事件未清除**    | 未在 unmount 中解绑事件      | ```js export async function unmount() { window.removeEventListener(...); } ``` |
| **IE11 兼容问题**                 | Qiankun 默认不支持 IE        | 1. 主应用引入 `core-js`<br>2. 子应用配置 `libraryTarget: 'var'`          |

### ⚠️ 重大限制（必须知道！）
1. **不支持 IE 浏览器**（除非用 LegacySandbox + polyfill）  
2. **子应用间跳转需走主应用路由**（不能直接 `window.location.href`）  
3. **子应用不能使用 `document.write`**（沙箱会拦截）  
4. **主应用需用 Vue2/React16+**（对旧框架支持弱）

---

## 五、学习路径建议（高效掌握 Qiankun）
### 📚 分阶段实战计划
| 阶段         | 目标                          | 具体行动                                                                 |
|--------------|-------------------------------|--------------------------------------------------------------------------|
| **Day 1-2**  | 跑通基础 demo                 | 1. 用 [qiankun 官方脚手架](https://qiankun.umijs.org/zh/guide/tutorial) 创建主应用+React/Vue 子应用<br>2. 实现子应用通信 |
| **Day 3-4**  | 解决核心问题                  | 1. 配置样式隔离（对比 strict/experimental 效果）<br>2. 模拟 JS 污染（验证沙箱有效性） |
| **Day 5-7**  | 模拟生产环境                  | 1. 配置子应用独立访问<br>2. 实现依赖共享（React/Vue）<br>3. 添加预加载策略 |
| **Week 2**   | 集成到真实项目                | 1. 用 Qiankun 替换旧系统的一个模块<br>2. 建立团队规范（通信协议/部署流程） |

### 🔗 必看资源清单
- **官方文档**：[Qiankun 中文文档](https://qiankun.umijs.org/zh/guide)（重点看「实践」章节）
- **源码解析**：
  - [沙箱机制源码](https://github.com/umijs/qiankun/blob/master/src/sandbox/strictSandbox.ts)
  - [样式隔离实现](https://github.com/umijs/qiankun/blob/master/src/sandbox/patchers/dynamicAppend.ts)
- **实战项目**：
  - [Qiankun + Vue3 模板](https://github.com/kuitos/qiankun-vue3-demo)
  - [React + Qiankun 多应用集成](https://github.com/Canfoo/qiankun-react-demo)

---

## 六、Qiankun vs Module Federation 如何选？
| 场景                          | 推荐方案       | 原因                                                                 |
|-------------------------------|----------------|----------------------------------------------------------------------|
| 团队使用 **Webpack 5+**       | Module Federation | 配置简单，天然支持依赖共享                                           |
| 需要 **IE 兼容**              | Qiankun        | MF 不支持 IE，Qiankun 可通过 LegacySandbox 兼容                      |
| **多框架混合**（React+Vue）   | Qiankun        | MF 通常限同技术栈，Qiankun 无此限制                                  |
| **轻量级应用**                | MF             | 无需额外框架，Webpack 原生支持                                       |
| **强隔离需求**                | Qiankun        | 内置沙箱和样式隔离，MF 需自行实现                                    |

> 💡 **终极建议**：  
> - **新项目**：优先尝试 **Module Federation**（更现代化）  
> - **企业级系统/旧项目改造**：**Qiankun 是更稳妥的选择**

---

## 最后：关键总结
1. **Qiankun 的核心价值**：  
   ✅ 用 **沙箱隔离** 解决 JS 冲突  
   ✅ 用 **scoped 样式** 解决 CSS 冲突  
   ✅ 用 **globalState** 实现标准化通信  

2. **必须掌握的 3 个动作**：  
   🔹 主应用：`registerMicroApps` + `start`  
   🔹 子应用：导出 `bootstrap/mount/unmount`  
   🔹 通信：`initGlobalState` + `onGlobalStateChange`  

3. **避免失败的关键**：  
   ❌ 不要直接集成到复杂系统 → 先用 **Demo 验证核心问题**  
   ❌ 不要忽略 **子应用独立运行能力** → 影响开发体验  
   ✅ **先定义规范**：通信协议、部署流程、错误监控  

> 🌟 **行动建议**：  
> 1. 立刻用 `npm create qiankun@latest` 创建一个 **React 主应用 + Vue 子应用**  
> 2. 故意在子应用中 `window.a = 1`，验证沙箱是否还原  
> 3. 在子应用中写 `.btn { color: red }`，检查是否自动添加 `[data-qiankun]` 前缀  

**遇到问题？** 请提供：  
- 你的主/子应用技术栈  
- 具体报错信息（截图更佳）  
- 你尝试过的解决方案  
我可以帮你精准定位问题！ 😄
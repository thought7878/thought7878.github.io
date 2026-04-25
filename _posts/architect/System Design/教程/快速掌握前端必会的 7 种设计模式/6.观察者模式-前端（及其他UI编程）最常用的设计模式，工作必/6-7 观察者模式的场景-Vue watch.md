本视频讲述了 Vue 3 中 watch 的两种 API（Options API 与 Composition API）的完整使用方法，涵盖基础监听、immediate 立即触发、deep 深度监听等核心配置，并通过对比演示阐明引用类型与值类型的监听差异；同时简要解析了 Vue 组件更新过程中观察者模式（Watcher）的底层机制。


### Options API 中 watch 的基础用法 
[01:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=60)

- **组件结构与事件绑定 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=0)**  
    创建 `watch demo1.vue` 组件，模板中使用 `<div>`（非错误的 `tempter`），内含按钮，点击触发 `changeName` 方法。
- **数据变更逻辑 [00:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=21)**  
    `changeName` 将 `this.name` 赋值为 `"双月"` 拼接时间戳（如 `"双月_1712345678900"`）。
- **基础 watch 监听 [01:04](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=64)**  
    在 `watch` 选项中定义 `name(newVal, oldVal)`，参数分别为新值与旧值。
- **控制台输出验证 [01:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=75)**  
    `console.log('name', newVal, oldVal)`，仅在 `name` 值发生变更时触发（初始化不执行）。

### immediate 配置：初始化立即触发 
[02:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=148)

- **对象形式 watch 配置 [02:10](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=130)**  
    `name: { handler(newVal, oldVal) { console.log('name', newVal, oldVal); }, immediate: true }`。
- **immediate 作用说明 [02:39](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=159)**  
    `immediate: true` 表示组件实例创建完成、数据初始化后**立刻执行一次 handler**，此时 `newVal` 为初始值（如 `"双月"`），`oldVal` 为 `undefined`（[03:25](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=205)中称“安内犯的是他老直”，实为 `oldVal` 未定义）。
- **对比验证 [31–37]**  
    移除 `immediate: true` 后，刷新页面控制台无输出；添加后刷新即打印初始值，体现初始化触发能力。

### deep 深度监听：应对引用类型 
[03:58](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=238)

- **info 对象定义 [45–46]**  
    `data()` 返回 `{ name: '双月', info: { city: '北京' } }`，其中 `info` 为响应式对象（引用类型）。
- **直接监听 info 失效 [51–52]**  
    `watch: { info(newVal, oldVal) { console.log('info', newVal, oldVal); } }` —— 修改 `this.info.city` **不会触发**（[62–63]）。
- **启用 deep 监听 [64–65]**  
    `info: { handler(newVal, oldVal) { console.log('info', newVal, oldVal); }, deep: true }`。
- **deep 语义 [06:12](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=372)**  
    `deep: true` 表示对对象内部所有嵌套属性进行递归监听（深度监听），使 `this.info.city = '上海'` 可触发回调（[06:20](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=380)）。

### Composition API 的 watch 基础用法 
[06:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=408)

- **环境准备 [76–80]**  
    升级 Vue 版本至 `3.2+`（支持 `<script setup>` 语法），确保 `@vue/runtime-dom` 兼容。
- **ref 声明与使用 [82–83]**  
    `import { ref } from 'vue'; const nameRef = ref('双月');`，模板中插值 `{{ nameRef }}`。
- **watch 监听 ref [08:45](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=525)**  
    `watch([nameRef], ([newVal], [oldVal]) => { console.log('name', newVal, oldVal); })`；第一个参数为**数组**（支持多 ref），第二个参数函数接收新旧值数组。
- **触发变更 [95–96]**  
    `changeName()` 执行 `nameRef.value = '双月_' + Date.now()`。

### Composition API 的 immediate 实现：watchEffect 
[11:53](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=713)

- **watchEffect 替代方案 [11:53](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=713)**  
    `import { watchEffect } from 'vue'; watchEffect(() => { console.log('name', nameRef.value); })`。
- **watchEffect 特性 [117–118]**  
    无需指定监听源，自动追踪内部响应式依赖；**定义即执行**（等效 `immediate: true`），后续值变更时再次执行（[120–122]）。

### Composition API 监听 reactive 对象 
[12:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=768)

- **reactive 声明 [125–126]**  
    `import { reactive } from 'vue'; const state = reactive({ info: { city: '北京' } });`。
- **watch 监听 reactive [14:40](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=880)**  
    `watch(() => state.info, (newVal, oldVal) => { console.log('city', newVal.city, oldVal?.city); }, { deep: true })`；第一个参数为**getter 函数**（非数组），返回需监听的响应式对象。
- **deep 必须显式声明 [15:06](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=906)**  
    `reactive` 对象默认**不开启深度监听**，必须传入 `{ deep: true }` 选项才能响应 `state.info.city`变更（[140–141]）。

### Vue 组件更新的观察者模式原理 
[17:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=1035)

- **Watcher 核心角色 [169–170]**  
    Vue 内部存在 `Watcher` 实例，负责监听 `data`（即响应式数据源）的变化。
- **更新流程图解 [175–176]**  
    `data` 变更 → `Watcher` 捕获 → 触发 `trigger re-render` → 重新渲染组件视图。
- **观察者模式体现 [179–180]**  
    整个过程是典型的观察者模式：`Watcher`（观察者）订阅 `data`（被观察者），`data` 状态变更时主动通知 `Watcher` 执行更新。
- **底层实现提示 [18:25](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=1105)**  
    `data` 的响应式基于 `Object.defineProperty`（Vue 2）或 `Proxy`（Vue 3）拦截 `getter/setter`，此细节将在代理模式章节展开。
本视频讲述了在Google Apps Script（JS）环境中实现单例模式的两种主要方法——闭包与模块化，并对比了其与TypeScript（TS）语法实现的差异，强调了JS单线程特性对单例安全性的天然保障，同时指出JS缺乏原生访问控制语法（如private/static）需借助语言机制迂回实现，最终确认该实现符合高内聚低耦合的设计原则。

### JS单例模式实现背景与前提 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=0)

- **TS语法限制的不可迁移性 [00:02](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=2)**  
    TS中`static`、`private`等语法在JS中缺失或需复杂工具链支持，无法直接复用。
- **JS环境配置要求 [00:29](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=29)**  
    需添加`// @ts-check`注释以启用TS类型检查，同时避免TS语法干扰JS运行。
- **核心依赖：闭包（Closure）基础 [00:39](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=39)**  
    闭包是JS实现单例的关键底层机制，其自由变量具有持久性，构成“常驻作用域”。

### 方法一：手写闭包实现单例 
[01:19](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=79)

- **函数定义与命名语义 [13–17]**  
    定义`getInstance`函数，内部声明自由变量`instance`与类`Singleton`；函数名`getInstance`明确表达“生成并返回单例实例”的语义。
- **闭包结构与单例逻辑 [18–24]**
    - `instance`为闭包自由变量，生命周期绑定于外层函数作用域。
    - 返回的函数体执行：若`instance === null`，则`instance = new Singleton()`；否则直接`return instance`。
- **使用方式与验证 [25–33]**
    - 调用`getInstance()`返回单例生成函数，赋值给`getInst`。
    - `s1 = getInst()`与`s2 = getInst()`两次调用，`s1 === s2`返回`true`，证明同一闭包作用域下实例唯一。

### 方法二：模块化替代闭包 
[03:41](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=221)

- **文件即模块的封装形式 [38–41]**  
    在`getInstance.JS`文件中：
    - 声明自由变量`instance`与类`Singleton`。
    - `exports.default`导出函数，逻辑同闭包：`if (instance === null) instance = new Singleton(); return instance;`。
- **模块化本质即闭包 [42–46]**
    - 模块文件经JS模块系统处理后，其顶层作用域自动形成函数作用域，`instance`成为模块级闭包变量。
    - 与手写闭包原理完全一致，仅封装载体由函数变为文件。
- **模块化优势与兼容性 [47–50]**
    - 更易读、更符合CommonJS/ES6模块规范，天然适配JS标准模块系统。
    - 若无模块化支持，则必须采用手写闭包方案，代码冗余度更高。

### 设计原则符合性分析 
[06:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=388)

- **高内聚低耦合体现 [60–63]**  
    所有单例创建逻辑（实例判断、初始化、返回）均封装于`getInstance`函数内部，外部仅通过函数调用获取实例，杜绝直接操作`instance`变量。
- **原则遵循边界说明 [64–67]**  
    单例模式本身不涉及多类协作，故无法体现“拆分”“提炼”等面向对象重构原则；但绝对不违背开放封闭原则（对扩展开放，对修改封闭），且严格满足封装性要求。

### JS单线程特性关键注意事项 
[07:41](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=461)

- **线程安全天然保障 [71–73]**  
    JS为单线程执行环境，不存在多线程并发创建单例的竞争条件。
- **对比Java多线程风险 [74–78]**  
    Java等语言中需显式加锁（如`synchronized`或双重检查锁定），否则线程A与B可能同时通过`instance == null`判断，各自创建实例，破坏单例唯一性（`$s1 \neq s2$`）。
- **JS实践结论 [79–80]**  
    无需额外同步机制，模块化或闭包方案在JS中均可安全、简洁地保证全局唯一性。
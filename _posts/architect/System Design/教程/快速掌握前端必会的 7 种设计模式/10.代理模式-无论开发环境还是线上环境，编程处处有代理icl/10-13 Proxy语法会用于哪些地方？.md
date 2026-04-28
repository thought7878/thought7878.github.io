本视频讲述了*JavaScript中Proxy的四个核心使用场景*：*跟踪属性访问（Vue3响应式原理）、隐藏属性、验证属性格式、记录类实例*。通过代码演示了如何利用get、set、has和construct实现数据监听、隐私保护、类型校验和自动化实例追踪，强调了Proxy在元编程中的强大能力。

![[_posts/architect/System Design/教程/快速掌握前端必会的 7 种设计模式/10.代理模式-无论开发环境还是线上环境，编程处处有代理icl/media/c081a3a16fb6d0a8c81e772da7eb1c09_MD5.webp]]

- **跟踪属性访问 [00:12](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=12)**
    
    - **Vue3响应式原理**  
        Vue3的数据响应式是通过Proxy实现的，与Vue2不同。
    - **代码实现**  
        创建一个`user`对象，使用`new Proxy`代理该对象。  
        在`get`陷阱中，打印日志并返回`Reflect.get(target, key)`。  
        在`set`陷阱中，打印日志并返回`Reflect.set(target, key, value)`。
    - **效果验证**  
        当获取`proxy.name`时，控制台输出“get”；当设置`proxy.name = '李四'`时，控制台输出“set”。  
        这表明Proxy能够监听对象属性的获取和设置操作，这是实现数据响应式的基础。
- **隐藏属性 [02:40](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=160)**
    
    - **需求描述**  
        希望隐藏对象中的特定属性（如`girlfriend`），使其无法被访问、检测或修改。
    - **定义隐藏列表**  
        定义一个数组`hiddenProps`，包含需要隐藏的键名，例如`['girlfriend']`。
    - **拦截get操作**  
        在`get`陷阱中，判断`key`是否在`hiddenProps`中。  
        如果是，返回`undefined`；否则，返回`Reflect.get(target, key)`。  
        注意：需处理`key`可能为`symbol`的情况，可使用`as string`或类型判断避免报错。
    - **拦截has操作**  
        在`has`陷阱中，判断`key`是否在`hiddenProps`中。  
        如果是，返回`false`；否则，返回`Reflect.has(target, key)`。  
        这导致使用`in`操作符检测隐藏属性时结果为`false`。
    - **拦截set操作**  
        在`set`陷阱中，判断`key`是否在`hiddenProps`中。  
        如果是，返回`false`（严格模式下可能抛出错误）；否则，执行`Reflect.set(target, key, value)`。
    - **效果验证**  
        访问`proxy.girlfriend`返回`undefined`。  
        尝试修改`proxy.girlfriend`会失败或报错。  
        使用`'girlfriend' in proxy`返回`false`。  
        从而实现了对属性的全方位隐藏。
- **验证属性格式 [08:47](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=527)**
    
    - **需求描述**  
        在设置属性值时，验证数据类型是否符合要求（例如年龄必须为数字）。
    - **代码实现**  
        在`set`陷阱中，检查`key`是否为`age`。  
        如果是，进一步检查`typeof value !== 'number'`。  
        若类型不符，直接返回`false`，阻止赋值操作。  
        若类型符合，执行`Reflect.set(target, key, value)`。
    - **应用场景**  
        在JavaScript动态类型语言中，这种运行时验证非常有用，可以防止非法数据写入。  
        相比之下，TypeScript在编译阶段进行静态类型检查，但Proxy提供的运行时防护可作为额外保障。
- **记录实例 [10:39](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=639)**
    
    - **需求描述**  
        自动记录某个类（Class）所有创建的实例，避免手动添加实例到集合的繁琐和遗漏。
    - **代码实现**  
        创建一个`WeakSet`（或`Set`）用于存储实例，命名为`userList`。  
        定义一个`User`类，包含构造函数。  
        使用`new Proxy(User, handler)`代理类本身，而非类的实例。  
        在`construct`陷阱中：
        1. 使用`Reflect.construct(target, args)`创建原始实例。
        2. 将创建的实例添加到`userList`中。
        3. 返回该实例。
    - **效果验证**  
        使用`new ProxyUser('张三')`和`new ProxyUser('李四')`创建实例。  
        检查`userList`，发现其中自动包含了这两个实例。  
        这种方式实现了实例创建的自动追踪，适用于需要管理对象生命周期的场景。
- **总结 [14:22](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=862)**
    
    - Proxy的四个主要应用场景回顾：
        1. 跟踪属性访问（用于响应式系统）。
        2. 隐藏属性（用于数据隐私和保护）。
        3. 验证属性格式（用于数据完整性校验）。
        4. 记录实例（用于自动化实例管理）。
    - 鼓励观众思考其他潜在的使用场景并进行讨论。
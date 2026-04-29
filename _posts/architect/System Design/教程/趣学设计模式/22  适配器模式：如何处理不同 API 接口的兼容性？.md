本视频讲述了适配器模式的核心定义、UML结构、代码实现原理、典型使用场景、三大选用原因、五大优点及主要缺点，并强调了其在接口兼容性问题中的应用边界与滥用风险，指出需警惕过度嵌套导致的耦合加剧与维护隐患。

![[_posts/architect/System Design/教程/趣学设计模式/media/3d6ba7080b380e806e857c38a92aab2e_MD5.webp]]

## 结构型模式
[00:01](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=1)

- 主题引入  
    本讲聚焦结构型设计模式中的适配器模式，用于解决不同API接口的兼容性问题。
- **设计模式分类对比**  
    `创建型模式`关注单个对象内部结构；***`结构型模式`关注多个对象之间的组合方式***。
- 结构型模式全集  
    共7种：适配器模式、桥接模式、组合模式、装饰模式、门面模式、享元模式、代理模式。
- 本讲重点  
    主要讲解最常用到的适配器模式，涉及UML图与代码实现。

## 适配器模式定义与核心原理 
[01:07](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=67)

- **原始定义**  
    *将类的接口转化为客户期望的另一个接口，使原本不兼容的类可以协同工作*。
- **关键点提炼**  
    *核心在于“转换”，且必须基于已有接口做好兼容，而非重构*。
- **UML图三角色** [01:30](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=90)
    - **目标类（Target）**：适配器即将适配的抽象类或接口。
    - **适配器类（Adapter）**：作为中间类，可为类或接口，集成目标类并实现新接口。
    - **具体适配者类（Adaptee）**：内部或外部的服务/对象，提供实际功能。
- **代码实现特征** [02:03](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=123)  
    `Adapter` 类继承目标抽象类（如 `TaskAbstract`）并实现接口（如 `Filter`），在接口方法中调用 `OtherClass` 实现扩展功能，从而在保留原有功能（如 `photo`）基础上新增能力。
- **封装的三个事实** [02:27](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=147)
    1. 具体适配者类可具有不同接口；
    2. 用户使用适配器时实际调用了多个接口；
    3. 适配器类与具体适配者类共同引入变化。
- **核心原理总结** [02:52](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=172)  
    在原有接口外层封装新适配器，实现对象结构的可扩展性，且该扩展可无限延伸。

![[_posts/architect/System Design/教程/趣学设计模式/media/4a8a1f4a623f58705881fb323ef63f13_MD5.webp]]

## 典型使用场景 
[03:08](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=188)

- **七大具体场景**
    - 原有接口无法修改；
    - 原有接口功能过旧；
    - 需统一多个类的接口设计；
    - 需过渡升级旧接口；
    - 需依赖外部系统；
    - 需适配不同数据格式；
    - 需进行不同接口协议转换。
- **两大归类总结** [03:30](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=210)
    - **第一类**：原有接口功能不满足需求，需在兼容老接口前提下扩展；
    - **第二类**：对多个具有相似性的不同接口进行功能统一。
- **现实类比** [03:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=228)
    - Type-C笔记本通过扩展坞转为HDMI/USB接口，连接外设；
    - 电源转换适配器将各国电流标准转换为设备适用标准。
- **框架与工程实践** [04:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=261)  
    各类库和框架中大量使用适配器模式。

## 代码实例解析：InputStreamReader适配器 
[04:32](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=272)

- **问题背景** [04:32](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=272)  
    命令行输入需用 `BufferedReader` 读取，但 `System.in` 提供的是 `InputStream` 类型，而 `BufferedReader` 构造函数要求 `Reader` 类型。
- **接口不匹配判定** [04:43](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=283)  
    `System.in` 的 `InputStream` 与 `BufferedReader` 所需 `Reader` 类型不符，属“原有接口无法修改”场景。
- **适配器角色** [04:51](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=291)  
    `InputStreamReader` 充当适配器，将 `InputStream` 转换为 `Reader`。
- **构造机制** [05:09](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=309)  
    `InputStreamReader` 构造函数内部将 `InputStream` 封装为可被 `BufferedReader` 读取的 `Reader` 流，完成适配。

## 选用的三大原因 
[05:34](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=334)

- **原因一：接口不可修改但需快速扩展** [05:34](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=334)  
    如已交付系统、跨团队公用接口等，适配器是“打补丁式”扩展首选；但需注意：新旧接口差异不宜过大（例：勿强行适配十年前软盘接口）。
- **原因二：复用外部组件构建新功能** [06:25](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=385)  
    如调用第三方NLP平台API替代自研模型，快速构建NLP组件；但存在外部依赖风险，属短期过渡方案。
- **原因三：多端协议与数据格式转换** [07:04](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=424)  
    如API网关对iOS、安卓、H5客户端的数据与通信协议进行双向适配，网关本身即为适配器。

## 五大优点 
[07:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=443)

- **优点一：解耦目标类与具体适配者** [07:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=448)  
    引入适配器后，重用原功能并扩展新功能，无需修改目标类代码。
- **优点二：增强类透明性** [07:47](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=467)  
    新增功能仅影响适配者类，客户端调用逻辑不受影响。
- **优点三：满足里氏替换原则** [08:01](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=481)  
    具体适配者通过适配器与目标类交互；只要目标接口功能不变，适配者可任意替换。
- **优点四：符合开闭原则** [08:19](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=499)  
    具体适配者为适配器子类或组合关系，目标类无修改。
- **优点五：统一多个类或接口** [08:31](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=511)  
    单个适配器可将多个不同适配者（子类或组合）适配至同一目标类；若目标类为新类，则间接实现多接口统一。

## 主要缺点 
[08:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=528)

- **限制一：单继承语言适配能力受限** [08:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=528)  
    Java、C#等不支持多重继承，一次最多适配一个适配者类。
- **限制二：目标类必须为抽象类或接口** [09:03](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=543)  
    不能为具体类，导致类文件与代码量增加。
- **缺点三：理解成本上升** [09:11](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=551)  
    适配类/接口过多时，代码理解难度增大。
- **缺点四：接口臃肿风险** [09:17](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=557)  
    过度嵌套适配器（如一个接口被适配20次）导致接口臃肿。
- **核心弊端：隐性风险累积** [09:20](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=560)  
    不断新增适配器而不修改原始接口，表面符合开闭原则，实则隐藏风险；一旦原始接口下线，整个适配链影响巨大。
- **缺点五：强耦合反演** [10:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=600)  
    目标接口依赖过多适配器时，修改目标接口将强制所有适配器同步定制修改，从解耦退化为定制化开发（例：上游商家接口变更致下游全部适配器重写）。

## 使用建议与总结 
[10:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=628)

- **设计建议** [10:50](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=650)  
    适配器内宜采用私有继承，限定接口功能范围。
- **嵌套约束** [10:58](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=658)  
    适配嵌套不超过三次；超限需重新设计接口。
- **学习方法论** [11:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=675)  
    学习结构型模式需建立大局观，从整体程序结构出发，避免过度纠结局部是否“严格满足”模式。
- **课后思考题** [11:36](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=923886033544463fac18a69af7b551ec#?seek_t=696)  
    当适配器内调用的外部接口抛出异常时，应选择透传、包装后重抛，还是内部消化？原因何在？
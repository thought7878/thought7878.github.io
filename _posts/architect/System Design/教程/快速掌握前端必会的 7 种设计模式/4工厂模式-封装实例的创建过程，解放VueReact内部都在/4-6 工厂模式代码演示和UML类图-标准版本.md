本视频讲述了标准工厂模式的代码实现、UML类图绘制及设计原则验证，重点演示了通过接口抽象（IProduct）解耦创建逻辑与具体产品类（Product1/2），体现依赖倒置原则（依赖接口而非具体类）和开闭原则（新增Product3等实现类无需修改Creator），并强调逻辑封装与代码复用价值。


- **标准工厂模式引入与结构概述 [00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=0)**
    
    - **核心结构对比** [00:04](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=4)  
        标准工厂模式在基础工厂模式上增加接口（IProduct）及多个实现类（Product1、Product2）。
    - **Creature类参数演进** [00:12](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=12)  
        Creature类构造参数由仅`name`扩展为`name`与`type`两个参数。
    - **Creator依赖关系定义** [00:18](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=18)  
        Creator类依赖于接口而非具体实现类。
    - **接口图示规范说明** [00:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=21)  
        接口（Interface）在UML中以`<<interface>>`标注，图形表示为带`<<interface>>`标签的矩形。
    - **IProduct接口定义与实现要求** [00:27](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=27)  
        IProduct接口包含属性`name`及方法`FN1`、`FN2`；Product1与Product2类必须完整实现该接口全部成员。
    - **实现接口的强制性约束** [00:36](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=36)  
        实现IProduct接口时，`name`、`FN1`、`FN2`均须提供具体实现。
    - **Creator返回类型设计依据** [00:44](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=44)  
        Creator方法返回类型必须为IProduct接口类型，而非具体Product1或Product2类类型。
    - **依赖倒置原则应用点明** [01:02](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=62)  
        此处明确体现面向对象五大设计原则中的依赖倒置原则（Dependency Inversion Principle）。
    - **代码实践强调** [01:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=83)  
        要求学习者必须动手编码，避免“眼高手低”。
- **IProduct接口定义与函数类型声明 [01:43](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=103)**
    
    - **接口成员声明** [01:47](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=107)  
        `IProduct`接口包含属性`name`及两个方法：`FN1`、`FN2`。
    - **函数类型本质说明** [02:03](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=123)  
        `FN1`、`FN2`为自定义函数类型，即指定函数签名而非具体实现。
    - **函数类型语法示例** [02:09](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=129)  
        函数类型定义为`FNType = () => string`（无参，返回`string`），`FN1`与`FN2`均采用此类型。
    - **类型声明禁用括号** [02:41](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=161)  
        声明`FN1: FNType`时不可写作`FN1: FNType()`。
    - **内联函数类型写法** [02:50](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=170)  
        可直接使用`() => string`替代单独定义的`FNType`名称，效果等价。
- **Product1与Product2类实现IProduct接口 [03:07](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=187)**
    
    - **Product1类定义** [03:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=195)  
        `class Product1 implements IProduct`，含`constructor(name: string)`，`name`属性赋值，`FN1`与`FN2`方法体可任意实现（如`console.log('Product1.FN1')`）。
    - **Product2类快速派生** [03:53](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=233)  
        通过复制Product1代码并仅修改类名为`Product2`完成定义，其余结构完全一致。
    - **双实现类目的重申** [04:04](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=244)  
        Product1与Product2均实现IProduct接口，构成平行产品族基础。
- **Creator类与create工厂方法实现 [04:13](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=253)**
    
    - **create方法签名** [04:20](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=260)  
        `create(type: string, name: string): IProduct`。
    - **参数与返回类型约束** [04:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=263)[04:31](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=271)[04:38](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=278)[04:41](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=281)  
        方法接收`type`与`name`参数，返回类型严格为`IProduct`接口。
    - **type分支逻辑实现** [04:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=282)[04:57](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=297)[05:07](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=307)
        
        typescript
        
        ```typescript
        if (type === 'P1') return new Product1(name);
        else if (type === 'P2') return new Product2(name);
        else throw new Error('Invalid type');
        ```
        
    - **工厂逻辑封装价值阐释**
        将实例化判断逻辑集中于`create`方法内，实现业务逻辑复用，避免各调用方重复编写相同判断。
    - **依赖倒置原则再强调**
        返回`IProduct`接口类型使Creator不依赖具体Product类；新增Product3（实现IProduct）时，`create`方法签名与调用方代码均无需修改，体现“面向抽象编程”与高扩展性。
- **工厂模式测试用例编写 [07:57](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=477)**
    
    - **Creator实例化** [08:07](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=487)  
        `const creator = new Creator();`
    - **P1与P2实例创建** [08:09](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=489)[08:14](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=494)  
        `creator.create('P1', 'name1');`  
        `creator.create('P2', 'name2');`
    - **同类型多实例创建** [08:39](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=519)[08:49](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=529)[08:56](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=536)  
        `creator.create('P1', 'nameE1');`  
        `creator.create('P2', 'name21');`
- **UML类图绘制步骤与规范 [10:05](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=605)**
    
    - **Creator类框图** [10:05](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=605)[10:13](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=613)[10:17](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=617)[10:19](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=619)  
        类名`Creator`，含方法`create(type: string, name: string): IProduct`。
    - **IProduct接口框图** [10:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=628)[10:32](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=632)[10:36](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=636)[10:40](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=640)  
        接口名`IProduct`，含属性`name: string`、方法`FN1(): void`、`FN2(): void`。
    - **依赖关系连线** [10:47](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=647)[10:51](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=651)[10:56](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=656)  
        Creator类到IProduct接口间为虚线+开放箭头的**依赖关系**（`<<use>>`），因Creator仅在其方法返回值中使用IProduct。
    - **Product1实现IProduct**[11:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=660)[11:10](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=670)[11:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=683)[11:24](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=684)[11:30](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=690)[11:33](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=693)[11:38](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=698)[11:43](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=703)[11:46](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=706)  
        Product1类框图含`name: string`、`FN1(): void`、`FN2(): void`；与IProduct间为虚线+空心三角箭头的**实现关系**。
    - **Product2实现IProduct** [11:50](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=710)[11:54](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=714)[11:57](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=717)  
        结构同Product1，同样以虚线+空心三角箭头连接IProduct。
- **设计原则符合性验证 [12:33](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=753)**
    
    - **开闭原则（OCP）体现** [12:36](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=756)[12:39](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=759)[12:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=762)[12:47](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=767)  
        对扩展开放：可无限新增Product3、Product4等IProduct实现类；对修改封闭：Creator类及现有Product类均无需改动。
    - **解耦设计说明** [12:52](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=772)[12:58](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=778)[13:04](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=784)[13:08](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=788)  
        Creator（工厂）与Product（产品）职责分离，降低模块间耦合度。
    - **扩展性图示验证** [13:18](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=798)[13:30](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=810)[13:37](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=817)[13:49](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=829)[13:56](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=836)[13:59](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=839)  
        UML图中可直观添加Product3类并以实现关系连接IProduct，证明扩展可行性。
    - **单一职责与依赖倒置补充确认** [14:19](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=859)  
        Creator仅负责对象创建，符合单一职责原则；全程依赖IProduct抽象，符合依赖倒置原则。
- **本节内容总结与后续预告 [15:06](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=906)**
    
    - **三层递进教学结构** [15:06](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=906)[15:12](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=912)[15:15](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=915)[15:17](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=917)  
        完成简易工厂→标准工厂（含接口/多实现类）→UML类图→设计原则验证全流程。
    - **实践导向学习法强调** [14:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=882)[14:53](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=893)  
        通过“写代码—画类图—验原则”循环，深化对面向对象设计的理解。
    - **下一节预告** [15:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=923)[15:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=928)  
        将深入工厂模式在实际工作场景中的典型应用案例。
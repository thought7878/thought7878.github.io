本视频讲述了*软件设计五大原则（单一职责、开闭、里氏替换、接口隔离、依赖倒置）的核心内涵与工程价值*，并*通过Promise加载图片的TypeScript代码实例，重点演示了单一职责原则与开闭原则*在异步链式调用中的具体实践，强调*设计原则对降低测试成本、提升系统可维护性与扩展性的现实意义*。

![[_posts/architect/System Design/教程/快速掌握前端必会的 7 种设计模式/3.设计原则-设计模式只是套路，设计原则是指导思想/media/ab6a358f47c2c4ec8297fe072c9c740f_MD5.webp]]

## 单一职责原则 
[00:01](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=1)
    
- **定义**  
	*每个程序（或模块、函数）只做好一件事情；
	功能过多时必须拆分为多个独立程序*。
- **结构要求**  
	*各部分保持高内聚、低耦合；
	相互独立又协作完成系统整体功能*。

## 开闭原则（重点强调）
[00:20](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=20)
    
- **定义**  
	*对扩展开放，对修改封闭；新功能通过扩展实现，而非修改既有代码*。
- 需求变更应对逻辑 [00:31](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=31)  
	需求变化时，应通过新增能力解决，而非改动原有逻辑。
- 工程动因：测试成本控制 [00:38](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=38)
	- 开发流程为：PM/运营提需求 → 需求评审 → 前端开发 → QA测试 → 上线。
	- *QA测试范围取决于代码改动范围*：若仅新增模块，则仅需测试该模块；若系统设计混乱导致多处修改，则需全量回归测试。
	- *全量测试显著增加工作量*（例：单模块测3天 vs 全系统测10天），直接抬高时间成本；*在劳动密集型开发中，“时间即金钱”*。
- 设计收益总结 [02:24](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=144)  
	**设计原则不仅保障逻辑合理性，更带来结构完整性、开发效率提升与测试成本降低等现实优势**。

## 里氏替换原则 
[03:09](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=189)
    
- **基本表述**  
	*子类对象能够替换父类对象出现在程序中，且不改变程序正确性*。
- 前端适用性说明 [03:17](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=197)  
	*该原则在前端开发中应用较少*，仅需初步了解，后续用到再深入。

## 接口隔离原则 
[03:25](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=205)
    
- **定义**  
	*保持接口单一、独立，避免“胖接口”（即一个接口承担过多职责）*。
- 类比说明
	接口应如专精工匠，而非“文韬武略、琴棋书画样样精通”的全能型角色。

## 依赖倒置原则 
[03:43](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=223)
    
- **定义**  
	*面向接口（抽象）编程，而非面向具体实现（实体）编程*。
- **代码体现方式** 
	- 函数参数类型应声明为接口，而非具体类；
	- 所有实现该接口的类均可作为实参传入，增强扩展性；
	- 若依赖具体类，则只能传入该类实例，丧失灵活性。

![[_posts/architect/System Design/教程/快速掌握前端必会的 7 种设计模式/3.设计原则-设计模式只是套路，设计原则是指导思想/media/afc4d072d326f780583b9768271b987d_MD5.webp]]

## Promise代码演示：单一职责与开闭原则落地 
[04:34](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=274)
    
- **示例功能**  
	实现异步加载图片，返回 `Promise<HTMLImageElement>`。
- **基础实现** [59–66]
	- 创建 `new Promise((resolve, reject) => { ... })`；
	- 使用 `document.createElement('img')` 创建图像元素；
	- 绑定 `img.onload = () => resolve(img)`；
	- 绑定 `img.onerror = () => reject(new Error('图片加载失败'))`；
	- 返回 Promise 实例。
- **泛型类型标注修正TS错误** [112–131]
	- 错误根源：`Promise` 默认泛型为 `any`，`then`回调中 `image` 类型推导失败；
	- 解决方案：显式声明泛型 `Promise<HTMLImageElement>`（第120句）；
	- `HTMLImageElement` 是 TypeScript 内置 DOM 接口（第121–124句）；
	- 泛型语法 `Promise<T>` 明确告知编译器 `resolve` 值类型为 `T`（第126–130句），使 `then` 参数获得精确类型 `HTMLImageElement`（第131句）。
- **链式调用体现设计原则** [78–90]
	- `promise.then(image => console.log(image.width))` 与 `.then(image => console.log(image.height))` 分离为两个独立 `then`；
	- 每个 `then` 仅执行单一操作（打印宽/高），符合**单一职责原则**（第82–84句）；
	- 新增功能（如打印 `alt` 属性）只需追加 `.then(image => console.log(image.alt))`，无需修改已有 `then`，体现**开闭原则**（第86–89句）；
	- 反例警示：若将所有逻辑写入单个 `then`，则后续修改易引发混乱（第94–97句）。

## 总结与延伸 
[09:24](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=564)
    
- 设计原则是理性工程决策，其“道理”直指可维护性、可测试性与成本控制（第101–105句）；
- 五大原则中，**S（单一职责）与 O（开闭）为重中之重**（第106–109句）；
- 后续将引入 Unix/Linux 设计哲学，其“做一件事并做好”等准则与前述原则高度契合（第110–111句）。
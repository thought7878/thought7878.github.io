本视频讲述了**观察者模式的设计原理与实现过程**，通过类图分析、代码演示及设计原则验证三步法，详细**展示了Subject（主题）与Observer（观察者）的关联关系、一对多结构、状态通知机制**，并确认其**符合开放封闭原则、高内聚低耦合等核心设计原则**。


### 基本结构 
[00:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=23)

- **Subject与Observer基本结构 [00:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=23)**
    - **Subject（主题）** [00:23](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=23)：表示被观察的对象，如咖啡吧台；包含私有属性 `state`（number类型，示例为咖啡制作状态）和 `observers`（Observer类型数组）；提供公有方法 `getState()`、`setState(newState)`、`attach(observer)`；含私有方法 `notify()`。
    - **Observer（观察者）** [00:26](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=26)：表示依赖于Subject状态变化的实体，如买咖啡的顾客；包含私有属性 `name`（字符串）和公有方法 `update(state)`（接收Subject状态并响应）。
- **一对N关联关系 [00:35](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=35)**
    - **关联含义** [00:35](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=35)：Subject与Observer之间为普通关联，标注“1”与“N”，表示一个Subject实例可关联多个Observer实例。
    - **技术依据** [00:44](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=44)：Subject中 `observers` 属性声明为 `Observer[]` 数组类型，支持存储多个Observer对象。
    - **生活类比** [01:03](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=63)：Subject类比咖啡店吧台，Observer类比排队买咖啡的多个顾客（你、我、他等），体现服务一对多的现实逻辑。

![[_posts/architect/System Design/教程/快速掌握前端必会的 7 种设计模式/6.观察者模式-前端（及其他UI编程）最常用的设计模式，工作必/media/6fcb326d616312bdbeec1b5975762e0d_MD5.webp]]

### 代码实现演示 
[03:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=201)

- **Subject类实现 [03:21](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=201)**
    - **私有属性定义** [03:28](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=208)：`private state: number = 0`（初始状态为0，代表咖啡未做好）；`private observers: Observer[] = []`（空数组，初始无观察者）。
    - **公有方法实现** [03:50](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=230)：
        - `getState(): number`：返回当前 `state`值。
        - `setState(newState: number): void`：更新 `this.state = newState`，并立即调用 `this.notify()`。
        - `attach(observer: Observer): void`：执行 `this.observers.push(observer)`，将新观察者加入数组。
    - **私有方法实现** [04:54](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=294)：`notify(): void`：遍历 `this.observers` 数组，对每个 `observer` 调用 `observer.update(this.state)`。
- **Observer类实现 [05:33](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=333)**
    - **构造函数与属性** [05:36](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=336)：`constructor(private name: string)`，初始化 `name` 属性。
    - **公有方法实现** [05:45](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=345)：`update(state: number): void`：打印 `console.log(`this.nameupdated,stateisthis.nameupdated,stateis{state}`)`，模拟接收通知并响应。
- **测试用例执行 [07:40](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=460)**
    - **实例化与注册** [07:44](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=464)：创建 `const subject = new Subject()`；`const observer1 = new Observer('A')`；`subject.attach(observer1)`；同理注册 `observer2`（name为'B'）。
    - **状态变更触发通知** [08:07](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=487)：调用 `subject.setState(1)`，触发 `notify()`，使 `observer1.update(1)` 和 `observer2.update(1)` 执行，输出 `A updated, state is 1` 和 `B updated, state is 1`。

### 类图重绘与结构确认 
[09:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=588)

- **Subject类图细节 [09:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=588)**
    - **属性区** [09:54](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=594)：`- state: number`；`- observers: Observer[]`。
    - **方法区** [10:49](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=649)：`+ getState(): number`；`+ setState(newState: number): void`；`+ attach(observer: Observer): void`；`- notify(): void`。
- **Observer类图细节 [11:39](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=699)**
    - **属性区** [11:39](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=699)：`- name: string`。
    - **方法区** [11:44](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=704)：`+ update(state: number): void`。
- **关联关系标注** [10:24](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=624)：Subject与Observer间绘制普通箭头，Subject端标注 `1`，Observer端标注 `N`，明确一对多语义。

### 设计原则验证 
[12:20](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=740)

- **开放封闭原则（OCP）** [12:20](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=740)：Subject与Observer职责分离，各自可独立扩展（如新增Observer子类或Subject子类），无需修改原有代码。
- **高内聚低耦合** [12:45](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=765)：
    - **高内聚** [12:45](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=765)：Subject内部封装 `state` 和 `observers` 状态管理逻辑；Observer内部封装 `name` 和 `update` 响应逻辑。
    - **低耦合** [12:53](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=773)：Subject仅通过 `update()` 接口与Observer交互，不依赖具体实现；所有非必要属性/方法均标记为 `private`（减号），公有接口（`+`）仅暴露必需方法（`getState`、`setState`、`attach`）。
- **扩展性体现** [13:11](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=791)：`observers` 为数组结构，支持动态添加任意数量Observer实例（2个、3个…至1万个），天然支持一对多横向扩展。
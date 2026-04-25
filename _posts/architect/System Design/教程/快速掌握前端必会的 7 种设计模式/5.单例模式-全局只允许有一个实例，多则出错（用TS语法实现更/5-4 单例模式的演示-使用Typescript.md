本视频讲述了单例模式在TypeScript和JavaScript中的代码实现与UML类图表达，重点解析了TS中静态属性、私有构造函数的语法特性及其对单例约束的保障机制，并通过实例验证了单例对象的唯一性。


### 单例模式演示目标与技术选型说明 
[00:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=0)

- **核心目标**  
    演示单例模式的代码实现，解答“如何从代码层面限制仅创建一个实例”的问题。
- **双语言演示必要性**  
    TypeScript与JavaScript实现单例模式的机制完全不同：TS依赖`static`属性与`private constructor`语法约束，而JS无原生静态成员与访问修饰符，需采用闭包等不同范式；因此必须分别讲解以确保全面性。
- **教学延伸目的**  
    演示后将验证是否符合开闭原则（Open-Closed Principle），该验证本质是帮助学员回顾设计原则与具体模式的对应关系，强化体系化理解。

### TypeScript静态属性详解 
[01:13](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=73)

- **静态属性与实例属性的本质区分**
    - 实例属性/方法属于对象级别：通过`new Class()`创建实例后，由实例（如`f1`）调用，`this`指向当前实例（`f1`或`f2`），值随实例独立变化。
    - 静态属性/方法属于类级别：通过`Class.staticMember`直接访问，`this`在静态方法中指向类本身（如`Foo`），与任何实例无关，值全局唯一且固定。
- **语法标识与调用规范**
    - 声明：使用`static`关键字（如`static flag = "ABC"`）。
    - UML类图表示：静态成员名称下方加下划线（如`_flag: string`）。
    - 调用：禁止通过实例访问（`f1.flag`报错），必须通过类名访问（`Foo.flag`）。

### 单例模式UML类图解析 
[06:44](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=404)

- **类结构定义**
    - 类名：`Singleton`。
    - 私有静态属性：`- instance: Singleton`（下划线表示静态，减号表示`private`）。
    - 公共静态方法：`+ getInstance(): Singleton`（下划线表示静态，加号表示`public`）。
- **自引用关系**  
    `instance`属性类型为`Singleton`自身，UML中以普通箭头（非空心三角）从`Singleton`指向自身，表示自关联。

### TypeScript单例模式代码实现 
[08:08](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=488)

- **类声明与静态属性**
    
    typescript
    
    ```typescript
    class Singleton {
      private static instance: Singleton;
    }
    ```
    
- **私有构造函数**
    
    typescript
    
    ```typescript
    private constructor() {}
    ```
    
    确保外部无法通过`new Singleton()`创建实例，强制所有实例化行为收敛至`getInstance`。
- **静态获取方法**
    
    typescript
    
    ```typescript
    public static getInstance(): Singleton {
      if (Singleton.instance === null) {
        Singleton.instance = new Singleton();
      }
      return Singleton.instance;
    }
    ```
    
    利用静态属性`instance`缓存唯一实例，首次调用时初始化，后续调用直接返回缓存对象。
- **实例唯一性验证**
    
    typescript
    
    ```typescript
    const s1 = Singleton.getInstance();
    const s2 = Singleton.getInstance();
    console.log(s1 === s2); // true
    ```
    
    通过严格相等（\`\=\=\=\`）验证`s1`与`s2`指向同一内存地址，证明全局唯一性。

### 关键约束机制总结 
[14:52](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=f9cedfe7f47d4c2781ece143cbd16a47#?seek_t=892)

- **三重防护保障单例**
    - `private static instance`：阻止外部直接读写实例引用。
    - `private constructor()`：阻止外部任意`new`操作，切断非法实例化路径。
    - `public static getInstance()`：提供唯一合法入口，内部管控实例生命周期。
- **UML与代码一致性**  
    类图中`- instance`（私有静态属性）、`+ getInstance()`（公共静态方法）、自引用箭头，完全对应代码中`private static instance`、`public static getInstance()`及`instance: Singleton`类型声明。
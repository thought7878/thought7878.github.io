本视频讲述了**JavaScript中内置迭代器（Symbol.iterator）的工作原理与应用**，通过对比自定义数据生成器的next/hasNext模式，阐明了所有有序结构（如数组、Map等）均具备Symbol.iterator方法，执行后返回具有next()方法和done属性的迭代器对象，从而支持统一的遍历机制。


- **回顾自定义迭代器模式 [00:12](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=12)**
    
    - **next与hasNext的作用解析** [00:12](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=12)  
        `next()`用于获取下一个值并累加索引，`hasNext()`用于判断是否还有下一个值。
    - **while循环遍历逻辑** [00:22](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=22)  
        通过`while`循环配合`hasNext()`判断与`next()`取值，实现对所有元素的逐个访问与打印。
- **有序结构内置Symbol.iterator方法 [00:48](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=48)**
    
    - **支持Symbol.iterator的有序结构类型** [00:53](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=53)  
        包括数组、NodeList、Map、Set、arguments等所有有序对象或有序结构。
    - **Symbol.iterator方法的性质与返回值** [01:00](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=60)  
        是一个`Symbol`类型的值，调用后返回**迭代器对象**。
    - **迭代器对象结构类比** [01:17](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=77)  
        返回的迭代器对象与此前演示的自定义迭代器高度一致，含`next()`方法及状态标识。
- **数组Symbol.iterator实操演示 [01:24](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=84)**
    
    - **创建数组并调用Symbol.iterator** [01:24](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=84)  
        定义数组`[10, 20, 30]`，执行`arr[Symbol.iterator]()`获取迭代器`iter`。
    - **迭代器next()返回格式** [02:08](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=128)  
        `iter.next()`返回对象：`{ value: 10, done: false }`；`{ value: 20, done: false }`；`{ value: 30, done: false }`；`{ value: undefined, done: true }`。
    - **value与done语义对应关系** [02:27](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=147)  
        `value`即当前迭代项（等同于自定义`next()`返回的“下一个值”）；`done: false`表示未完成（等同于`hasNext() === true`），`done: true`表示迭代结束（等同于`hasNext() === false`）。
- **Symbol.iterator通用性验证：Map结构 [05:05](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=305)**
    
    - **Map实例化与Symbol.iterator调用** [05:05](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=305)  
        创建`new Map([['k1', 'v1'], ['k2', 'v2']])`，调用`map[Symbol.iterator]()`获取迭代器。
    - **Map迭代器next()返回格式** [05:45](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=345)  
        `next()`返回键值对数组：`{ value: ['k1', 'v1'], done: false }`；`{ value: ['k2', 'v2'], done: false }`；`{ value: undefined, done: true }`。
- **Map其他视图的Symbol.iterator支持 [06:11](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=371)**
    
    - **values()视图迭代器** [06:11](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=371)  
        `map.values()[Symbol.iterator]()`返回迭代器，`next()`依次返回`{ value: 'v1', done: false }`、`{ value: 'v2', done: false }`、`{ value: undefined, done: true }`。
    - **keys()与entries()视图同理** [06:42](https://b.quark.cn/apps/5AZ7aRopS/routes/mofb35Rkb?debug=0&fid=a7d94a717c6448d4b2706a9e235c2be4#?seek_t=402)  
        `map.keys()[Symbol.iterator]()`与`map.entries()[Symbol.iterator]()`同样返回标准迭代器，分别遍历键与完整键值对。
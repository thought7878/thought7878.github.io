[Mongoose v7.5.2: Queries](https://mongoosejs.com/docs/queries.html)

Mongoose [models](https://mongoosejs.com/docs/models.html) provide several static helper functions for [CRUD operations](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete). Each of these functions returns a [mongoose `Query` object](https://mongoosejs.com/docs/api/query.html#Query).

mongoose query可以通过两种方式之一执行。首先，如果您传入 `callback` 函数，Mongoose 将异步执行查询并将结果传递给 `callback` 。查询还具有 `.then()` 函数，因此可以用作promise。



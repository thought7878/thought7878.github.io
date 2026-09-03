>Mongoose 中的 Middleware 介绍

Mongoose 的 `Middleware（中间件）`，也常被称为 `Hooks（钩子）`，是指**在 Mongoose 执行某些操作前后自动执行的函数**。

**应用的场景，如：**

```text
保存用户前自动加密密码
删除文章前检查权限
查询结果返回后自动填充关联数据
保存成功后写入审计日志
发生唯一键冲突时转换错误信息
```

这些都可以用 Mongoose Middleware 实现。

---

# 一、Middleware 的基本概念

Mongoose Middleware *定义在 Schema 上，常见写法是：*

```javascript
schema.pre('操作名', 中间件函数)
```

或：

```javascript
schema.post('操作名', 中间件函数)
```

- `pre`：**操作执行前**触发
- `post`：**操作执行后**触发

例如：

```javascript
const userSchema = new mongoose.Schema({
  name: String,
  password: String
});

userSchema.pre('save', function (next) {
  console.log('保存前执行');
  next();
});

userSchema.post('save', function (doc) {
  console.log('保存后执行');
});
```

**执行流程大致是：**

```text
pre middleware
  -> 实际操作，例如 save / find / updateOne
    -> post middleware
```

---

# 二、常见**使用场景**

Mongoose Middleware 常用于：

| 场景                | 说明                      |
| ----------------- | ----------------------- |
| 保存前**加密密码**       | `pre('save')`           |
| **自动设置创建时间、更新时间** | `pre('save')`           |
| 查询时自动过滤软删除数据      | `pre('find')`           |
| 查询后自动 populate    | `post('find')`          |
| 删除后同步删除关联数据       | `post('deleteOne')`     |
| 保存成功后写日志          | `post('save')`          |
| 统一处理数据库错误         | `post` error middleware |
| 聚合前自动追加过滤条件       | `pre('aggregate')`      |

---

# 三、Middleware 的**四大类型**

Mongoose 中间件主要分为四类：

```text
1. Document Middleware 文档中间件
2. Query Middleware 查询中间件
3. Model Middleware 模型中间件
4. Aggregation Middleware 聚合中间件
```

---

## 1. Document Middleware：文档中间件

文档中间件**作用在文档实例上**。

常见操作：

```javascript
init
validate
save
updateOne
deleteOne
```

其中**最常用的是：**

```javascript
save
```

示例：

```javascript
const userSchema = new mongoose.Schema({
  username: String,
  password: String
});

userSchema.pre('save', function (next) {
  console.log('文档保存前');
  next();
});

userSchema.post('save', function (doc) {
  console.log('文档保存后');
});
```

*在文档中间件中，`this` 指向当前文档*。

例如：

```javascript
userSchema.pre('save', function (next) {
  console.log(this.username);
  next();
});
```

使用文档保存：

```javascript
const user = new User({
  username: 'tom',
  password: '123456'
});

await user.save();
```

会触发：

```text
pre('save')
save 操作
post('save')
```

---

# 四、pre('save') 常见用法

## 1. 保存前加密密码

```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');

const userSchema = new mongoose.Schema({
  username: String,
  password: String
});

userSchema.pre('save', async function (next) {
  // 如果密码没有被修改，则不需要重新加密
  if (!this.isModified('password')) {
    return next();
  }

  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);

  next();
});

const User = mongoose.model('User', userSchema);
```

这样每次保存用户时，如果密码被修改，就会自动加密。

---

## 2. 自动设置创建时间和更新时间

虽然 Mongoose 提供了 `timestamps` 选项：

```javascript
new mongoose.Schema(
  {
    name: String
  },
  {
    timestamps: true
  }
);
```

但你也可以手动实现：

```javascript
const articleSchema = new mongoose.Schema({
  title: String,
  createdAt: Date,
  updatedAt: Date
});

articleSchema.pre('save', function (next) {
  const now = new Date();

  if (this.isNew) {
    this.createdAt = now;
  }

  this.updatedAt = now;

  next();
});
```

---

## 3. 数据预处理

例如保存前将邮箱转成小写：

```javascript
userSchema.pre('save', function (next) {
  if (this.isModified('email')) {
    this.email = this.email.toLowerCase();
  }

  next();
});
```

---

# 五、post('save') 常见用法

`post('save')` 在文档保存成功后执行。

示例：

```javascript
userSchema.post('save', function (doc) {
  console.log('用户保存成功', doc._id);
});
```

可以在这里做：

- 记录日志
- 发送通知
- 同步数据到搜索服务
- 写入审计表

例如：

```javascript
userSchema.post('save', async function (doc) {
  await AuditLog.create({
    action: 'CREATE_USER',
    targetId: doc._id,
    createdAt: new Date()
  });
});
```

注意：`post('save')` 通常不应该用来取消保存，因为此时保存已经发生。

如果需要阻止保存，应使用：

```javascript
pre('save')
```

---

# 六、Query Middleware：查询中间件

查询中间件作用在 Mongoose Query 对象上。

常见操作包括：

```javascript
find
findOne
findOneAndUpdate
findOneAndDelete
updateOne
updateMany
deleteOne
deleteMany
countDocuments
distinct
```

在查询中间件中，`this` 指向当前查询对象，而不是文档。

---

## 1. 查询前自动过滤软删除数据

假设文章有一个软删除字段：

```javascript
const articleSchema = new mongoose.Schema({
  title: String,
  content: String,
  isDeleted: {
    type: Boolean,
    default: false
  }
});
```

可以在查询时自动过滤已删除数据：

```javascript
articleSchema.pre(['find', 'findOne'], function (next) {
  this.where({ isDeleted: { $ne: true } });
  next();
});
```

这样：

```javascript
await Article.find();
```

实际查询条件会变成：

```javascript
{ isDeleted: { $ne: true } }
```

---

## 2. 查询前自动 populate

```javascript
const commentSchema = new mongoose.Schema({
  content: String,
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User'
  }
});

commentSchema.pre(['find', 'findOne'], function (next) {
  this.populate('user');
  next();
});
```

这样查询评论时会自动带出用户信息。

---

## 3. 查询后处理结果

```javascript
articleSchema.post('find', function (docs) {
  console.log(`查询到 ${docs.length} 篇文章`);
});
```

---

# 七、Document Middleware 和 Query Middleware 的区别

这是 Mongoose 中非常重要的一点。

例如：

```javascript
schema.pre('save', function () {
  console.log('document save middleware');
});
```

它只在文档实例调用 `save()` 时触发：

```javascript
const user = new User(data);
await user.save();
```

但下面这些更新操作默认不会触发 `save` 中间件：

```javascript
await User.updateOne({ _id: id }, { name: 'Tom' });
await User.updateMany({}, { status: 'active' });
await User.findOneAndUpdate({ _id: id }, { name: 'Tom' });
```

因为 `updateOne()`、`updateMany()`、`findOneAndUpdate()` 是查询/更新操作，不一定经过文档 `save()` 流程。

如果你想在更新时执行逻辑，需要使用查询中间件：

```javascript
userSchema.pre(['updateOne', 'findOneAndUpdate'], function (next) {
  console.log('update middleware');
  next();
});
```

简单记忆：

```text
doc.save()        触发 document save middleware
Model.updateOne() 触发 query middleware
```

---

# 八、Model Middleware：模型中间件

模型中间件作用在 Model 级别。

常见操作：

```javascript
insertMany
createCollection
createIndexes
```

例如 `insertMany`：

```javascript
const userSchema = new mongoose.Schema({
  username: String,
  password: String
});

userSchema.pre('insertMany', function (next, docs) {
  docs.forEach(doc => {
    doc.createdAt = new Date();
  });

  next();
});
```

使用：

```javascript
await User.insertMany([
  { username: 'a', password: '123' },
  { username: 'b', password: '456' }
]);
```

注意：

```javascript
Model.insertMany()
```

默认不会触发每个文档的 `save` 中间件。

所以如果你有密码加密逻辑写在：

```javascript
pre('save')
```

那么：

```javascript
await User.create([...]);
```

通常会触发 `save`，因为 `create()` 内部会保存文档。

但：

```javascript
await User.insertMany([...]);
```

不一定会走每个文档的 `save` 中间件。

如果需要处理 `insertMany`，要单独写：

```javascript
userSchema.pre('insertMany', function (next, docs) {
  // 批量处理
  next();
});
```

---

# 九、Aggregation Middleware：聚合中间件

聚合中间件用于 `aggregate()` 操作。

示例：

```javascript
const orderSchema = new mongoose.Schema({
  userId: mongoose.Schema.Types.ObjectId,
  amount: Number,
  status: String
});

orderSchema.pre('aggregate', function (next) {
  console.log('执行聚合前');
  next();
});

orderSchema.post('aggregate', function (result) {
  console.log('聚合执行完成');
});
```

使用：

```javascript
await Order.aggregate([
  {
    $match: { status: 'paid' }
  },
  {
    $group: {
      _id: '$userId',
      total: { $sum: '$amount' }
    }
  }
]);
```

你也可以在聚合前自动插入公共条件：

```javascript
orderSchema.pre('aggregate', function (next) {
  this.pipeline().unshift({
    $match: { tenantId: 'tenant_001' }
  });

  next();
});
```

---

# 十、Middleware 中的 next 和 async

Mongoose Middleware 支持多种写法。

---

## 1. 回调风格

```javascript
userSchema.pre('save', function (next) {
  console.log('before save');
  next();
});
```

必须调用 `next()`，否则流程会继续等待。

---

## 2. async 风格

如果中间件是 `async` 函数，可以不用调用 `next()`：

```javascript
userSchema.pre('save', async function () {
  if (!this.isModified('password')) {
    return;
  }

  this.password = await bcrypt.hash(this.password, 10);
});
```

如果函数执行成功，Mongoose 会自动继续。

如果抛出错误，会进入错误处理流程：

```javascript
userSchema.pre('save', async function () {
  throw new Error('发生错误');
});
```

---

# 十一、Middleware 中的 this 指向

Mongoose Middleware 中 `this` 的指向取决于中间件类型。

---

## 1. Document Middleware

```javascript
userSchema.pre('save', function () {
  console.log(this); // 当前文档
});
```

---

## 2. Query Middleware

```javascript
userSchema.pre('find', function () {
  console.log(this); // 当前 Query 对象
});
```

例如可以修改查询条件：

```javascript
userSchema.pre('find', function () {
  this.where({ status: 'active' });
});
```

---

## 3. Aggregation Middleware

```javascript
orderSchema.pre('aggregate', function () {
  console.log(this.pipeline());
});
```

---

# 十二、不要使用箭头函数定义需要 this 的 Middleware

错误示例：

```javascript
userSchema.pre('save', () => {
  console.log(this); // 不是当前文档
});
```

箭头函数没有自己的 `this`。

正确写法：

```javascript
userSchema.pre('save', function () {
  console.log(this); // 当前文档
});
```

如果你不需要使用 `this`，箭头函数可以用：

```javascript
userSchema.post('save', () => {
  console.log('saved');
});
```

但一般建议使用普通函数。

---

# 十三、错误处理 Middleware

Mongoose 支持错误处理中间件。

普通 `post` 中间件：

```javascript
schema.post('save', function (doc) {
  console.log('保存成功');
});
```

错误处理 `post` 中间件：

```javascript
schema.post('save', function (err, doc, next) {
  console.log('保存失败', err);
  next(err);
});
```

如果第一个参数是 `error`，Mongoose 会把它识别为错误处理中间件。

例如处理唯一索引冲突：

```javascript
userSchema.post('save', function (err, doc, next) {
  if (err.code === 11000) {
    return next(new Error('邮箱已存在'));
  }

  next(err);
});
```

也可以处理验证错误：

```javascript
userSchema.post('save', function (err, doc, next) {
  if (err.name === 'ValidationError') {
    return next(new Error('参数校验失败'));
  }

  next(err);
});
```

---

# 十四、多个 Middleware 的执行顺序

可以定义多个中间件：

```javascript
userSchema.pre('save', function (next) {
  console.log('pre save 1');
  next();
});

userSchema.pre('save', function (next) {
  console.log('pre save 2');
  next();
});

userSchema.post('save', function () {
  console.log('post save 1');
});

userSchema.post('save', function () {
  console.log('post save 2');
});
```

执行顺序：

```text
pre save 1
pre save 2
save
post save 1
post save 2
```

---

# 十五、常用示例汇总

---

## 1. 保存前自动加密密码

```javascript
const bcrypt = require('bcrypt');

const userSchema = new mongoose.Schema({
  email: String,
  password: String
});

userSchema.pre('save', async function () {
  if (!this.isModified('password')) return;

  this.password = await bcrypt.hash(this.password, 10);
});
```

---

## 2. 查询时自动过滤已删除数据

```javascript
const articleSchema = new mongoose.Schema({
  title: String,
  isDeleted: {
    type: Boolean,
    default: false
  }
});

articleSchema.pre(['find', 'findOne', 'findOneAndUpdate'], function () {
  this.where({ isDeleted: { $ne: true } });
});
```

---

## 3. 查询后记录日志

```javascript
articleSchema.post('find', function (docs) {
  console.log(`查询文章数量：${docs.length}`);
});
```

---

## 4. 删除文章后删除相关评论

```javascript
const articleSchema = new mongoose.Schema({
  title: String
});

articleSchema.post('deleteOne', { document: true }, async function () {
  await Comment.deleteMany({ articleId: this._id });
});
```

注意：不同 Mongoose 版本中，`deleteOne` 可能既可以是文档中间件，也可以是查询中间件。如果需要明确作用在文档上，可以加：

```javascript
{ document: true }
```

如果作用在查询上，可以加：

```javascript
{ query: true }
```

---

## 5. 保存成功后写审计日志

```javascript
const auditLogSchema = new mongoose.Schema({
  action: String,
  targetId: mongoose.Schema.Types.ObjectId,
  createdAt: Date
});

const AuditLog = mongoose.model('AuditLog', auditLogSchema);

userSchema.post('save', async function (doc) {
  await AuditLog.create({
    action: 'SAVE_USER',
    targetId: doc._id,
    createdAt: new Date()
  });
});
```

---

# 十六、使用 Middleware 时的注意事项

---

## 1. `pre('save')` 不会在 update 操作中触发

下面不会触发 `pre('save')`：

```javascript
await User.updateOne(
  { _id: userId },
  { name: 'Tom' }
);
```

如果需要在更新时处理，要写查询中间件：

```javascript
userSchema.pre(['updateOne', 'findOneAndUpdate'], function () {
  // ...
});
```

---

## 2. `insertMany()` 不等同于多次 `save()`

```javascript
await User.insertMany(users);
```

不会像下面这样简单触发每个文档的 `save`：

```javascript
await user.save();
```

如果有批量插入逻辑，需要使用：

```javascript
userSchema.pre('insertMany', function (next, docs) {
  // ...
  next();
});
```

---

## 3. 箭头函数没有 this

错误：

```javascript
userSchema.pre('save', () => {
  this.password = '123';
});
```

正确：

```javascript
userSchema.pre('save', function () {
  this.password = '123';
});
```

---

## 4. pre 中间件可以阻止操作执行

如果在 `pre` 中间件里抛出错误或调用：

```javascript
next(new Error('错误'));
```

后续操作会被终止。

示例：

```javascript
userSchema.pre('save', function (next) {
  if (!this.email) {
    return next(new Error('邮箱不能为空'));
  }

  next();
});
```

---

## 5. post 中间件一般不能取消原操作

因为 `post` 执行时，原操作通常已经完成。

例如：

```javascript
userSchema.post('save', function () {
  throw new Error('保存后错误');
});
```

此时文档可能已经保存成功。

所以校验和拦截逻辑通常放在：

```javascript
pre
```

后置处理放在：

```javascript
post
```

---

# 十七、Middleware 执行流程总结

以保存文档为例：

```javascript
await user.save();
```

流程：

```text
pre('validate')
  -> validate
    -> post('validate')
      -> pre('save')
        -> save 到数据库
          -> post('save')
```

以查询为例：

```javascript
await User.find();
```

流程：

```text
pre('find')
  -> 执行查询
    -> post('find')
```

---

# 十八、一张表理解 Mongoose Middleware

| 中间件类型 | 常见操作 | this 指向 | 典型用途 |
|---|---|---|---|
| Document Middleware | `save`、`validate`、`updateOne`、`deleteOne` | 文档实例 | 加密密码、字段预处理、文档级校验 |
| Query Middleware | `find`、`findOne`、`updateOne`、`deleteMany` | Query 对象 | 自动过滤条件、查询日志、更新逻辑 |
| Model Middleware | `insertMany`、`createIndexes` | Model | 批量插入处理、索引初始化 |
| Aggregation Middleware | `aggregate` | Aggregate 对象 | 聚合前追加条件、聚合日志 |

---

# 十九、一句话总结

Mongoose Middleware 是一种在数据库操作前后自动执行逻辑的机制。

它常用于：

```text
保存前加密密码
查询时过滤软删除数据
更新时自动补充字段
删除后清理关联数据
保存后写日志
统一处理错误
```

核心写法：

```javascript
schema.pre('操作名', function () {
  // 操作前逻辑
});

schema.post('操作名', function () {
  // 操作后逻辑
});
```

最重要的是要区分：

```text
文档中间件：作用于文档，例如 save
查询中间件：作用于查询，例如 find / updateOne
```

否则很容易出现：

```text
为什么我的 pre('save') 没有被触发？
```

通常原因就是：

```text
你执行的是 update / delete / insertMany，而不是 doc.save()
```
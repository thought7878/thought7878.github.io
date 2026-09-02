
# 什么是 Mongoose？
[[1.什么是 Mongoose、schema、model？]]

# 连接 数据库 与 Express
[[_posts/后端/课程/Node.js、Express、MongoDB & More：The Complete Bootcamp/介绍#连接 数据库 与 Express|视频演示：连接数据库 与 Express]]


# 创建 Schema、Model、Document
[[_posts/后端/课程/Node.js、Express、MongoDB & More：The Complete Bootcamp/介绍#创建 Schema、Model、Document|创建 Schema、Model、Document]]

## 创建 Schema

## 创建 Model（通过 Schema）

## 创建 Document（通过 Model）

# CRUD
[[_posts/后端/课程/Node.js、Express、MongoDB & More：The Complete Bootcamp/介绍# [实践] CRUD（Tours）|[实践] CRUD（Tours）]]

## Create Document

## Read Document
### 实现**过滤**功能
- Model.find() 返回Query对象
- query.where()/equals()
```js
Model.find();
Model.find().where('age').equals(49);
Model.where('age').equals(49);
```


## Update Document

## Delete Document

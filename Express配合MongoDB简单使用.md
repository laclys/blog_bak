---
title: Express配合MongoDB简单使用
tags: 
	- Express 
	- MongoDB
---

快速开发一个web接口，应该没有比Express+mongoDB更快/更简单的组合了。这里简单总结一些使用nodeJs的mongoose模块链接和操作mongoDB。

### Express:
基于nodejs快速、开放、极简的web开发框架,怎么安装就不在赘言。这里要说的是如果使用node命令启动server，每次修改文件都需要Ctrl+c非常麻烦。这里可以使用nodemon自动重启，监听路由和响应内容.十分方便
```
npm install -g nodemon
```
Express的一些特性：
- app.get、app.post分别开发get和post接口
- app.use使用模块
- res.send、res.json、res.sendfile响应不同的内容

使用起来非常简单，半个小时不到就能上手

### mongoDB
Mac的话,安装mongoDB非常简单
```
brew install mongodb
```
搞定.

通过命令后台启动
```
mongod --config /usr/local/etc/mongod.conf
```
之后再开一个终端窗口

![](/assets/blogImg/20171206-01.jpg)

就证明mongo安装成功咯。

<!-- more -->

之后就是安装mongoose。mongoDB和node配合最好的一个库。
说通俗一点：通过mongoose操作mongobd，存储的就是json，相对mysql来说，要易用很多。
```
npm install mongoose --save
```
### mongoose的使用

- connect链接数据库
```javascript
const mongoose = require('mongoose')
// 链接mongo
const DB_URL = 'mongodb://127.0.0.1:27017'
mongoose.connect(DB_URL)
mongoose.connection.on('connected',function () {
  console.log('mongo connect success')
})
```
如果node server端打印出：mongo connect success。mongoDB就链接上了

- 定义文档模型，Schema和model新建模型
- 代一个数据库文档对应一个模型，通过模型对数据库进行操作

eg：
```javascript
const User = mongoose.model('user', new mongoose.Schema({
  name: {type: String, require: true},
  age: {type: Number, require: true},
}))
```
### Mongoose文档
类型：
- String、Number等数据结构
- 定create、remove、update分别用来增删改查的操作
- Find和findOne用来查询数据

新增数据：
```javascript
// 新增数据
User.create({
  name: 'lac',
  age: 20
}, function (err, doc) {
  if (!err) {
    console.log(doc)
  } else {
    console.log(err)
  }
})

```

删除数据：
```javascript
// 删除数据
User.remove({age: 20}, function (err, doc) {
  if (!err) {
    console.log(doc)
  }
})
```
查询数据
```javascript
  User.find({name: 'lac'},function (err, doc) {
    res.json(doc)
  })
```
findOne就是查找到一条返回~，find是多条。

更新数据
```javascript
// 更新
User.update({'name': 'lac'},{'$set': {age: 26}},function (err, doc) {
  console.log(doc)
})
```
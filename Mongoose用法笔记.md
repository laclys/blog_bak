---
title: Mongoose用法笔记
tags: 
	- mongoDB 
---

本来新年第一篇博文理应是2017年的总结。客观+主观原因，一直拖着难产不出。近期工作这边有需求，APP这块一个独立的业务搭建一个迷你小后台。既然是迷你小后台，应该找不出Express+Mongo以外更好的选择了吧。node和mongo结合，最通用的就是mongoose这个库，操作使用都很便利，简单。在这里笔记总结一下

<!-- more -->

首先需要npm install mongoose 安装mongoose，安装成功后就可以通过require('mongoose') 来使用！


### Connect
```javascript
var mongoose = require('mongoose'),
    DB_URL = 'mongodb://localhost:27017/mongoosesample';

/**
 * 连接
 */
mongoose.connect(DB_URL);

/**
  * 连接成功
  */
mongoose.connection.on('connected', function () {    
    console.log('Mongoose connection open to ' + DB_URL);  
});    

/**
 * 连接异常
 */
mongoose.connection.on('error',function (err) {    
    console.log('Mongoose connection error: ' + err);  
});    
 
/**
 * 连接断开
 */
mongoose.connection.on('disconnected', function () {    
    console.log('Mongoose connection disconnected');  
});
```

### Schema

　schema是mongoose里会用到的一种数据模式，可以理解为表结构的定义；每个schema会映射到mongodb中的一个collection，它不具备操作数据库的能力
　
```javascript
　
　var userSchema =  new mongoose.Schema({
  name: {type: String, require: true},
  age: {type: Number, require: true}
}))
```
　
　定义一个Schema就这么简单~

　Schema Types内置类型如下：

　　String

　　Number

　　Boolean | Bool

　　Array

　　Buffer

　　Date

　　ObjectId | Oid

　　Mixed

### 定义好Schema，接下就是生成Model。model是由schema生成的模型，可以对数据库的操作

```javascript
const User = mongoose.model('user', userSchema)
```

### 常用数据库操作

插入 Model#save([fn])
```javascript
// insert

 function insert () {
   var user = new User({
     name: 'yoyo',
     age: 66
   })

   user.save(function(err, doc) {
     if (err) {
       console.log("Error:" + err);
     }
     else {
         console.log("Doc:" + doc);
     }
   })
```
更新  Model.update(conditions, update, [options], [callback])

```javascript
 User.update({'name': 'yoyo'}, {'$set': {age: 26}}, function(err, doc) {
 console.log(doc)
 })
```

update方法基本可以满足所有更新！
常用方法还有findByIdAndUpdate，这种比较有指定性，就是根据_id
Model.findByIdAndUpdate(id, [update], [options], [callback])

```javascript
function findByIdAndUpdate(){
    var id = '56f2558b2dd74855a345edb2';
    var updatestr = {'name': 'yoyoyo'};
    
    User.findByIdAndUpdate(id, updatestr, function(err, res){
        if (err) {
            console.log("Error:" + err);
        }
        else {
            console.log("Res:" + res);
        }
    })
}

findByIdAndUpdate();
```
其他的更新方法：
Model.findOneAndUpdate([conditions], [update], [options], [callback])　　　　　　//找到一条记录并更新

删除 Model.remove(conditions, [callback])
```javascript
User.remove({},function (e, d) {})
```
res中会返回是否成功以及影响的行数：{"ok":1,"n":1}

还有其他删除：

　　Model.findByIdAndRemove(id, [options], [callback])　　　　　　

　　Model.findOneAndRemove(conditions, [options], [callback])
　　
查 Model.find(conditions, [fields], [options], [callback])
```javascript
  User.find({name: 'zyy'}, function(err, doc) {
    res.json(doc)
  })
```
第2个参数可以设置要查询输出的字段,比如改成:
```javascript
  var opt = {"name": 1 ,"_id": 0};
  User.find({name: 'zyy'}, opt, function(err, doc) {
    res.json(doc)
  })
```
输出只会有name字段，设置方法如上，1表示查询输出该字段，0表示不输出

比如我要查询年龄范围条件应该怎么写呢？

User.find({userage: {$gte: 21, $lte: 65}}, callback);    //这表示查询年龄大于等21而且小于等于65岁

类似的其他：

$or　　　　或关系

　　$nor　　　 或关系取反

　　$gt　　　　大于

　　$gte　　　 大于等于

　　$lt　　　　 小于

　　$lte　　　  小于等于

　　$ne            不等于

　　$in             在多个值范围内

　　$nin           不在多个值范围内

　　$all            匹配数组中多个值

　　$regex　　正则，用于模糊查询

　　$size　　　匹配数组大小

　　$maxDistance　　范围查询，距离（基于LBS）

　　$mod　　   取模运算

　　$near　　　邻域查询，查询附近的位置（基于LBS）

　　$exists　　  字段是否存在

　　$elemMatch　　匹配内数组内的元素

　　$within　　范围查询（基于LBS）

　　$box　　　 范围查询，矩形范围（基于LBS）

　　$center       范围醒询，圆形范围（基于LBS）

　　$centerSphere　　范围查询，球形范围（基于LBS）

　　$slice　　　　查询字段集合中的元素（比如从第几个之后，第N到第M个元素）

　　

　　可能还有一些，没什么印象，大家自行看看api ^_^!　　

数量查询 Model.count(conditions, [callback])
```javascript

function getCountByConditions(){
    var wherestr = {}
    
    User.count(wherestr, function(err, res){
        if (err) {
            console.log("Error:" + err);
        }
        else {
            console.log("Res:" + res);
        }
    })
}
getCountByConditions()

```

其他的一些东西：
根据_id查询 Model.findById(id, [fields], [options], [callback])

模糊查询：
```javascript
var User = require("./user.js");

function getByRegex(){
    var whereStr = {'username':{$regex:/m/i}};
    
    User.find(whereStr, function(err, res){
        if (err) {
            console.log("Error:" + err);
        }
        else {
            console.log("Res:" + res);
        }
    })
}

getByRegex();

```
上面示例中查询出所有用户名中有'm'的名字，且不区分大小写，模糊查询比较常用，正则形式匹配，正则方式就是javascript正则，用到的比较多！

### 常用的其他方法
　　Model.distinct(field, [conditions], [callback])　　　　　　　　　　　　　　　　　　//去重

　　Model.findOne(conditions, [fields], [options], [callback])　　　　　　　　　　　　 //查找一条记录

　　Model.findOneAndRemove(conditions, [options], [callback])　　　　　　　　　　 //查找一条记录并删除

　　Model.findOneAndUpdate([conditions], [update], [options], [callback])　　　　　 //查找一条记录并更新
　　
　　以上~
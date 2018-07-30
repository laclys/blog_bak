---
title: 小试NodeJS的express进行前后端数据交互
tags: 
	- node 
	- express
---

今儿小试了一下NodeJs的express框架，在这说一说如何通过express进行前后台的数据交互。

这几年nodeJs大火，有很多原因。其中最重要的我想就是和前台同为JS语言，在交互上比其他后台语言配合起来更爽一些。

这里就简要的讲一下通过express框架进行前后端数据交互。
express是一个很轻量级的nodeJS框架。使用起来也比较简单。
要运用这个框架首先我们需要先将它下载下来：
```bash
npm install express --save
```
（*后面加了--save，是为了把模块保存到配置文件中，当项目移植的时候只需要npm install 就会把所有依赖的模块下载下来*）

之后就是搭建服务器了。
创建一个js文件
1, 首先引入框架
```javascript
const express=require('express');
```
<!-- more -->
2, 创建、搭建服务器
```javascript
let server=express();
```
3,设置一个端口进行监听
```javascript
server.listen(2333);
```
*端口号随便设，只要不超过65535就好*
4,设置中间量
这里我们可以使用三种依次是get/post/use。这里我用的是get方式
```javascript
server.get('/a',(request, response)=> {
	console.log(request.query);
	response.send("永远18岁");
    response.end(); 
});
```
5,设由于三次握手，我们还需要访问静态的html，让其得到后台的处理结果。
所以我们需要再引入一个模块：
```bash
npm install express-static --save
```
并在js中添加：
```javascript
const static = require('express-static');
```
有了它，我们就能访问html这类静态文件了。
6,设置文件权限。将前后端分离开来，前端文件都放在一个叫‘www’的文件夹中。
```javascript
server.use(static('www'));
```
7，前端交互（运用了JQ打包好的Ajax,这里就不细说了）
```html
    <script src="jquery-3.1.1.min.js"></script>
    <script>
        $.ajax({
            url:'/a',
            type:'POST',
            data:{
                name:'zyy',
                age:18
            },
            success:function(data){
              console.log(data);
            },
            error:function(){}
        }); 
    </script>
```
前台向后台发送data（永远18岁），
在后台接收到了数据，通过console.log(request.query);打印出来。
另外post的话，和get不同。
先下载
```bash
npm i body-parser --save
```
再加上下面两行代码，再在里面用：console.log(request.body);打印
```javascript
const body=require('body-parser');
server.use(body.urlencoded());
```
随后后台通过response.send("永远18岁");又向前台做出回应。前台通过Ajax，
```javascript
 success:function(data){
              console.log(data);//得到这条回应，并在浏览器控制台打印出来
              					//“永远18岁”
            }
```


这个就是基于NodeJs express框架 前后端数据交互3次握手典型的小Demo。


以上！
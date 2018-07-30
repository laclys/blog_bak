---
title: node服务器开发上线小记
tags: 
	- node
	- mongoDB 
---

说道node，之前其实也没少用。不过大部分都是自己搞着玩，
写个小爬虫或是在本地为自己的前端项目跑个接口。
实际开发生产中经验为零。正巧工作中自己负责的APP项目的新模块后台要独立出来，
借此机会使用走了一趟node开发到上线的全过程（express+mongoDB）。

<!-- more -->

### 开发
新模块开始的功能不太多，开发这块没有花太多时间。不过还是有一些值得记录的地方：

1. 摒弃callback，拥抱async/promisify。

node在8.X.X版本之后支持了async以及promisify的方式。callback hole确确实实令人不爽使用async
或者util.promisify会让代码优雅很多。

2. import代替require

在最新的node v9.5.0，依然没有支持这一特性。我们引入文件还是需要通过require的方式。
虽然不是很推荐，不过执意要在node中使用import这一特性的话。需要安装babel-cli，
进行转换 。

3.  处理图片存储这一块

公司用的图片存储用的是又拍云。这里需要的是，将图片上传至又拍云，得到云上的图片地址，
进而存储到数据库中。期初想的是在前端完成上传又拍云得到图片地址这一系列操作。
后端只接收图片地址这一个字符串，进行存储。虽说是有相关JS SDK。不过前端终究不安全，
有一些生成密锁/token之类的操作。还是通过form-data传到服务器这边进行操作符合逻辑。这里遇到了一个
小问题node这边怎么接受图片。经过一番尝试，这里使用了multer这个中间件。拿到form表单传过来的图片。

4. log

服务器当然要有log。这里使用了另一个中间件：log4js。
```javascript
// LOG配置
log4js.configure({
  appenders: [{
    type: 'dateFile',
    filename: 'logs/access.log',
    pattern: '-yyyy-MM-dd.log',
    alwaysIncludePattern: true,
    category: 'access'
  }]
})
// Log Middleware
app.use(log4js.connectLogger(log4js.getLogger('access'), { level: log4js.levels.INFO }))
```
这里部署上线的时候，其实还遇到了一个小问题。官方例子中type: 'Date File'。在服务器上执行。报错！查了一下issue，改成小写d，搞定。

5. mongoDB

又是一个部署服务器上出现的问题。在本地使用mongoDB没有设置相关的权限账号。服务器上运维给我了一个admin的root账号。一开始不太懂尝试连接建model。出了一大堆权限问题。和传统数据库不同：

1 MongoDB是没有默认管理员账号的，所以要先添加管理员账号，在开启权限认证。

2 切换到admin数据库，添加的账号才是管理员的账号。

3 用户只能在用户所在的数据库登录，包括管理员账号。

4 管理员可以管理所有的数据库，但是不能直接管理其它数据库，要先在admin数据库中认证才可以，也是为了 安全性考虑。

具体操作如下：

这里需要先通过mongo命令使用账号、密码连接线上的mongo。注意这里必须指定admin model。
```bash
mongo --username XXX --password XXX mongoDB线上地址/admin

```
之后通过use DATEBASE_NAME (use 如果数据库不存在，则创建数据库,否则切换到指定数据库）

show dbs看看创建成功没有。之后
```bash
db.createUser(
  {
    user: "lac",
    pwd: "123321",
    roles: [ { role: "readWrite", db: "XXX" } ]
  }
)
```
通过上面的命令 创建一个对应数据库，拥有读写权限的用户。

在node中，这样使用就好
```javascript
mongodb: 'mongodb://lac:123321@mongoDB线上地址:端口/所使用的DB名字'
```
搞明白了很简单。当时倒腾了好一阵orz

6. 413错误

    上线之后，上传图片/数据。response返回了413错误
    Request Entity Too Large。这里是由于服务器限制了上传文件的大小，解决办法就是： 在nginx.conf里http{}中加入：
    
    ```bash
    client_max_body_size 20m ;
    ```
  允许最大上传的大小设为20m。之后重启nginx即可。
  
  
7. pm2

    线上部署运行node 使用的是pm2
    
    一些常用命令：
    ```bash
    
     $ pm2 start app.js # 启动app.js应用程序
     $ pm2 start app.js --name="api" # 启动应用程序并命名为 "api"
     $ pm2 start app.js --watch # 当文件变化时自动重启应用
     $ pm2 start script.sh # 启动 bash 脚本

     $ pm2 list # 列表 PM2 启动的所有的应用程序
     $ pm2 monit # 显示每个应用程序的CPU和内存占用情况
     $ pm2 show [app-name] # 显示应用程序的所有信息

     $ pm2 logs # 显示所有应用程序的日志
     $ pm2 logs [app-name] # 显示指定应用程序的日志
     pm2 flush

     $ pm2 stop all # 停止所有的应用程序
     $ pm2 stop 0 # 停止 id为 0的指定应用程序
     $ pm2 restart all # 重启所有应用
     $ pm2 reload all # 重启 cluster mode下的所有应用
     $ pm2 gracefulReload all # Graceful reload all apps in cluster mode
     $ pm2 delete all # 关闭并删除所有应用
     $ pm2 delete 0 # 删除指定应用 id 0
     $ pm2 scale api 10 # 把名字叫api的应用扩展到10个实例
     $ pm2 reset [app-name] # 重置重启数量
    ```

以上~
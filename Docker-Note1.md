---
title: Docker Note
tags:  
	- docker
---

> 容器技术的一个实现

 ## container
 > 快速打包技术
 - 标准化
 - 轻量化
 - 易移植

### Linux Container(2008)
> Namespace和Cgroup(负责资源管理控制)两大机制保障

### 标准化
`docker != container`

- runtime spec(基本规范 eg：下载、创建容器)
- image spec(镜像的基本格式)


## Image VS Container

### Image
- Docker image是一个 `read-only` 文件
- 理解成一个模板
- 包含文件系统、源码、库文件还有依赖
- Image有分层的概念

<!-- more -->

### Container
> ”运行中的Docker Image“
- 实质是复制image并在image最上层加上一层`read-write`的层 （称之为`container layer`,容器层）
- 一个`Image`可以创建多个`Container`

## 获取
- 自己
- 从registry拉取(docker hub)

## basic
> docker container run nginx
可以去创建一个容器，如果没有nginx会去下载

> docker container ls
容器的列出(up)

> docker container stop  <name or ID> (ID可以只写前面)
容器的停止

> docker container ps -a or docker container ls -a
容器的列出(up和exit)(停止的也会展示)

> docker container rm <name or ID>
容器的删除

## tips
> docker container stop $(docker container ps -qa)
批量停止 `docker container ps -qa`打印出所有container ID传入$

> docker container rm $(docker container ps -qa)
同理批量删除 `docker container ps -qa`打印出所有container ID传入$、

* 不能直接删除正在运行的容器(先停止再删除或者 加`-f`强制)

## 容器两种模式attached和detached模式
(win不是完整的`attached`模式，`ctrl+c`不能把容器停掉)

detached模式 > docker container run -d -p 80:80 nginx (后台运行)

> docker attach <ID>
detached -> attached

(* 创建容器尽量使用`detached`模式)

## 容器交互
> docker container logs <ID>
查看log

>docker container logs -f <ID>
实时查看log

> docker container run -it ubuntu sh
创建一个容器并进入交互式模式 (这里创建了ubuntu sh)

> docker container exec -it
在一个已经运行的容器里执行一个额外的command

eg:
```bash
docker container run -d -p 80:80 nginx

docker exec -it <ID> sh

```

*`exit`退出

## Container vs VM
<img width="763" alt="截屏2021-06-29 下午11 04 59" src="https://user-images.githubusercontent.com/22010181/123828601-3f645980-d934-11eb-8919-5ad8fb42525b.png">

容器并不是mini的虚拟机：container其实就是进程。容器中的进程被限制了对CPU内存等资源的访问。当进程停止后，容器就退出

> docker container top <ID>
显示container运行了那些进程

## docker container run
- 在本地查找是否有nginx这个image镜像，但是没有发现

- 去远程的image registry查找nginx镜像（默认的registry是Docker Hub)

- 下载最新版本的nginx镜像 （nginx:latest 默认)

- 基于nginx镜像来创建一个新的容器，并且准备运行

- docker engine分配给这个容器一个虚拟IP地址

- 在宿主机上打开80端口并把容器的80端口转发到宿主机上

- 启动容器，运行指定的命令（这里是一个shell脚本去启动nginx）



## 镜像获取
- pull from registry (online) 从registry拉取(`dockerhub`)
  - public（公有）
  - private（私有）
- build from Dockerfile (online) 从Dockerfile构建
- load from file (offline) 文件导入 （离线）(`backup.tar`)

![docker-stages](https://user-images.githubusercontent.com/22010181/124139839-8c723800-daba-11eb-848a-e722126e8d04.png)

## Image操作

> docker image pull xxx
拉取镜像`xxx`

> docker image pull nginx:1.20.0
指定版本 拉取`nginx:1.20.0`

> docker image pull quay.io/bitnami/nginx
`Quay`上拉取镜像

> docker image ls
查看镜像

> docker image inspect <ID>
显示镜像更多信息

> docker image rm <ID>
删除镜像
(如果有正在使用的容器是无法删除的 必须docker container rm <ID>删除了容器才可以)

## Image导出、导入
> docker image save nginx<: version> -o nginx.image
导出

> docker image load -i ./nginx.image
导入

## Dockerfile
> Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. Using docker build users can create an automated build that executes several command-line instructions in succession.

- 是用于构建docker镜像的文件
- 里包含了构建镜像所需的“指令”
- 有其特定的语法规则

### example
hello.py
```py
print('hello docker!')
```
Dockerfile
```dockerfile
FROM ubuntu:21.04
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y python3.9 python3-pip python3.9-dev
ADD hello.py /
CMD ["python3", "/hello.py"]
```

- RUN: 指定镜像被构建时要运行的命令
- CMD: 指定容器被启动时要运行的命令
- ENTRYPOINT: 同 CMD ，但不会被 docker run -t 覆盖
- WORKDIR: CMD/ENTRYPOINT 会在这个目录下执行
- VOLUME
- ADD
- COPY


> docker image build -t hello .
构建名为`hello`的构建(-t是tag的意思)

```bash
➜  docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
hello        latest    1a963417de9a   43 seconds ago   206MB
```
构建成功

```bash
➜  docker run -it hello
hello docker!
```
执行成功！

## commit
docker提供了一个方法直接 通过一个`container`创建一个新的`image`
> docker container commit <ID> <REPOSITORY:[:tag]>


## 前端example
- 新建一个文件夹 `docker`
- 创建`dockerfile` & `index.html`
```dockerfile
FROM nginx
COPY ./index.html /usr/share/nginx/html/index.html
```
基于`nginx`这个镜像。将文件夹下的`index.html`拷贝到镜像`container`下的 `/usr/share/nginx/html/`下面

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hello Docker</title>
</head>
<body>
  Test
</body>
</html>
```
这里随便写了一个（用来替换默认的nginx欢迎页）

- > docker image build -t dockerTest . 

```bash
➜  docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
docker-test   latest    65a48f14f1c1   20 seconds ago   133MB

```
`docker-test`已经生成

- > docker container run -d -p 80:80 docker-test 运行容器指定端口80:80
<img width="383" alt="截屏2021-07-04 上午12 55 33" src="https://user-images.githubusercontent.com/22010181/124361635-90d95500-dc62-11eb-94fb-c6587a4bccc1.png">

成功！


以上`https://github.com/laclys/Front-End_practice/issues/153` docker学习汇总版
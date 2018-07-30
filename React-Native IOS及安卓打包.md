---
title: React-Native IOS及安卓打包
tags: 
	- 前端 
	- React
	- ReactNative
---

开发了快两个月的RN终于到了要打包发布的阶段。

网上有不少文章博客，还是RN更新太快的缘由，有些并不适用于当前版本。这里简单说一下吧。（这里用的是RN0.43.4）

### IOS

首先在项目根目录创建一个空文件夹，我这里创建的是：release_ios

之后执行打包命令（有点像webpack将JS代码压缩合并，并且导出所使用的静态资源）
```bash
react-native bundle --entry-file index.ios.js --platform ios --dev false --bundle-output release_ios/main.jsbundle --assets-dest release_ios/
```
#### 命令具体含义：
- --entry-file ,ios或者android入口的js名称，比如index.ios.js
- --platform ,平台名称(ios或者android)
- --dev ,设置为false的时候将会对JavaScript代码进行优化处理。
- --bundle-output, 生成的jsbundle文件的名称，比如 release_ios/main.jsbundle
- --assets-dest 图片以及其他资源存放的目录,比如 release_ios/

大概1，2min打包ok。在我们的新建文件夹中就会有相应的bundle文件。
![](/assets/blogImg/20170929-1.jpg)

<!-- more -->

在之后打开xCode。将bundle文件拖拽到项目中。（++点击options，一定要勾选Create folder references选项，将bundle文件夹添加到项目里，Xcode下该文件夹一定要是蓝色的。++）
![](/assets/blogImg/20170929-4.jpg)
修改AppDelegate.m中的代码,替换jsCodeLocation ，注释掉之前的jsCodeLocation 代码。
```object-c
 //jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"index.ios" fallbackResource:nil];
 +jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
```
![](/assets/blogImg/20170929-2.jpg)

最后将项目由debug状态改成release状态，Xcode-->Product-->Scheme-->Edit Scheme... 
（在此期间选择 Generic iOS Device ,修改Version和Build号）

![](/assets/blogImg/20170929-3.jpg) 

最后command+shift+k clean一下项目。选择Archice。就可以生成ipa包咯。

### Android

android这块打包很简单。这里主要说一下真机调试。走了不少弯路。（比ios方便很多。）
首先，在项目根目录下启动服务：
```bash
react-native start
```
手机打开开发者中的USB调试，链接上PC。
通过命令：
```bash
adb reverse tcp:8081 tcp:8081
```
将手机与rn的node服务链接。

最后在android studio中编译运行选择连接上的设备就ok咯。

安卓这块开发还是建议使用真机链接的方式。ios端模拟器相对流畅，安卓模拟器（这里mac pro13）卡的不要不要的。很影响开发效率。

打包的话 直接看官方文档就ok


以上
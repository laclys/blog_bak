---
title: React Native热更新一些命令
tags: 
	- 移动端 
	- ReactNative
---

## CodePush
巨硬提供的一套热更新策略。使用CodePush 可以进行实时的推送代码更新。CodePush 作为一个中央仓库，开发者可以推送更新 (JS, HTML, CSS and images)，应用可以从客户端 SDK 里面查询更新。

CodePush开源了react-native版本，react-native-code-push托管在GitHub上。

在公司开发的APP上，上周集成了热更新。碰到了一些小bug，热更新搞一哈，悄悄地把问题改掉咯。免去了发版的烦恼。（仅限于JS bundle部分，要是修改了原生，使用了link操作。还是得老老实实发版）
<!-- more -->
## CodePush的一些命令

### 账户相关
```
code-push app add 在账号里面添加一个新的app
code-push app remove 或者 rm 在账号里移除一个app
code-push app rename 重命名一个存在app
code-push app list 或则 ls 列出账号下面的所有app
code-push app transfer 把app的所有权转移到另外一个账号
code-push login 登陆
code-push loout 注销
code-push access-key ls 列出登陆的token
code-push access-key rm <accessKye> 删除某个 access-key
code-push app list 或则 ls 列出账号下面的所有app
```

### 打包发布
```
code-push release-react <appName> <platform> -t 版本 -d 环境 --des 描述
```
eg：
```
code-push release-react App-iOS ios -t 2.10.0 -d Production --des '一段描述'
//其中参数–t为二进制(.ipa与apk)安装包的的版本；–dev为是否启用开发者模式(默认为false)；–d是要发布更新的环境分Production与Staging(默认为Staging)；–des为更新说明；–m 是强制更新。
```
注意：热更新的版本必须和app版本相同才有效。高或者低都无效。

### 部署
```
// 清除历史部署记录
code-push deployment clear <appName> Production or Staging
// 列出应用的部署情况
code-push deployment ls <appName>
// 查看部署的key
code-push deployment ls <appName> -k 
// 部署记录历史
code-push deployment history uniApp-IOS Production or Staging
```


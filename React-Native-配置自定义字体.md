---
title: React Native 配置自定义字体
tags: 
	- 前端 
	- React
	- ReactNative
---

本想着配置自定义字体不是什么大坑，官网文档找了一下，没有。遂关键字：“React Native 配置自定义字体”这么一搜。都是一样的文章，作者名却是各式各样，真是天下文章大家抄。然而试了一下并不好用。

（这里使用最新的RN 0.48.4 测试）

这里想一下React Native最新的，最省事的配置自定自定义字体的方法：
1）随便下载一个字体（不要改名字，会有坑）我这里随便下了一个scratchmyback的英文字体。
    SCRATCHMYBACK.TTF

2）在项目根目录创建文件夹assets/fonts。并把刚才下载的字体放进去。

![](/assets/blogImg/20170924-1.jpg)

3）在Package.json告诉React Native 咱们自定义的字体在哪里：
```javascript
"rnpm": {
    "assets": [
	"./assets/fonts/"
    ]
}
```

<!-- more -->

4) react-native link （并不需要手动打开xcode，copy文件夹进项目）

5) IOS需要在Info.plist文件里添加将要使用的自定义字体：
```plist
<key>UIAppFonts</key>
<array>
   <string>SCRATCHMYBACK.TTF</string>
</array>
```
Android link操作自动把引入字体copy到了android/app/src/main/assets/fonts/文件夹下。确认一下就好。

6）现在就可以在香炉里通过fontFamily使用自定义字体咯。重新编译一下 大功告成~

![](/assets/blogImg/20170924-2.jpg)
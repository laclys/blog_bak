---
title: node图片合成
tags: 
	- node
---


最近在工作上有一个需求是在server(node.js)，将几张图片和文字合成。

经过google一番，尝试了3个库：

- node-images： 国人开发的一个轻量级，不需要额外安装依赖的图片处理库。可以实现图片大小，合并等功能。本打算使用它来完成需求。不过已经近一年不维护了。node 8.x版本跑步起来。看了一下issue，发现各个平台、系统的兼容性堪忧。还是不去踩这个坑了。另外其也不支持文字写入。

- node-canvas：功能很强大，搜到不少Demo，没有做深入调研。应该是可以实现需求。

- 终于到了重点：gm。nodeJs对GraphicsMagick和ImageMagick的封装。图片处理的老牌工具，包含各种各样的功能。由于两个工具本身都不是js实现，所以需要额外安装。这里就主要说说gm。

<!-- more -->

### install：
在安装gm之前，我们需要安装GraphicsMagick和ImageMagick
mac这里：
```bash
  brew install imagemagick
  brew install graphicsmagick
```
linux的话，拿apt-get安装就ok。

这两个安装完毕之后。在工程目录：
```bash
npm i gm --save
```
就ok

### usage：

我们现在想把图一（test.jpg)(600\*600）的右下角添加图二(1.jpg)(140\*140），使其合成一张图。
![](/assets/blogImg/20180310-01.jpg)
![](/assets/blogImg/20180310-02.jpg)
这里 我们使用gm的draw方法。
```javascript
const gm = require('gm')
gm('./test.jpg')
  .draw('image Over 460, 460, 140, 140 "./1.jpg"')
  .write(`./output/${Date.now()}.jpg`, function(err) {
    if (!err) {
      console.log('done')
    }else {
      console.log(err.message || "出错了！");
    }
  })

```
跑一下程序，output目录下就多出来一张合成的照片咯。当然gm还有很多种合成照片的方式：
compose/mosaic/append(api文档说的很细致这里就不展开了)

![](/assets/blogImg/20180310-03.jpg)

gm的APi文档地址是：[gm api文档](http://aheckmann.github.io/gm/)

图片和图片合成很简单之后我们还想添加一段文字,查阅api我们发现有这个方法：drawText

.drawText(x, y, text [, gravity])

如果没有gravity参数，那么就将text绘制到图片中的(x,y)坐标处。 
如果带有gravity参数，从(x,y)坐标开始到右下角构成的图片范围内，依据gravity含义绘制文字。

gravity的选项如下：

NorthWest, North, NorthEast, West, Center, East, SouthWest, South, or SouthEast

```javascript
gm('./test.jpg')
  .draw('image Over 460, 460, 140, 140 "./1.jpg"')
  .drawText(100, 100, 'wa~~~')
  .write(`./output/${Date.now()}.jpg`, function(err) {
    if (!err) {
      console.log('done')
    }else {
      console.log(err.message || "出错了！");
    }
  })
```
上述代码，我们想要在图片 左上为（0，0）。（100，100）的位置添加wa~
跑一下这是报了一个错：
```bash
Error: unable to read font `/usr/local/share/ghostscript/fonts/n019003l.pfb' @ error/annotate.c/RenderFreetype/1123: `(null)'
```
这是因为我们没有装ghostscript(一套建基于Adobe、PostScript及可移植文档格式（PDF）的页面描述语言等而编译成的免费软件。)
```bash
brew install ghostscript
```
安装好了在执行就ok了。
顺势我们再试试中文。
```javascript
.drawText(100, 100, '哇~~~')
```
乱码了。google一下发现gm、GraphicsMagick的使用中，中文乱码问题屡见不鲜。
解决方案总结起来就是1）utf-8编码；2）要指定中文字体库

这里我们下载了微软雅黑的ttf文件，放在根目录的font文件夹下.我们再添加一些字体属性：

```javascript
gm('./test.jpg')
  .draw('image Over 460, 460, 140, 140 "./1.jpg"')
  .drawText(100, 100, '哇')
  .fontSize(36)
  .fill('#fff')
  .font('./font/微软雅黑.ttf')
  .write(`./output/${Date.now()}.jpg`, function(err) {
    if (!err) {console.log('done')}else{
      console.log(err.message || "出错了！");
    }
  })

```
这样 白的的36号字的哇就出现在了图片上。
![](/assets/blogImg/20180310-04.jpg)

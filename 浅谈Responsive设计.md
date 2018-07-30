---
title: 浅谈Responsive设计
tags: 
	- 前端
	- 响应式
---

这两天一直在看CSS3相关的东西，提到CSS3有个很重要的特性就是Media Queries,说到Media Queries,到头来还是网页的响应式开发（*Responsive设计简单的称为RWD*）。

说到响应式开发，自己第一想到的就是这个带有‘栅格系统’的Bootstrap。它可以让你的网站在不同设备中展现不同的风格，版面不会因为屏幕大小变化而乱掉。说俗一点就是‘有求必应’。就这先前Bootstrap的使用经验，结合理论在这里笔记总结一下。

响应式设计需要满足以下三个条件：

> * 网站必须建立灵活的网格基础；

> * 引用到网站的图片必须是可伸缩的；

> * 不同的显示风格，需要在Media Queries上写不同的样式。
>   <!-- more -->
###响应式设计相关术语
1.**流体网络**
流体网格是一个简单的网格系统，这种网格设计参考了流体设计中的网格系统，将每个网格格子使用百分比单位来控制网格大小。这种网格系统最大的好处是让你的网格大小随时根据屏幕尺寸大小做出相对应的比例缩放。
2.**弹性图片**
弹性图片指的是不给图片设置固定尺寸，而是根据流体网格进行缩放，用于适应各种网格的尺寸。而实现方法是比较简单，一句代码就能搞定的事情。
```css
img {max-width:100%;}
```
(在ie8中存在问题=。=)
3.**媒体查询**
就是开头说到的CSS3强大的新扩展吧。使用这个属性可以让你的设计根据用户终端设备适配对应的样式。这也是响应式设计中最为关键的。可以说Responsive设计离开了Medial Queries就失去了他生存的意义。
4.**主要断点**
对于Responsive设计中是一个很重要的一部分。简单的描述就是，设备宽度的临界点。在Media Queries中，其中媒体特性“min-width”和“max-width”对应的属性值就是响应式设计中的断点值。简单点说，就是使用主要断点和次要断点，创建媒体查询的条件。而每个断点会对应调用一个样式文件（或者样式代码）
![](/assets/blogImg/20170111_1.jpg)
上图的style.css样式文件运用在Web页面中，但这个样式文件包括了所有风格的样式代码，也就是说所有设备下显示的风格都通过这个样式文件下载下来。

###响应式设计-----meta标签
最后还有一个不可或缺的东东，那就是meta标签。
eg：
```html
<meta name=”viewport” content=”” />
```
其中content属性用来处理可视区域。属性值这块就不一一列举了。
在实际项目中，为了让Responsive设计在智能设备中能显示正常，也就是浏览Web页面适应屏幕的大小，显示在屏幕上，可以通过这个可视区域的meta标签进行重置，告诉他使用设备的宽度为视图的宽度，也就是说禁止其默认的自适应页面的效果，具体设置如下：
```html
<meta name=”viewport” content=”width=device-width,initial-scale=1.0” />
```
###不同设备的分辨率设置
在网上找的以后应该有用！
1.1024px显屏
```css
@media screen and (max-width : 1024px) {                    
/* 样式写在这里 */          
}     
```
2.800px显屏
```css
@media screen and (max-width : 800px) {              
/* 样式写在这里 */          
}    
```
3.640px显屏
```css
@media screen and (max-width : 640px) {              
/* 样式写在这*/            
}     
```
4.iPad横板显屏
```css
@media screen and (max-device-width: 1024px) and (orientation: landscape) {              
/* 样式写在这 */            
} 
```
5.iPad竖板显屏
```css
@media screen and (max-device-width: 768px) and (orientation: portrait) {         
/* 样式写在这 */            
}     
```
6.iPhone 和 Smartphones
```css
@media screen and (min-device-width: 320px) and (min-device-width: 480px) {              
/* 样式写在这 */            
}    
```
![](/assets/blogImg/20170111_2.jpg)
结尾测试一下新blog的图片功能好不好使。=。=
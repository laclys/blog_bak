---
title: CSS混合模式
tags: 
	- CSS 
	- 前端
---

前几天在一个一个技术网站看到了一篇通过CSS3特性，完成酷炫效果的文章。在公司搞App移动端以来，好一阵没有写纯粹的html和css了。今儿学到了一个以前不知道的CSS3属性：混合模式。

![](/assets/blogImg/20171114-01.jpg)

### 关于混合模式
CSS3出现了两个与混合模式有关的属性，mix-blend-mode和background-blend-mode.

兼容性如下：
![](/assets/blogImg/20171114-02.jpg)
可见，Chrome和FireFox已经支持了，并且不需要使用前缀。

<!-- more -->

### mix-blend-mode

可使用的值：
```css
mix-blend-mode: normal;          //正常
mix-blend-mode: multiply;        //正片叠底
mix-blend-mode: screen;          //滤色
mix-blend-mode: overlay;         //叠加
mix-blend-mode: darken;          //变暗
mix-blend-mode: lighten;         //变亮
mix-blend-mode: color-dodge;     //颜色减淡
mix-blend-mode: color-burn;      //颜色加深
mix-blend-mode: hard-light;      //强光
mix-blend-mode: soft-light;      //柔光
mix-blend-mode: difference;      //差值
mix-blend-mode: exclusion;       //排除
mix-blend-mode: hue;             //色相
mix-blend-mode: saturation;      //饱和度
mix-blend-mode: color;           //颜色
mix-blend-mode: luminosity;      //亮度

mix-blend-mode: initial;         //初始
mix-blend-mode: inherit;         //继承
mix-blend-mode: unset;           //复原
```
有了mix-blend-mode，我们能更方便的使用一些文字特效。

\* mix-blend-mode默认情况下是会混合所有比起层叠顺序低的元素的，如果我们希望值混合某一两个元素，而不是全部，该怎么办呢？可以试试使用**CSS3 isolation:isolate**

#### background-blend-mode
顾名思义 背景混合，属性支持和mix-blend-mode一致，需要注意的是，只能是background属性中的背景图片和颜色混合，而且只能在一个background属性中。



---


---
title: SASS基础语法总结
tags: 
	- 前端 
	- SASS
---

### SASS

CSS预处理器。一种基于CSS之上的高级语言，其目的是使得CSS开发更灵活和更强大。

-------

### 语法

1.变量

> $开头紧跟变量名，变量名值以：分隔，！表示默认值 。$blue : #1875e7;　

``` scss
$blue : #1875e7;　
div {
color : $blue;
}
```

2.导入@import；

> 导入其他sass文件。（优于css的@import）

```scss
@import "path/filename.scss";
```

3.计算功能；

> 允许在代码中使用算式

```scss
　　body {
　　　　margin: (14px/2);
　　　　top: 50px + 100px;
　　　　right: $var * 10%;
　　}
```
<!--more-->

4.嵌套；

> 可以在代码中进行嵌套（自己尝试sass之后，有了嵌套这个语法，可以省去起名的麻烦，很方便的功能=。=）

```scss
　　div {
　　　　hi {
　　　　　　color:red;
　　　　}
　　}
```
>在嵌套里可以使用&引用父级元素。
```scss
a{ 
        &:hover{ 
            color:#fff;
             } 
        &:after{
        content:'';
         clear:both;
          display:block;
           } 
}
```
5.mixin(宏)；
> 类似其他语言的函数。可以重用代码块。
> 使用@mixin命令，定义一个代码块。
```scss
　　@mixin fl {
　　　　float: left;
　　}
```
> 使用@include命令，调用这个mixin。
```scss
　　div {
　　　　@include fl;
　　}
```
6.继承
```scss
//sass 
.class1{ border: 1px solid #ddd; } .class2{ @extend .class1; font-size:120%; } 
//编译后css 
.class1,.class2{ border: 1px solid #ddd; } .class2{ font-size:120%; }
```
7,IF
```scss
p{ 
    @if 12+2==14
    { color:#fff;
     }@else{ 
    color:#000; 
    } 
}
```
8,循环
>for循环
```scss
@for $i form 1 to 10{
    .border-#{$i}{
        border: #{$i}px solid blue;
    }
}
```
>while循环
```scss
$i:6; 
@while $i>0{ 
    .item-#{$i}{ 
        width:4px * $i;
         } 
         $i: $i - 2; 
}
```
>each命令
```scss
@each $member in a,b,c,d{
     .#{$member}{ 
         background-image: url("img/#{$member}.jpg");
          } 
    }

```
###总结
差不多常用的就这些，个人觉得有了嵌套，可以让代码更有逻辑，敲起来顺手很多。在开发中比较重要的就是@mixin的使用和@extend的使用。可以提高代码的复用率。
就这样。[一个用sass写的小DEMO][1]


####github上第一篇blog，纪念一下！。


[1]: https://github.com/laclys/HTML-CSS_practice/tree/master/sass_web
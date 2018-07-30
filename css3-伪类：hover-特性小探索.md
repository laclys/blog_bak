---
title: css3 伪类：hover 特性小探索
tags: 
	- 前端 
	- css3
---

伪类在css3中相对还是很容易理解，也正是因为他很容易理解，平时也不纠结。不过前两天看到这个写法，还是小懵逼了一下：
```css
.a .b:hover{...........}; 
.a:hover .b{...........}; 
```
这两个有什么去别捏，不费事就写了两个demo：
 <!-- more -->
#### 第一个：
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        div {
            width: 100px;
            height: 100px;
            float: left;
            border: 1px solid darkblue;
            text-align: center;
            line-height: 100px;
        }
        .a .b:hover{
             background: red;
        }
    </style>
</head>

<body>
    <div class="a">
        A
        <div class="b">
            B
        </div>
    </div>
</body>

</html>
```
这是第一种写法的例子，这很好理解，当鼠标移入b时，b的背景颜色变成红色。
![](/assets/blogImg/20170218-1.jpg)
#### 第二种：
css部分改为了：
```css
    .a:hover .b{
        background: red;
    }
```
这是第二种写法的例子，它所表示的是当鼠标移入a时，b的背景颜色变成红色；同时，当鼠标移
入b时，b的背景颜色也会变成红色。（当然b一定要是a的子元素才行！）
![](/assets/blogImg/20170218-2.jpg)
这样就看出区别了，其实仔细想想，归根结底还是css选择器匹配元素不一致导致的。不能被表象迷惑= =。

ps：话说prtSc+alt这截图这截不出鼠标，很尴尬= =。
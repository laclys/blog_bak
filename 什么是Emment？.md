---
title: HTML/CSS代码快速编写---Emmet
tags: 
	- 前端
	- Emmet
---
最近学习前端，一开始用sublime，现在转到vscode上。它们通过安装插件都支持Emmet的HTML语法规则。在这里，一边自己学习一边分享一下啊。
### 什么是Emment？
*Emmet的前身是大名鼎鼎的Zen coding，如果你从事Web前端开发的话，对该插件一定不会陌生。它使用仿CSS选择器的语法来生成代码，大大提高了HTML/CSS代码编写的速度。*
eg：输入!,然后按Tab键：
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    
</body>
</html>
```

- html:5 或!：用于HTML5文档类型
- html:xt：用于XHTML过渡文档类型
- html:4s：用于HTML4严格文档类型

### Emmet支持的简写规则
1. E 代表HTML标签。

2. E#id 代表id属性。

3. E.class 代表class属性。

4. E[attr=foo] 代表某一个特定属性。

5. E{foo} 代表标签包含的内容是foo。

6. E>N 代表N是E的子元素。

7. E+N 代表N是E的同级元素。

8. E^N 代表N是E的上级元素。

<!--more-->
#### 后代
缩写：nav>ul>li

```html
<nav>
    <ul>
        <li></li>
    </ul>
</nav>
```
#### 兄弟
缩写：div+p+bq

```html
<div></div>
<p></p>
<blockquote></blockquote>
```

#### 上级

缩写：div+div>p>span+em^bq

```html
<div></div>
<div>
    <p><span></span><em></em></p>
    <blockquote></blockquote>
</div>
```

#### 分组：()

缩写：div>(header>ul>li*2>a)+footer>p
```html
<div>
    <header>
        <ul>
            <li><a href=""></a></li>
            <li><a href=""></a></li>
        </ul>
    </header>
    <footer>
        <p></p>
    </footer>
</div>
```
缩写：(div>dl>(dt+dd)*3)+footer>p

```html
<div>
    <dl>
        <dt></dt>
        <dd></dd>
        <dt></dt>
        <dd></dd>
        <dt></dt>
        <dd></dd>
    </dl>
</div>
<footer>
    <p></p>
</footer>
```

#### 乘法：*

缩写：ul>li*5

```html
<ul>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
    <li></li>
</ul>
```

#### 自增符号：$
缩写：ul>li.item$*5

```html
<ul>
    <li class="item1"></li>
    <li class="item2"></li>
    <li class="item3"></li>
    <li class="item4"></li>
    <li class="item5"></li>
</ul>
```

缩写：ul>li.item$$$*5

```html
<ul>
    <li class="item001"></li>
    <li class="item002"></li>
    <li class="item003"></li>
    <li class="item004"></li>
    <li class="item005"></li>
</ul>
```
#### ID和类属性
缩写：form#search.wide

```html
<form id="search" class="wide"></form>
```

#### 自定义
缩写：p[title="Hello world"]

```html
<p title="Hello world"></p>
```
#### 文本：{}
缩写：a{Click me}
```html
<a href="">Click me</a>
```
-----------
差不多常用的就这些。=。=
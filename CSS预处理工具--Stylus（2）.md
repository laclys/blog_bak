---
title: CSS预处理工具--Stylus（2）
tags: 
	- 前端 
	- Stylus
	- CSS
---

#### 内建函数：
- 颜色函数： darken/lighten
```stylus
.btn-primary
  background-color $primary-color
  border-color darken($primary-color, 15%)

.link
  color red

  &:hover
    color lighten(red, 10%)
```
<!-- more -->

- 嵌入图片：embedurl
```
background: embedurl('logo.png')
// => background: url("data:image/png;base64,…")

background: embedurl('logo.svg', 'utf8')
// => background: url("data:image/svg+xml;charset=utf-8,…")
```
- 添加前缀：+prefix-classes(prefix)
```stylus
+prefix-classes('.tqb-dp-')
  .header
    height 2.5rem

    .back-button
      color white
```
编译为：
```css
.tqb-dp-header {
  height: 2.5rem;
}
.tqb-dp-header .tqb-dp-back-button {
  color: white;
}
```

生态：
- NIB

Nib 是 Stylus 样式库，用于将样式进行兼容性处理
类似 Compass

position:
```
#back-to-top
  fixed bottom right
```
编译为：
```css
#back-to-top {
  position: fixed;
  right: 0;
  bottom: 0;
}
```
渐变色：
```
@import 'nib'

body
  background linear-gradient(top, white, black)
```
编译为：
```
body {
  background: -webkit-gradient(linear, left top, left bottom, color-stop(0, #fff), color-stop(1, #000));
  background: -webkit-linear-gradient(top, #fff 0%, #000 100%);
  background: -moz-linear-gradient(top, #fff 0%, #000 100%);
  background: linear-gradient(top, #fff 0%, #000 100%);
}
```

### CSS小技巧~
固定比例的容器：
```stylus
.intrinsic-ratio-box
  height 0
  padding-bottom 20%
  position relative

  .real-container
    absolute top left
    width 100%
    height 100%
```
视觉上不可见:

屏幕上不可见，但对搜索引擎和阅读器可见。
```stylus
.visually-hidden
  border 0
  clip rect(0,0,0,0)
  position absolute
  width 1px
  height 1px
  margin -1px
  overflow hidden
  padding 0
``
```
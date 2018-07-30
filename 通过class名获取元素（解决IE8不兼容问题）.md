---
title: 通过class名获取元素（解决IE8不兼容问题）
tags: 
	- 前端 
	- javascript
---

Js中获取元素通常有三种方式通过ID（getElementById）,标签（getElementsByTagName）以及CLASS名（getElementsByClassName）。前两种方式最为常用，在Chrome，FireFox以及低版本IE8下都好使（win10自带的ie只支持模拟到IE8，再古老的版本直接忽略好了）。可是CLASS名获取元素时，在IE8下出现这样的问题：
**对象不支持“getElementsByClassName”属性或方法**

<!-- more -->

因此在ie8下使用CLASS名获取元素时，需要我们自行解决兼容问题。
eg：在下面这个简单的无序列表，有序列表嵌套li的简单HTML页面中，让ul标签下的class为red的li标签背景变红

```html
<body>
    <ul>
        <li class="red"></li>
        <li></li>
        <li class="red2 blue"></li>
    </ul>
    <ol>
        <li class="red"></li>
        <li></li>
        <li></li>
    </ol>
</body>
```
解决方案如下：
```javascript
        //oParent：获取元素的父级对象；sClass：要获取的CLASS名（字符串）
        function getByClassName(oParent, sClass) {
            //当getElementsByClassName方法可以用时，直接使用这个方法
            if (oParent.getElementsByClassName) {
                return oParent.getElementsByClassName(sClass);
            } else {
                var aEl = oParent.getElementsByTagName('*');
                var arr = [];
                for (var i = 0; aEl.length; i++) {
                 //声明一个临时数组，把每一个元素获取过来的className存起来
                    var temp = aEl[i].className.split(' ');
                    //在temp中找class里包括red的元素，如果存在则true。让                     //这个元素背景色变红
                    if (findInArr(sClass, temp)) {
                        arr.push(aEl[i]);
                    }
                }
                return arr；
            }
        }
        //函数：查找item在arr中存不存在
        function findInArr(item, arr) {
            for (var i = 0; i < arr.length; i++) {
                if (arr[i] == item) {
                    return true;
                }
            }
            return false;
        }
```
github：github.com/laclys/HTML-CSS-JS_practice/blob/master/JS/classname获取 封装（对应ie8--）.html
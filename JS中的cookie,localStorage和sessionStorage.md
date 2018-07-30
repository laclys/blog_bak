---
title: JS中的cookie,localStorage和sessionStorage
tags: 
	- 前端 
	- javascript
---
说到cookie通俗一点就是服务器留在用户计算机中的小文件。
在JS中创建cookie很简单，比如：document.cookie=123 这样就存储了一条没有名字的cookie。
cookie的存储都是字符串，如果存储一条名字是name，值是‘lac’的cookie只需要：document.cookie='name=lac'即可！取出存储的cookie也只需要：
document.cookie就可以取出全部的cookie字符串了。
cookie里除了名字和值还有expires和path很重要。

**expires** 默认是session：浏览器和服务器进行的一次通话  浏览器关闭一次会话结束。
当然也可以设置时间。expires=事件对象；

**path** cookie存储的位置 需要注意的是：<u>内层文件存的cookie外层文件取不到，内层文件中可以取到外层文件的cookie</u>在一般工作中，一套cookie存储在一层目录中便于管理。即：path=/；
下面是自己封装的setCookie(),getCookie()以及removeCookie();
<!-- more -->
code如下：
```javascript
//设置cookie
function setCookie(sName, sValue, iDay) {
    if (iDay) {
        var oDate = new Date();
        oDate.setDate(oDate.getDate() + iDay);
        document.cookie = `${sName}=${sValue};path=/;expires=${oDate}`;
    } else {
        document.cookie = `${sName}=${sValue};path=/`;
    
    }
}
// 获取cookie
function getCookie(sName) {
    var str = document.cookie;
    var arr1 = str.split('; ');
    for (var i = 0; i < arr1.length; i++) {
        var arr2 = arr1[i].split('=');
        if (arr2[0] == sName) {
            return arr2[1];
        }
    }
    return '';
}
//删除coocookie
function removeCookie(sName){
    setCookie(sName,'',-1);
}
```
有了cookie就能解决很多问题了（ps：cookie操作必须在服务器环境）
例如直接自己做过拖拽小例子。现在每次打开小方块儿都在起始位置，关闭浏览器之后再打开。没有保存关闭之前他所在的位置。
现在有了cookie。就很简单可以实现关闭页面再打开依然是我们上一个关闭前的位置。
code：
https://laclys.github.io/HTML-CSS-JS_practice/JS/cookie/drag.html
在抬起鼠标事件下
```javascript
setCookie('pos', `[${oBox.offsetLeft},${oBox.offsetTop}]`,3);
```
再在window.onload下获取元素之后，获取出来
```javascript
var arr = eval(getCookie('pos'));
    if (arr) {
        oBox.style.left = arr[0] + 'px';
        oBox.style.top = arr[1] + 'px';
}
```
这样在这三天内（cookie设置了保存三天），移动小方块关闭浏览器，再打开小方块都会在关闭前的位置了。

**下面再来说说cookie的缺点**
1, 会过期；
2, 容量小（历史问题吧）只有4k,一般小于20条。=；
3, 每次向服务器发送请求时，都会向服务器发送cookie；（这点很致命）

###  localStorage / sessionStorage
下面再说说localStorage / sessionStorage这两个高级浏览器才拥有的好东西。为什么说是好东西，cookie的缺点全部干掉，变成优点。
localStorage：
>*  使用方便 不用封装函数；
>*  5M；
>*  不会向服务器发送请求；
>*  使没有过期；

使用的话：
        存：  localStorage.setItem(name.value);
        取：  localStorage.getItme(name);
        删除  localStorage.removeItem(name);
        全部删除 localStorage.clear();
是不是很简单。
sessionStorage：
和localStorage一样就是在一个会话session中关闭浏览器就消失。使用的话也和localStorage一样：
        存：  sessionStorage.setItem(name.value);
        取：  sessionStorage.getItme(name);
        删除  sessionStorage.removeItem(name);
        全部删除 sessionStorage.clear();

高级浏览器方法无限好，还是那句话：干掉IE人人有责！


以上！


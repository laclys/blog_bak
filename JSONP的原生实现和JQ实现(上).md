---
title: JSONP的原生实现和JQ实现(上)
tags: 
	- 前端 
	- jsonp
	- javascript
---


### JSONP
JSONP，全称：JSON with padding乍一看应该是和用来数据解析描述的JSON的某个远房亲戚。通过学习，**这两者本质上完全是不同的东西**。

JSONP的概念在这就不赘言。自己的理解，由于Ajax直接请求文件数据时，跨域无权访问（为了安全）。为了解决跨域问题，我们发现在页面调用JS文件时不受跨域影响（例如，我们在任意文件中都可以引入jq文件，从而使用jq开发页面），因此通过把数据装在一个js格式文件里，在客户端进行处理。这就是JSONP的原理。JSONP允许用户传递一个callback参数给服务器，之后服务器返回数据时通过这个参数的函数包裹上数据传给客户端。

下面不多说废话，下面用原生和jq分别写两个小获取baidu联想词条的小demo。
想要的效果就是当我在搜索条输入‘a’时，出现下拉联想列表（baidu的）
![](/assets/blogImg/20170223-1.jpg)
 <!-- more -->
#### 首先是原生实现方式：
我们先通过浏览器得到这一个数据请求。（在调试窗口的network中）
![](/assets/blogImg/20170223-2.jpg)
![](/assets/blogImg/20170223-3.jpg)
得到了请求：
```html
Request URL:https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?wd=a&json=1&p=3&sid=1424_21114_17001_21930_22036&req=2&csor=1&cb=jQuery1102023194193176203193_1487780037910&_=1487780037913
```
经过分析wd=a是我们联想的a（故这儿可以放我们想要联想的值）；cb是callback回调函数。把其他的都干掉：
https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?wd=a&cb=show
show随便起的回调函数名字。
之后代码如下：
```javascript
        function show(data) {
            var oUl = document.getElementById('ull');
            var arr = data.s;
            oUl.innerHTML='';
            for(var i=0;i<arr.length;i++){
                var oLi=document.createElement('li');
                oLi.innerHTML=arr[i];
                oUl.appendChild(oLi);
            }
        }

        window.onload = function () {
            var oT = document.getElementById('inp');
            oT.oninput = function () {
                var str = oT.value;
                var oScript = document.createElement('script');
                oScript.src = `https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?wd=${str}&cb=show`;
                document.head.appendChild(oScript);
            }
        };
```
html
```html
<body>
    <input type="text" name="" value="" id="inp">
    <input type="button" name="" value="baidu" id="inp">
    <ul id="ull">

    </ul>
</body>
```
当文本框有键盘输入事件时，我们在head标签下面动态的创建了一个script标签，标签的src中是放入了我们的请求wd，之后cb（回调函数）show来接受返回的data。并存储在ul的li下。效果如下：
![](/assets/blogImg/20170223-4.jpg)
![](/assets/blogImg/20170223-5.jpg)
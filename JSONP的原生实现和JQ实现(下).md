---
title: JSONP的原生实现和JQ实现(下)
tags: 
	- 前端 
	- jsonp
	- javascript
---

#### JQ实现方式：
JQ实现JSONP的时候看起来和ajax调用方式很像，目的也一样，都是请求url，染过获得服务器的数据之后进行处理。但两者完全是两个不同的东西。只是JQ这个框架将jsonp作为ajax的一种形式进行了封装。
实质上：ajax的核心是通过XmlHttpRequest获取非本页内容，而jsonp的核心则是动态添加script标签来调用服务器提供的js脚本。

ajax与jsonp的区别不在于是否跨域，ajax通过服务端代理一样可以实现跨域，jsonp本身也不排斥同域的数据的获取。
话不多说，下边是jq的实现，用过console.log得到目标数据：
```javascript
  $(function () {
            $('#inp').keypress(function () {
                var str = $(this).val();
                $.ajax({
                    url: 'https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su',
                    data: {
                        wd: str
                    },
                    dataType: 'jsonp',
                    jsonp: 'cb',
                    success: function (data) {
                        console.log(data);
                    },
                    error: function () {
    
                    }
    
                });
            });
        });
```
![](/assets/blogImg/20170223-6.jpg)


以上！

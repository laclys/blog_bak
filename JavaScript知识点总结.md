---
title:  JavaScript知识点总结1
tags: 
	- javascript
	- 前端
---
近期学习js知识的总结，，还会有2，3，4····
#### 事件：

onclick,onmouseover,onmouseout,onmouseup,onmousedown,onchange，onload

#### 获取元素：

通过id获取元素 ：document.getElementById();

通过Tag获取元素： document.getElementsByTagName();

#### 函数：

定义函数：function 函数名(){}

执行函数：谁调用了这个函数就在谁那里执行。

#### 变量：

var 变量名=变量值

".":只能操作已存在的属性。

"[]":可以接受一个变量，点能够操作的中括号都能操作。

#### 循环：

for：
```javascript
    //初始值var i=0，
    //条件i<6，
    //自增i++ 代表每次加1
    for(var i=0;i<6;i++){
        alert(i);
    }
```

while:
```javascript
    //初始值
    var i=0;
    //条件
    while(i<6){
        alert(i);
        //自增
        i++;
    }
```

<!--more-->

#### this：

表示当前发生事件的元素。



#### 变量类型：

**判断变量类型用typeof**

1,字符串：string

2,数字number（包含NaN）

3,布尔值：boolean

4,未定义:undefined(1访问一个不存在的属性2变量定义未赋值）

5函数：function

6,对象：Object

#### 数据类型转换：

parseInt();parseFloat();Number();

*Number('12.5abc')=>NaN     parseInt('12.5abc')=>12.5

#### 运算符：

1,算数运算符：+,-,*,/,%
2,赋值运算符：=，+=，-=，*=，/=,%=
3,比较运算 ==  >,>=,<,<=
​	==相等  ！=不等于
​	===相等 ！==不等于
逻辑运算：||或 &&与 ！取反

#### 流程控制if
 1，if(条件){
条件满足就走语句一
}else{
条件不满足就走语句二
}
简写：三目：条件？语句一：语句二；
2，if(条件){
语句；
     }
简写：条件&&语句；
3，
if(条件一){
语句一
}else if(条件二){
语句二
}else{
语句三
}
####switch
switch(条件){
```javascript
 case 条件1：
      语句一；
 break；
 case 条件2：
      语句二
   break；
 default:
      默认语句
```
}

break:中断整个程序，不再执行
continue：中断此次程序，继续下一次程序

#### 判断真假:

真：非零数字，非空字符串，true，非空对象；

假：数字0，NaN，空字符串，false，undefined，null；

#### 获取非行间样式：
```javascript
window.onload=function(){
    var oBox=document.getElementById('box');
    oBox.onclick=function(){
        //getComputedStyle(obj,false);高版本
        //obj.currentStyle；ie系列
        //alert(getComputedStyle(oBox,false));在ie8直接报错
        alert(oBox.currentStyle);//在chrome和ff是undefined
        if(oBox.currentStyle){
            alert(oBox.currentStyle.width);
        }else{
            alert(getComputedStyle(oBox,false).width);
        }
    }
}
```

#### eval():

可将字符串转化成js可执行代码。很强大，容易造成eval注入问题

#### 定时器：

##### 连续不断执行：

var timer = setInterval(函数，时间)

清空定时器：clearInterval(timer);

##### 只执行一次：

setTimeout(函数，时间)

#### 时间对象：
```javascript
var oDate=new Date();
var iY=oDate.getFullYear();
var iMon=oDate.getMonth()+1;
var iD=oDate.getDate();
var iWeek=oDate.getDay();
var iH=oDate.getHours();
var iM=oDate.getMinutes();
var iS=oDate.getSeconds();
//设置时间
var oDate=new Date();
oDate.setFullYear(年，月，日)
oDate.setHours(时，分，秒，毫秒)
//时间戳
var time=oDate.getTime();
```

#### 封闭空间

解决变量名冲突；解决了i的问题；（循环里加事件，事件里的i不好使；循环里加定时器，i也不好使）

eg：
```javascript
 (function(a){
        alert(a);
    })(12);
```

#### 设置样式：

1）用with语句：
```javascript
with(oBox.style){
     width='400px',
     height='400px',
     background='red'
}
```
2）cssText:批量设置样式
```javascript
oBox.style.cssText='width:400px; height:400px; background:red';
```

3）setStyle封装函数：
```javascript
function setStyle(obj,name,value){
    obj.style[name]=value;
}
```
####字符串
1，str.charAt(下标) 获取字符串中的某一项
2，str.indexOf(‘小字符串’) 获取某个小字符串在字符串中出现的位置
<u>	1）从左往右开始找，找到一个就不继续往下找了</u>
<u>	2）区分大小写</u>
<u>	3)如果没有找到，就返回-1</u>
eg：
```javascript
//获取浏览器信息，通过indexOf判断里面有没有MSIE 10.0，如果等于-1，代表没有
if(window.navigator.userAgent.indexOf('MSIE 10.0')!=-1){
    alert('ie10');
}else{
    alert('不是ie10');
}
```
3，str.lastIndexOf（）
<u>	1）从右往左找</u>
<u>	2）区分大小写</u>
<u>	3）如果没找到就返回-1</u>
4，str.toLowerCase()转小写
5，str.toUpperCase（）转大写`
6，str.substring(起始位置下标，结束位置下标) 截取字符串 （不包括结束位置）
7，str.startsWith(小字符串)
8，str.endsWith(小字符串 )
9，str.split(切割方式)切割字符串，把字符串转化成数组
	数组转字符串arr.join(切割方式)
#####字符串比较：
1，字符串数字
​	a)两个都是数字字符串类型，一位一位进行比较
​	b)其中有一个是数字类型，会做隐式类型转化，转化成数字进行比较
2，单词 字典序
3，汉字没规律
​	unicode 

#### 数组：

##### 创建数组：
 var arr=[];
 var arr = new Array();
#####数组函数：
arr.push()  往数组后面添东西，会返回新的数组的长度
arr.unshift() 往数组前面添东西，会返回新的数组的长度
arr.pop() 从数组后面删除，会返回被删掉的数据
arr.shift() 从前面删除，会返回被删掉的数据
arr.splice(开始位置,删除长度,元素1,元素2)
arr.reverse()翻转
arr.sort() 排序
arr1.concat(arr2) 数组合并
arr.join()将数组转化成字符串

####数学方法：
Math.random()随机数0-1，不包括1
Math.ceil()向上取整
Math.floor（）向下取整
Math.round()四舍五入
Math.abs()绝对值
Math.max()取最大值
Math.min()取最小值
Math.sqrt()开平方
Math.pow(n，m)n的m次方







---
title: VueJS组件化开发实践总结
tags: 
	- 前端
	- Vue
---

## 前言
接触Vuejs，好一阵了。清明节前在慕课网学了一门课。前前后后花了40多个小时琢磨，搭建起了组件化开发的饿了么SPA。这40小时收获了很多，也踩了不少坑。这里总结一下。

源码： https://github.com/laclys/vueApp

## 技术栈

VueJS+Vue-resource+Vue-router+Sass+webpack+ES6

## 具体细节

### 目录结构
![](/assets/blogImg/20170409-01.jpg)

common: 公共文件，包含图标字体文件、一些公共JS方法、Sass的mixin(这里包含移动端一像素、移动端DPR等，后面再提)
components：公共组件
	包含：
	cartcontrol:页面底部购物车组件
	food：商品详情也组件
	goods：商品列表页组件
	header：头部组件
	ratings：评论列表页组件
	seller：商家页组件
	shopcart：购物车组件
	split：空隙行组件
	star：评价星星组件
App.vue: 页面入口app组件（组件头头，所有组件都依附于它。)
main.js: VueJs入口文件,加载路由,公共组件,组件状态。

## 收获

比较零散一条一一条总结：

 <!-- more -->

- ​Vue的核心思想就是数据驱动，省去了操纵DOM的步骤，我们所做的一切都是为了改变数据。数据（model）改变/驱动视图（view）自动更新。
- 组件化的意义：扩展HTML元素，封装可重用的代码。
- DPR--量化屏幕的物理分辨率和显示清晰度。例如iphone6的DPR为2，6 plus/7是3。一般UI给的psd图都是基于DPR为2做的，所以移动端的设计稿，像素尺寸都是两倍或三倍。（这里提一下一般只针对ios，安卓采用1倍布局方案）
- 移动端实现1px：利用伪类+y轴缩放。味蕾相对于元素绝对定位后，给伪类1px的边框，然后根据设备最小dpr进行scaleY。
```css
@mixin border-1px($color) {
    position: relative;
    &:after {
        display: block;
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
        border-top: 1px solid $color;
        content: '';
    }
}
// DPR1.5时候
@media (-webkit-min-device-pixel-ratio:1.5),
(min-device-pixel-ratio:1.5) {
    .border-1px {
        &:after {
            -webkit-transform: scaleY(0.7);
            transform: scaleY(0.7);
        }
    }
}
// DPR2时候
@media (-webkit-min-device-pixel-ratio:2),
(min-device-pixel-ratio:2) {
    .border-1px {
        &:after {
            -webkit-transform: scaleY(0.5);
            transform: scaleY(0.5);
        }
    }
}
```
- ​Vue组件中不能直接使用src，要通过v-bind绑定使用。
- CSS消除inline-block下两个行间样式之间的留白。两种办法一种是两个标签不换行紧紧贴着；二是给他们的父级加font-size：0。这样就可以解决了。
- iphone一般的默认字体：
  'PingFang SC', 'STHeitiSC-Light', 'Helvetica-Light', arial, sans-serif;
- 省略号：（写过好多遍）
```css
white-space:nowrap;
overflow:hidden;
text-overflow:ellipsis;
```
- CSS sticky footer布局（实现底部内容随着内容长度而变化。）：http://www.w3cplus.com/css3/css-secrets/sticky-footers.html
- IOS专有的模糊背景属性：backdrop-filter: blur(10px);
- ​垂直居中：父级用display: table, 子级display：table-cell；vertical-align：middle；
- Vue异步赋值。一般需要dom加载完成后操作dom时使用（动态更新数据）：this.$nextTick(()=>{});
- css黑魔法：padding-top：100%（相对于宽度来计算padding）
- 兼容iphone5小屏幕时：
```css
@media only screen and (max-width:320px){
  
}
```
- 注意驼峰命名和连字符命名

### 总结
通过这次组件化开发事件，收获不仅仅是vue这门语言，还有很多规范的编程习惯。ESlink的使用、css结构格式、命名规范···
Vue很多技术细节，还是需要好好琢磨文档。抽时间再学一下Vuex。
webpack真的很方便。不过吐槽一下vue-loader根本没有处理.vue文件里面的内容，他只是告诉你应该由其他的方式来loader来处理这个文件。

就这样吧。
![](/assets/blogImg/20170409-02.jpg)
又是一年樱花季，来一张去年的照片解解馋吧=。=
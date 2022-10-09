---
title: 小程序Note2
tags: 
	- 小程序
---

最近一个月，主要的精力还是在小程序这边，公司的项目是，自己闲暇之余写的小项目也是。
公司项目就不说了，自己的小项目[涙曇](https://github.com/tomorrowchuang/leona)
前后用了一个月，101commits完成了1.0.0版。从有想法到自己设计再到开发完完全全走完这一套流程，还是很满足的。

这里还是聊聊最近编写小程序一些问题和解决方案

![](/assets/blogImg/20180928-01.jpg)
（中秋在三里屯人行天桥拍到的日落，很美）

<!-- more -->

### 简单说说登录
小程序登录，这里抛开后端（其实镇等复杂的还是获取用户登录信息并涉及上传用户到服务器这个过程的后端操作）说说前端登录。

首先说说1.9版本新加的显示用户信息的方法（不需要授权，静默获取）如果只是想在UI显示用户的信息比如昵称，来自那里···，就可以用`<open-data>`这个组件。
使用起来超级简单，弊端就是没有授权，并没有真正拿到数据。如果要上传用户信息到服务器，就必须getUserInfo在js中拿到数据。

下面再说说授权登录。

早期获取用户登录信息是通过`wx.getUserInfo`这个接口获取的，使用起来非常简单。但微信大概是觉得API的方式太过灵活，微信团队不希望
开发者滥用这些方法，所以登录授权的操作放在了button这个组件上。让用户使用组件主动点击

`<button open-type="getUserInfo" bindgetUserInfo="handleUserInfo">授权</button>`

这样。授权弹窗谈过一次，就不再出现，可以在getUserInfo函数中拿到用户信息。
然而上面这种方法有个问题就是，每次都需要用户点击。我们需要一个更灵活的随时可以拿到用户信息的方式。这里又要使用先前说的`wx.getUserInfo`接口了。这个接口并没有废弃需要和button组件配合使用。
只有用户授权通过，`wx.getUserInfo`才可以获取用户信息。这里我们可以使用wx.setting来判断用过是否已经授权登录.这里简单写了一个userAuthorized函数，放在生命周期onload中就可以判断用户是否授权，并拿到值咯。
```javascript
  userAuthorized: function() {
    wx.getSetting({
      success: data => {
        if (data.authSetting['scope.userInfo']) {
          // 用户授权了
          wx.getUserInfo({
            success: data => {
              console.log(data)
              this.setData({
                authorized: true,
                userInfo: data.userInfo
              })
            }
          })
        }
      }
    })
  }

```
这里还要说的就是由于登录、分享这类操作微信强制用button组件点击执行。为了样式的自由，可以简单写一个image-button组件。

wxml:
```html
<button bind:getuserinfo="onGetUserInfo" open-type="{{ openType }}" plain class="container" >
  <slot name="img" ></slot>
</button>
```
wxss:
```css
/* 去覆盖btn的默认样式 */
.container {
  padding: 0 !important;
  border: none !important;
}
```

js:
```javascript
Component({

  options: {
    multipleSlots: true
  },

  properties: {
    openType: String
  },

  methods: {
    onGetUserInfo: function(ev) {
      this.triggerEvent('getuserinfo', ev.detail)
    }
  }
})

```

### wx.navigateBack（）返回上一页面如何传参数

一开始解决这个问题，采用的是往localStorage中存值、取值的方式。维护localStorage是个蛮麻烦的一件事。
之后发现了getCurrentPages()函数获取页面栈的实例从而获取上一个页面数据的方式。代码如下非常好用
```javascript
let pages = getCurrentPages(); // 获取页面堆栈
let currPage = pages[pages.length - 1];   //当前页面
let prevPage = pages[pages.length - 2];  //上一个页面
 
//可以直接调用上一个页面对象的setData()方法，把数据存到上一个页面中去

let prevData = prevPage.__data__ // 可以通过这种hack的方式获取到上一个页面的data

prevPage.setData({
  data：data
})
wx.navigateBack({
    delta: 1
  })

```

### scroll-view隐藏滚动条方法

这个是前两天才看到的

```css
  ::-webkit-scrollbar{
  width: 0;
  height: 0;
  color: transparent;
  }
```

### 侧滑删除
在App上蛮普遍的一个操作，小程序这边本来打算用同组另一个项目的代码，粘贴复制轻松搞定。不过他们这个组件要传定高不说还有不少多余的功能，索性自己写一个，这里就不赘言了。后续可能会维护一下

[wx-side-slip-comp](https://github.com/laclys/wx-side-slip-comp)

### 后记

北京的九月很舒服秋高气爽，秋天真是一个好季节。抽了两个周末分别去了青岛和南京见了一波老同学。转眼毕业四年，大家还是老样子同时也或多或少变了一些。
还有漫长的两天，十一就来咯~

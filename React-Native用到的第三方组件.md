---
title: React-Native用到的第三方组件
tags: 
	- 前端 
	- React
	- ReactNative
---

这一个多月一直在搞公司的App项目，采用的技术就是React Native。一个人蒙着眼摸着石头过河，第一版总算是接近尾声了。（其实还有好多好多坑···）在这里总结一下，开发中用到的第三方组件。

![](/assets/blogImg/20170917-1.jpg)
（2016·秋--湘南）

说道第三方组件，React Native的社区非常非常的活跃。各式各样的开源组件。开发时需要什么功能，google一下，绝大多数情况都能找到符合需求的组件。不过，RN有个大坑就是更新太快了。一个月前还是0.45吧，现在已经0.48了。而且改动非常巨大，带来的问题就是有些组件这个版本ok，，下个版本GG。也有这个版本ok，作者无暇适配开发老版本的兼容。当然还有很多第三方组件自身有Bug，提供的接口太少，样式太死板。Link Ios/Android的时候由于版本引起的神秘报错。总之。寻找一款合适的第三方组件，真的是需要耐心的。

这次App开发采用的是0.43.4这个版本。（0.44这个版本改动非常巨大。立项目之初，保守一点选择了这个版本）。

 <!-- more -->

1.react-native-animatable：动画。RN一个痛点就是动画，这个组件按照Animated.css提供的特效进行RN化。

2.react-native-camera+react-native-qrcode-scanner：两者配合解决了扫一扫功能。这里值得注意的是，qrcode-scanner基于.react-native-camera0.10.0实现的。而0.10.0只支持RN0.40以上的版本。
https://github.com/moaazsidat/react-native-qrcode-scanner（这个star不是很多，不过真的很好用）

3.react-native-dash：画虚线

4.react-native-device-info：获取真机信息。机子类型/系统版本都能获取出来。（反馈功能，提供所使用的真机信息）

5.leancloud-storage：LeanCloud。国内的BaaS提供商。也是为了反馈这个功能而使用的，意外的好用。将数据存储这块交给了它。

6.react-native-easy-toast：原生的Toast提示

7.react-native-fetch-blob：下载这块用的这个，非常强大。下载api地址的json内容。json里的网络图片url，一并拉下来，缓存在本地进行处理都依赖于这个组件。

8.react-native-img-cache：依赖于react-native-fetch-blob的图片缓存组件。基本就上上述组件的一个封装。更加简单易用一些。不需要一口气下载下来的图片，都使用了这个组件。

9.react-native-autoheight-webview：WebView高度自适应组件。基于Webview组件这个方法onNavigationStateChange（）的一个封装调用。原理就是：webview内的JS代码获取自己的高度，添加一个hash给自己的URL，React当感知到webview加载状态变化后，读取这个hash值，传递给this.state.WebViewHeight，从而改变WebView的高度。

10.react-native-image-pan-zoom：图片手势放大缩小

11.react-native-linear-gradient：渐变效果。RN本身样式里不支持渐变。这里引用它，搞定！

12.react-native-modalbox：模态视图。其实github上modal这块功能相同的组件蛮多的。star最前的三四个都试了一下。这个最好用。

13.react-native-pdf： 支持url PDF的下载查看。star最多的那个react-native-pdf-view只支持本地pdf的查看。这个组件找了好久(https://github.com/wonday/react-native-pdf)

14.react-native-maps: airbnb开源的map。ios用的native apple map。Android这块用的 google map。功能非常强大。这个组件配置安装比较复杂。还有一个大坑就是国内安卓没有google play。由于所做的app 地图模块对应的海外。现在的解决方案很傻。ios用native map。Android跳转google map。如果开发的app仅仅需要国内map部分这里推荐：react-native-amap3d。国人开发的高德地图组件安卓部分用的Kotlin。亲测好用。功能很强大：手势交互、标记、绘图·热力图、海点图、导航都ok

15.react-native-spinkit: 一些现成的等待状态的会动图标。app里下载这个状态就用的这个。

16.react-native-vector-icons： 大而全的字体图标库。

17.react-native-snap-carousel： 轮播图组件。有别于react-native-swiper的是：他有点像kindle APP里显示图书那一块的轮播。有缩放显示。


就这些吧。还有一些大众的比如react-native-tab-navigator、react-navigation、react-native-scrollable-tab-view就不在赘言咯。

----

有时间在把开发中的一些坑总结总结~
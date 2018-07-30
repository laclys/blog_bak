---
title: 关于RN项目升级(-)
tags: 
	- ReactNative
---

说道RN，依稀记得去年刚来时接触的时候还是0.38。之后公司要搞App，选了一个不新不旧的版本0.44.3。再到之后为了做一个滤镜的效果，依赖于gl-react和gl-react-native这个库。为了追究更好的体验和兼容Android机，将版本升级成了0.46.4。离职之后最近一直在看web，没怎么看RN这块。没想到现在RN已经更新到了0.55.3。（FB团队是真的勤快）,主要是闲的蛋疼。把自己的一个个人小项目升级了一波。顺便总结一下这一年RN 组件和API的变化以及升级方法。

### 组件上的变化：
新增了：ImageBackground（背景图片组件，它是一个容器组件。当然有了它，也就不支持Image直接包裹的形式）、FlatList、SectionList、VirtualizedList（FlatList、SectionList的底层实现）、SwipeableFlatList（类似于ios备忘录从右往左划出现的效果，带滑动显示更多菜单的FlatList组件）、MaskedViewIOS（可以为组件添加一个透明的遮罩）以及为了适配ipX，0.50出现的SafeAreaView。

### 新增的Api：
自身使用上，主要用了AccessibilityInfo（判断屏幕阅读器是否处于激活状态）以及DeviceInfo（一个类专门提供屏幕尺寸，字体缩放等信息）。
YellowBox（可以屏蔽指定的警告，这个其实也很好用，0.44版本提供的。在公司项目中由于使用了code push。但是在开发环境没有配置key。每次刷新都会弹错，有了它就解决了这个问题。当然更好的办法就是在dev环境配个key）
BackAndroid也被BackHandler所替换。在原有的基础上做了加强和补充。

### 其他：

- View.propTypes -> ViewPropTypes
- takeSnapshot:从 UIManager 移动到 ReactNative(截图的话，疯狂安利：react-native-view-shot)
- react-navigation 代替 Navigator、NavigationExperimental。（前一阵尝试了一下 react-native-router-flux 体验也不错）

<!-- more -->

### 怎么升级
RN一周一个版本，小版本之前有时候都跨度很大。更别提大版本0.4x->0.5x这种了。升级方式有两种：
- 使用react-native-git-upgrade
- 手动shengji

我这边比较倾向手动升级，能减少很多不必要的麻烦。使用前者是通过git自动合并更新。版本跨度较大的时候在native层各种冲突，跟头痛的一件事。
手动升级这里就简单很多。可以初始化一个最新的rn项目，之后对比老项目的以来文件，进行版本改变。然后删除node_modules && npm i.(当然这里要注意的就是 依赖的第三方库/组件，一定要一个一个去检索不同版本RN对应的版本)。


除了这些之外就是：
- Navigator -> react-navigation
- BackAndroid -> BackHandler
- PropTypes -> prop-types(React16开始PropTypes被迁移到了prop-types里，需要另行安装和引用)
- 访问修饰符protected -> public
- View。propTypes -> ViewPropTypes
- codePush。需要在package.json中移除依赖之后重新npm i安装


### 杂

换了新的2017 mac pro 编译速度相比之前的15版快了很多。13k大洋不是白花的

用了新电脑，在构建rn程序的时候遇到了这个错误：

![](/assets/blogImg/20180428-01.jpg)

查了好半天原因，原来是新电脑没有装watchman

```bash
brew install watchman
```
就好。

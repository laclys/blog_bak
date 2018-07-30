---
title: React Native学习--布局问题
tags: 
	- react
	- react-native 
---


最近一直闲暇时间一直在看RN。大概之前主要看Vue，对React只是了解的缘故。直接上RN，扑面而来一箩筐的概念和知识（不光是React还有ios/Android ，没有原生开发经验，上周在配置1环境的时候就踩了不少坑=。=）。

今儿主要说说RN中的布局问题。RN采用的布局方式是FlexBox。现代浏览器还是会有FlexBox的兼容问题，在网页开发多少会有一些限制。而RN，是面向ios/Android的。既然RN对FlexBox支持。我们大可不必关心兼容性问题。

### RN的单位

首先要说的是RN中的尺寸。
在RN中尺寸是没有单位的，他代表设备独立像素。
eg：
```javascript
<View style={ {width:100,height:100,margin:40,backgroundColor:'gray'}}>
        <Text style={ {fontSize:16,margin:20}}>尺寸</Text>
</View>
```
这里使用无单位的尺寸，是为了确保布局在任何不同的dpi的手机屏幕上不会发生改变。
在上面的代码中：

运行在Android上时，View的长和宽被解释成：100dp 100dp单位是dp，字体被解释成16sp 单位是sp，运行在iOS上时尺寸单位被解释称了pt。

<!-- more -->

### RN/web css上FlexBox的不同之处

	lexDirection: React Native中默认为flexDirection:'column'，在Web CSS中默认为flex-direction:'row'

	alignItems: React Native中默认为alignItems:'stretch'，在Web CSS中默认align-items:'flex-start'

	flex: 相比Web CSS的flex接受多参数，如:flex: 2 2 10%;，但在 React Native中flex只接受一个参数

	不支持属性：align-content，flex-basis，order，flex-basis，flex-flow，flex-grow，flex-shrink
以上是React Native中的FlexBox 和Web CSSS上FlexBox的不同之处。

### Flex in React Native

容器属性：
- flexDirection enum(‘row’, ‘column’,’row-reverse’,’column-reverse’)
- flexWrap enum(‘wrap’, ‘nowrap’)（nowrap flex的元素只排列在一行上，可能导致溢出。/wrap flex的元素在一行排列不下时，就进行多行排列。）
- justifyContent enum(‘flex-start’, ‘flex-end’, ‘center’, ‘space-between’, ‘space-around’)
- alignItems enum(‘flex-start’, ‘flex-end’, ‘center’, ‘stretch’)

子容器属性：
- alignSelf enum(‘auto’, ‘flex-start’, ‘flex-end’, ‘center’, ‘stretch’)
  alignSelf 属性可重写灵活容器的 alignItems 属性。	
- flex number(属性定义了一个可伸缩元素的能力，默认为0)

### 其他布局 in RN

**视图边框：**
- borderBottomWidth number 底部边框宽度
- borderLeftWidth number 左边框宽度
- borderRightWidth number 右边框宽度
- borderTopWidth number 顶部边框宽度
- borderWidth number 边框宽度
- borderColor 边框颜色

尺寸：
- width number
- height numb

margin：
- margin number 外边距
- marginBottom number 下外边距
- marginHorizontal number 左右外边距
- marginLeft number 左外边距
- marginRight number 右外边距
- marginTop number 上外边距
- marginVertical number 上下外边距

padding：
- padding number 内边距
- paddingBottom number 下内边距
- paddingHorizontal number 左右内边距
- paddingLeft number 做内边距
- paddingRight number 右内边距
- paddingTop number 上内边距
- paddingVertical number 上下内边距

定位：
- position enum(‘absolute’, ‘relative’)属性设置元素的定位方式，为将要定位的元素定义定位规则。

---

### 参考：

[A Complete Guide to Flexbox ][1]
[Using CSS flexible boxes][2]

![](/assets/blogImg/20170610.png)






[1]: https://css-tricks.com/snippets/css/a-guide-to-flexbox/
[2]: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes
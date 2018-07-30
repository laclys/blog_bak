---
title: React Native开发遇到的一些坑
tags: 
	- 移动端 
	- ReactNative
---

距上篇博文有间隔了好一阵。10月初，用RN写的app上架以后，马不停蹄地有整出来一份同业的版本，之后就全力进行二期的优化。其实就是填坑。每天忙忙碌碌，烦心事闹心事也不少。不过看到自己完成的app上store，同事夸app好用，心里还是美滋滋的。回首搞app开发这几个月，真是成长了不少。

![](/assets/blogImg/20171024-1.jpg)

言归正传，在这总结一下RN开发中的一些坑：

<!-- more -->

### react-navigation
react-navigation作为官方推荐的导航跳转的工具，比起之前的navigator好用不少。当然也有不少问题。

- goBack（）问题
 
 react-navigation提供有goBack()这api, 但是使用返回栈中某个组件的方法this.props.navigation.goBack('component')或this.props.navigation.dispatch(NavigationActions.back({key: "component"}))没有反应, 原理是因为这里的component不是用自定义的routeName而是用自动生成的随机值key. 又没有提供有api获取组件的key, 手动去获取又十分麻烦。这里我们可以修改他的源码：
把项目/node_modules/react-navigation/src/routers/StackRouter.js中
```javascript
const backRoute = state.routes.find((route: *) => route.key === action.key);
```
改为：
```
const backRoute = state.routes.find(route => route.routeName === action.key);
```
这样的话，里的component要填想返回的组件的前一个组件的routeName, 比如你的栈里顺序是home1, home2, home3, home4, 在home4里要返回home2, 使用this.props.navigation.goBack('home3');; 并且又会带出一个问题: goBack()方法没反应了, 必须加个null进去, 写成goBack(null)

- 快速点击多次跳转问题

当我们快速点击x按钮，请求跳转时。如果快速地点击两下，三下。会导致快速跳转。这里的解决方案是加入一个setTimeout函数。

修改react-navigation目录下，scr文件夹中的addNavigationHelpers.js
```javascript
export default function<S: *>(navigation: NavigationProp<S, NavigationAction>) {
  // 添加点击判断
  let debounce = true;
  return {
      ...navigation,
      goBack: (key?: ?string): boolean =>
          navigation.dispatch(
              NavigationActions.back({
                  key: key === undefined ? navigation.state.key : key,
              }),
          ),
      navigate: (routeName: string,
                 params?: NavigationParams,
                 action?: NavigationAction,): boolean => {
          if (debounce) {
              debounce = false;
              navigation.dispatch(
                  NavigationActions.navigate({
                      routeName,
                      params,
                      action,
                  }),
              );
              setTimeout(
                  () => {
                      debounce = true;
                  },
              500,
              );
              return true;
          }
          return false;
      },
    /**
     * For updating current route params. For example the nav bar title and
     * buttons are based on the route params.
     * This means `setParams` can be used to update nav bar for example.
     */
    setParams: (params: NavigationParams): boolean =>
      navigation.dispatch(
        NavigationActions.setParams({
          params,
          key: navigation.state.key,
        }),
      ),
  };
}

```
### 底部多个TextInput，输入法遮盖问题

安卓上貌似不会出现这个问题，主要是ios端。一开始使用的是原生的IQKeyBoardManager。但是多个TextInput情况下。支持的不是太好。这里使用了一块开源的组件：react-native-keyboard-aware-scroll-view 完美解决这个问题。

### react-native-lightbox

这个组件实现了点击图片，灯箱效果。不过它的问题蛮多的。
遇到的第一个问题就是Pinch to zoom。这个组件本身不支持灯箱效果下，手势放大。这里使用了一个小技巧。通过本身的swipeToDismiss={false}以及ScrollView的minimumZoomScale和maximumZoomScale属性解决这个问题。

eg:
```javascript
<LightBox
        key={i}
        swipeToDismiss={false}
      >
        <ScrollView
          minimumZoomScale={1}
          maximumZoomScale={2}
          centerContent={true}
        >
          <Image
            style={styles.image}
            source={{'uri' : source}}
          >

          </Image>
        </ScrollView>
      </LightBox>
```
这个组件还有一个坑是，点击图片出现灯箱效果之后。IOS端的顶部StatusBar信息莫名其妙的消失了。排查了一大圈，发现是这个组件自己写没了。
这里需要修改一下源码

/node_modules/react-native-lightbox/LightboxOverlay.js:
135  line: 
```
StatusBar.setHidden(true, 'fade'); -> StatusBar.setHidden(false, 'fade');
```
### scrollview的触摸滚动事件
在这里详细的列举一下：
```
1、onScrollBeginDrag：一个子view滑动开始拖动开始时触发，注意和onMomentumScrollBegin的区别

2、onScrollEndDrag：一个子view滚动结束拖拽时触发，注意和onMomentumScrollEnd的区别

3、onTouchStart：按下屏幕时触发

4、onTouchMove：移动手指时触发

5、onTouchEnd：手指离开屏幕触摸结束时触发

6、onMomentumScrollBegin：当一帧滚动开始时调用.

7、onMomentumScrollEnd：当一帧滚动完毕时调用.

8、onStartShouldSetResponder：触摸开始时是否成为响应者

9、onStartShouldSetResponderCapture：防止子视图在触摸开始时成为应答器

10、onScrollShouldSetResponder：滚动时是否成为响应者

11、onResponderGrant：开始响应时触发

12、onResponderRelease：手指释放后，视图成为响应者

13、onResponderReject：另一个响应已经被激活, 响应者不会释放它到该视图

14、onScroll：滚动时触发，会触发多次
```
下面是在网上找到的两张图，分别是ios端的测试和Android端的：

![](/assets/blogImg/20171024-2.jpg)

![](/assets/blogImg/20171024-3.jpg)


### WebView自适应高度
在做APP的时候遇到了一个需求就是希望页面中的文字可以复制。如果是Text组件下的。这个好说一个selectable={true}属性搞定。
但是selectable={true}这个属性。在ios端只支持整段话的复制。不支持，选哪儿复制那这种操作。之前查了一下好像是和底层OC代码有关。Android这块没有问题。
为了实现需求，采用了Webview的方式。（从接口的到的字段也是超文本类型的）。但是这里出现了一个问题就是WebView无法根据提供内容的多少自适应高度。

ReactNative中的WebView需要设定高度才能展示出来，因此需要用js来计算文档高度做到高度自适应。一个思路就是让WebView和React的JS进行通信。页面加载后，webview内的JS代码获取自己的高度，添加一个hash给自己的URL，React当感知到webview加载状态变化后，读取这个hash值，传递给this.state.WebViewHeight，从而改变WebView的高度。

第二种思路就是万能的github。试了好几个组件之后，一个国人开发的react-native-autoheight-webview最好用。

不过有一个诡异的现象是value值很少的情况下无法进行长按选中字段复制。字数多的时候 （4，5行以上）功能正常。这个问题的解决：
```javascript
// default width is the width of screen
// to fix that it can not select text when the length of it is not long enough on iOS, the width should be reduced more than 15
style={{ width: Dimensions.get('window').width - 15 }}
```

### react-native-scrollable-tab-view 
这个组件是tab切换用的。其实react-navigation也有这个功能。
它有一个问题是。在多个页面切换下无法正确的显示页面的高。默认选用的是最长的那个。（Tab view always has the height equal to height of the highest tab）

在issue上看到不少人碰到这个问题。一个不是很完美的解决方案就是：

Use ScrollView as tabs.

eg：
```javascript
<ScrollableTabView style={{ height: Dimensions.get('window').height }}>
  <ScrollView>
    <View style={{ height: 300, backgroundColor: 'cadetblue' }} />
  </ScrollView>

  <SrcollView>
    <View style={{ height: 5000, backgroundColor: 'lightgreen' }} />
  </ScrollView>
</ScrollableTabView>
```
### MAC对APK包进行重新签名

jarsigner -verbose -keystore [您的私钥存放路径] -signedjar [签名后文件存放路径] [未签名的文件路径] [您的证书名称]

```
jarsigner的参数说明
-keystore 参数指定您的私钥的绝对路径,例如：/Users/haoweilai/mykeystore
-signedjar 参数指定签名后apk文件存放绝对的路径,例如 /Users/haoweilai/signed.apk
[未签名的文件路径] 指定要签名apk文件的绝对路径,也就是您从我们这里下载到的，例如 /Users/haoweilai/meizuemptyapk-release-unsigned.apk
[您的证书名称] 是指您创建密钥时您设置的证书名称
```

以上

先总结这些，想到了再补充。RN还有太多太多知识需要掌握。原生，redux，优化。加油~
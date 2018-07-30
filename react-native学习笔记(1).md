---
title: react-native学习笔记 
tags: 
	- react
	- react-native 
---

周末两天的RN初体验，一些杂乱的笔记。

![](/assets/blogImg/20170604.jpg)

### 传统开发：
人员稀缺；开发成本高；代码复用；无法实现动态更新

### 优点：
learn once,write anywhere
跨平台 性能高 低投入高回报 支持动态更新

### first app
 command+d 在模拟器里打开 开发者菜单

react-native run-ios （没什么坑直接用就好，启动xcode里的ios模拟器）

 Android这边坑比较多
遇到的第一个问题：

**Unable to access Android SDK add-on list**
解决方案：
 在 Android Studio 安装目录 bin/idea.properties 文件最后追加一句

    disable.android.first.run=true

<!-- more -->

 Android Studio SDK配置问题：

 http://reactnative.cn/docs/0.44/getting-started.html

 这一块不管是中文doc还是英文都没更新。最新版本的RN 不支持SDK25以下的了。都用最新的就好。要注意的是Show Package Details里面的选项。（这块坑了好久- -）

 之后运行项目遇到这个错误
 **Error calling Appregistry.runApplication**

 全局变量配置： 
    open .bash_profile
  粘贴如下代码：
 ```
export ANDROID_HOME=~/Library/Android/sdk
export PATH=${PATH}:/Users/lac/Library/Android/sdk/tools:/Users/lac/Library/Android/sdk/platform-tools
 ```
    source .bash_profile
 配置好全局变量 这下就可以运行adb了； 

    adb reverse tcp:8081 tcp:8081

  run react-native start in the background then run react-native run-android.

#### 命令行启动Android模拟器：
  ~/Library/Android/sdk/tools/emulator -avd a25

### 三种定义组件的方法
  es6定义组件：
```javascript
  export default class HelloComponent extends Component{
    render() {
        return <Text style={{fontSize:20,backgroundColor:'red'}}>Hello</Text>
    }
}
```

  es5定义组件：
```javascript
var HelloComponent=React.createClass({
    render() {
        return <Text style={{fontSize:20,backgroundColor:'red'}}>Hello</Text>
    }    
})
module.exports=HelloComponent;
```

  函数式创建组件(无状态的（不能使用this）)：
```javascript
function HelloComponent() {
    return <Text style={{fontSize:20,backgroundColor:'red'}}>Hello</Text>;
}
module.exports=HelloComponent;
```
console.log页面：
http://localhost:8081/debugger-ui

**如何导出一个组件？如何使用导出的组件？**

ES6 通过export default导出组件，
通过import使用组件
ES5通过module.exports=组件名字

**如何导出一个变量或常量？如何使用导出的变量和常量？**
导出：

    var name="Lac";
    const age = "18";
    export {name,age};

导入 在import里使用{name,age}

**如何导出一个方法？如何使用导出的方法？**

    export function sum(a,b) {
        return a+b;
    }

导入 在import里使用{sum}


### 设置默认属性(在组件没有传递props时候)
```javascript
  static  defaultProps = {
    name:'Bill'
    }
    // 属性检查机制 （要import {PropsTest}
    static propTypes = {
    name:PropTypes.String,
    }
```

*props是只读的，不可改变

Es6 延展操作符：
 之前：向一个组件中传递属性：

     var params = {
      name:'小张',
      age:24
    }
    
    <PropsTest
    name={params.name}
    age={params.age}
     />

Es6中可以这样：(在传递很多属性是非常方便)

    <PropsTest
    {...parmas}
     />

*结构赋值（获取指定的属性（es6））*

    var {name}=parmas;
    <PropsTest
    name={name}
     />

​    
ref:组件被渲染后指向组件的一个引用





todo···
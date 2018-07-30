---
title: 比Redux简单太多的Mobx
tags: 
	- ReactNative 
	- Mobx
---

unhappy with redux? try mobx

相比上手/操作及其繁琐的Redux，用Mobx做状态管理，最直观的感受是爽爆了。

Mobx是一个非常直观的状态管理库，使用非常简单。通过Mobx，使react 关注的状态(state)到视图(view)的问题。而 mobx 关注的是状态仓库（store）到的状态(state)的问题。

废话不多说，这里通过一个最最简单的例子来感受基于RN的Mobx。

<!-- more -->

首先init 一个RN工程：
```bash
react-native init ReactNativeMobX
```
安装依赖库mobx以及mobx-react：
```bash
npm i mobx mobx-react --save
```
因为mobx要使用ES7的语法（Decorator）,我们还需要在项目中使用babel：
```bash
npm i babel-plugin-transform-decorators-legacy babel-preset-react-native-stage-0 --save-dev
```
在工程目录下的.babellrc文件里配置一下：
```
{ 
 "presets": ["react-native"],  
 "plugins": ["transform-decorators-legacy"]
}
```
环境搭配完咯。下面我们就可以编写代码咯。这里实现一个超级简单的例子。两个btn，+和-。控制字符的加减。

![](/assets/blogImg/20171118-01.jpg)

![](/assets/blogImg/20171118-02.jpg)

首先，我们在工程根目录创建一个mobx文件夹并创建一个仓库文件：countStore.js

```javascript

// 导入了被观察者 observable
import {observable} from 'mobx'

// 创建了一个新类：ObservableCountStore
class ObservableCountStore {

    // 定义一个data变量。这个变量的特殊之处在于被@observable修饰
    // 表示该类的data属性可被观察者（observer）观察，也就是观察者模式。
    @observable data = 0
    
    // 递增操作方法
    handleAdd = () => {
      this.data++
    }
    
    // 递减操作方法
    handleDec = () => {
      this.data--
    }

}
// 实例化了ObservableListStore类
const observableCountStore = new ObservableCountStore();
// export实例化的对象
export default observableCountStore
```

这里我们的仓库就创建完了。接下来在App.js文件（新版RN将index.android.js及index.ios.js合二为一了）使用我们的store。

```javascript
import React, { Component } from 'react';
import {
  StyleSheet,
  Text,
  View,
  TouchableOpacity
} from 'react-native';

// 导入observer（观察者）
import {observer} from 'mobx-react/native'
// 导入我们的仓库
import observableCountStore from './mobx/countStore'
// 我们使用@observer修饰符来修饰App类，它可以保证当其相关联的数据变化时，该组件会重新渲染
@observer
export default class App extends Component<{}> {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.welcome}>
          {observableCountStore.data}
        </Text>
        <View style={styles.wrapper} >
          <TouchableOpacity
            style={styles.countBtn}
            onPress={() => {
              observableCountStore.handleAdd()
            }}
          >
            <Text>+</Text>
          </TouchableOpacity>
          <TouchableOpacity
            style={styles.countBtn}
            onPress={() => {
                observableCountStore.handleDec()
            }}
          >
            <Text>-</Text>
          </TouchableOpacity>
        </View>
      </View>
    );
  }
}
// 样式省略~
```
通过observableCountStore.data，在render里使用store里的数据。
通过observableCountStore.handleAdd()和observableCountStore.handleDec()两个方法来改变状态。

至此一个最最简单的Mobx例子完成。我们可以看到mobx以最直接的方式来管理我们的状态，即我们开始说的 mobx 关注的是状态仓库（store）到的状态(state)的问题。是不是比Redux简单太多了~=。=

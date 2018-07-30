---
title: Vuex学习笔记
tags: 
	- 前端
	- Vue
	- Vuex
---

## 什么是Vuex？

![](/assets/blogImg/20170423-01.png)

专门为Vue开发的状态管理模式。采用集中存储管理应用的所有组件状态。在没有用Vuex的时候，我们在进行组件化开发时，数据（也就是状态一般都分散在各个组件中。加入我们要使用这些状态是，我们就得在不同组件中去取状态值，然后进行状态修改，最后还要相互读取对方状态值。
**父子组件中：**
我们可以通过事件触发/props进行状态传递。
**子组件之间：**
由于Vue本身组件之间有作用域，他们之间无法相互通信。这里就必须用单一时间管理（event bus）或者在所有组件和Vue对象下使用一个统一的Object作为数据，来实现。（实质：引用）。

如果项目较小还好，中大型项目，组件通信复杂频繁的时候，这两种方式管理起来就会十分麻烦，而且容易出错。

有了Vuex，它将数据单独放在一个store里，并提供给外部操作内部数据的方法。让我们能更好的管理项目里的状态。这也是Vuex出现的意义吧。就像官网说的：
	Flux 架构就像眼镜：您自会知道什么时候需要它
 <!-- more -->

## 正文
基本就是按官网的教程撸了一遍。记录一下自己觉得重要的内容。

Vuex 应用的核心就是 store（仓库）。"store" 基本上就是一个容器，它包含着你的应用中大部分的状态(state)。当然，在使用Vuex的同时，各个组件仍保有局部的状态：
	使用Vuex，并不意味着你需要将所有的状态放入Vuex。
下面我们来按搭建一个小例子：

源码：https://github.com/laclys/vuex-demo

在Vue脚手架里面，在main.js中：
```javascript
import Vuex from 'vuex';
Vue.use(Vuex);
const store = new Vuex.Store({
	state: {
      count: 0
	},
	  mutations: {
    increment (state) {
      state.count++
    }
  }
new Vue({
  el: '#app',
  store,
  render: h => h(App)
})
```
这里我们就创建了一个最简单的store，并且挂载在了Vue实例上。配置其实和vue-router挺像的。
State：就是单一状态树，将我们需要管理的状态在这里定义。
Mutations：更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutations 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。
要想在组件中使用。由于 Vuex 的状态存储是响应式的，从 store 实例中读取状态最简单的方法就是在计算属性中返回某个状态。
```javascript
    computed: {
        count() {
            return this.$store.state.count;
        }
```
通过要唤醒一个 mutation handler，你需要以相应的 type 调用 store.commit 方法。
在组件中：
```javascript
    methods: {
        increment() {
            this.$store.commit('increment');
        }
   }
```

Getters：这个在上面的例子没有涉及到。我们可以认为Getters是Vuex在store中的计算属性。（Getters 接受 state 作为其第一个参数）
```javascript
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```
组件中调用：
```javascript
computed: {
  doneTodos () {
    return this.$store.getters.doneTodos
  }
}
```
Actions: 类似于 mutation，不同在于：
1)Action 提交的是 mutation，而不是直接变更状态。
2)Action 可以包含任意异步操作。

Mutations中对状态的操作只能是同步操作，不能是异步操作。
```javascript
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```
在组件中：
```javascript
methods: {
    incrementAsync() {
      this.$store.dispatch('incrementAsync');
     }
   }
```
说到异步，当然是支持promise以及 async / await 这个 JavaScript 即将到来的新特性：
这里说一下promise：
	store.dispatch 可以处理被触发的action的回调函数返回的Promise，并且store.dispatch仍旧返回Promise
```javascript
    decrementAsync({commit}) {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          commit('decrement');
          resolve();
        },1000)
      })
    }
```
组件中 我们就可以用链式then了：
```javascript
        decrementAsync() {
            this.$store.dispatch('decrementAsync').then(() => {
                // 执行完decrementAsync,之后执行：
                console.log('decrementAsync执行完了');
            });
        }
```

之后还有一个核心概念叫做Modules，顾名思义模块化。当项目非常的庞大，那么状态可能本身还需要进行分模块分类管理，这个时候就需要用到模块了。这里就不再赘言了。


以上
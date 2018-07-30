---
title: React杂记
tags: 
	- react
---

![](/assets/blogImg/20180624-01.jpg)
（上海·凌空SOHO）

### React性能优化：
性能优化老生常谈，网上的文章也一大堆，自己总结一下，也做一个记录。

1）单组件：传递参数要少传，尽可能减少React在传递参数时候的负担。当然在使用数据的时候尽可能使用一份数据，而不是重新定义一份。

在使用 函数的时候：

NG：

每次执行render函数的时候bind都会执行一次
```html
<button onClick={this.handleClick.bind(this)}></button>
```
比上一个好一点，但每次执行render的时候都会重新生成一个函数
```html
<button onClick={() => this.handleClick()}></button>
```
Good：

```javascript
  constructor(props) {
    super(props)
    this.handleClick = this.handleClick.bind(this) // 构造函数只会执行一次。在构造函数中绑定this
  }
```
```html
<button onClick={this.handleClick}></button>
```

<!-- more -->

在传递参数的时候
NG：
每次都会生成新对象
```html
<div style={color: 'red'}></div>
```
GOOD：

提前定义好需要的对象。目的就是使用一份数据，而不是重新定义一份

再给子组件传递参数的时候 ，只传递需要的参数。！

2） 多组件：
说道多组件有一个情况就是：子组件是不需要变化的，但是父组件的render执行了变化，子组件也会跟着再次渲染
例,fu组件render中通过一个改变name的btn，使state中的name变化。在点击按钮的时候，state中的title没有变化，childCom渲染的内容没有改变当然是不需要再渲染的，但事实上childCom又重新渲染了
```javascript
render() {
  return (
    <div>hello,{this.state.name}</div>
    <button onClick={this.changeName}></button>
    <childCom title = {this.state.title} />
  )
}

```
ps：在react性能检测。在开发url中加上？react_perf->刷新在chrome中的performance中即可得到我们需要的性能检测的过程数据（重点看：user Timing）

这是我们就可以在子组件里通多shouldComponentUpdate来处理组件需不需要渲染。默认直接return true渲染组件。这里我们不希望子组件在父组件title没变化的时候再次渲染，这里我们就要定制一下shouldComponentUpdate
```javascript
shouldComponentUpdate(nextProps, nextState) {
  if (nextProps.title == this.props.title) return false
  return true
}
```
当然在react15之后 使用pureComponent就可以了。它的核心原理也是重写了shouldComponentUpdate

3）老生常谈key的问题

4）使用不可变的JS（immutable.js）。在这里就不细说了，具体原理就是 通过不可变得数据结构（直接比较hash值）减少我们的渲染次数。

5）如果使用了Redux。众所周知 reducer是一个纯函数，纯函数的特点就是稳定输入，稳定输出。这里我们可以用一个库（reselect）通过缓存输入过的结果，进而优化性能，程序速度。

6）React同构 SSR 首屏渲染速度


### React Context：
在使用react-redux的时候，我们要在最外层包裹一个Provider。把redux的store放进去。这样我们就可以在项目的任意组件中自由读取store内容了。这里用运用了context。

context是全局的，组件声明，所有子元素可以直接获取

context对数据校验强要求！(子/父都需要设置)

这里是一个最简单的例子
```javascript
import React from 'react'
import PropTypes from 'prop-types'

class Sidebar extends React.Component {
  render () {
    <div>
      <p>Top Place</p>
      <Navbar />
    </div>
  }
}

class Navbar extends React.Component {

  static contextTypes = {
    user: PropTypes.string
  }

  render () {
    <div>
      <p>{this.context.user}のNavbar Place</p>
    </div>
  }
}

class Page extends React.Component {

  static childContextTypes = {
    user: PropTypes.string
  }

  constructor (props) {
    super(props)
    this.state = {user: 'Lac'}
  } 

  getChildContext () {
    return this.state
  }

  render () {
    return (
      <div>
        <p>my name is {this.state.user}</p>
        <Sidebar />
      </div>
    )
  }
}

export default Page

```

以上

---
这两个月过得无比纠结，经验教训就是：真的没事别瞎折腾，非常累。

踏踏实实，好好工作才是真的。

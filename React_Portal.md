---
title: React Portal 小谈
tags:  
	- 前端
	- react
---

最近在看ReactV16带来的新东西，V16版本直接支持了Portal。今儿就总结一下Portal这个自己不是那么熟悉却又非常重要的“传送门”吧。
先说说为什么需要传送门。React的世界一切皆组件，进行render就需要一个root根节点进行挂载。组件套组件一层一层，不过最外面一层都是挂在在这个root根节点上的。

有一个常见的需求Dialog(弹窗),通常它会显示在屏幕正中央，它应该是一个独立的组件。因此将这个弹窗组件放在根节点下的子组件里显然是不合适的。
我们希望将其渲染在和root根节点同级的DOM上在React组件树的最顶层。样式独立，没必要受其他组件的干扰。除了Dialog需要在最顶层渲染之外，我们还希望(也可以说是诉求)在不同的业务组件中去使用它。这里就需要Portal咯。

![](/assets/blogImg/20181214-1.jpg)
（ 天野喜孝展 ）
<!-- more -->
### V16之前去实现Dialog
上代码
```javascript
import React from 'react'
import { unstable_renderSubtreeIntoContainer, unmountComponentAtNode } from 'react-dom'

class Dialog extends React.Component {

  constructor(props) {
    super(props)
  }

  componentDidMount() {
    const doc = window.document
    this.node = doc.createElement('div')
    doc.body.appendChild(this.node)

    this.renderPortal(this.props)
  }

  componentDidUpdate() {
    this.renderPortal(this.props)
  }

  componentWillUnmount() {
    unmountComponentAtNode(this.node)
    window.document.body.removeChild(this.node)
  }

  render() {
    return null
  }

  renderPortal(props) {
    unstable_renderSubtreeIntoContainer(
      this, //当前组件
      <div class='dialog'>
        {props.children}
      </div>,
      this.node 
    )
  }
}
```
render返回的是null，在componentDidMount下在body上创建一个div。通过`renderPortal`将组件内容（props.children）塞进去

这里主要用了 react-dom中 `unstable_renderSubtreeIntoContainer`, `unmountComponentAtNode`这两个库。
其中`unstable_renderSubtreeIntoContainer`算是一个React标准外的API，作用嘛就是“传送门”把一部分组件结构
塞到另外一个地方。

`unmountComponentAtNode`则是在componentWillUnmount这个生命周期里调用去清理`unstable_renderSubtreeIntoContainer`产生的副作用


### V16实现Dialog
React V16真的是一个巨大的版本。本身支持了`Portal`。提供了createPortal函数。
我们就不需要在componentDidMount、componentDidUpdate中`renderPortal`也不需要在去通过`unmountComponentAtNode`清理副作用

```javascript
import React from 'react'
import { createPortal } from 'react-dom'

class Dialog extends React.Component {

  constructor(props) {
    super(props)
    this.node = doc.createElement('div')
    window.document.body.appendChild(this.node)
  }

  componentWillUnmount() {
    window.document.body.removeChild(this.node)
  }

  render() {
    return createPortal(
      <div class='dialog'>
        { this.props.children }
      </div>,
      this.node
    )
  }
}

```

以上~~~

11中旬开始，异常地忙碌。项目需求真的是无穷无尽，大佬开会表示19年的需求都排满了。

代码代码何时是个头。

前两天和仍然在日本的好友聊天，日本的生活还是那么波澜不惊、按部就班小日子蛮惬意。
回想自己从日本回来这几年，一开始疯狂焦躁了一波，之后慢慢平稳
努力学技术、看书一直坚持。技术、工资都再上涨，除了这些随之而来的还有不断上涨的焦虑。
有时候未来真的不敢想。

睡觉🛌
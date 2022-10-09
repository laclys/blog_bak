---
title: 说说React16的生命周期
tags: 
	- react
---

React16算是React诞生至今变化相当大的一个版本，在`v16`版本中陆陆续续新增/修改了很多地方。最新的release版本已经到了`v16.8.4`。
不管是平时工作还是自己学习都已经使用上了v16这个版本

这里就简单说一下v16里的生命周期(虽说是老生常谈，但仔细看文档还是发现了一些之前没注意、不怎么用的东西比如`getDerivedStateFromProps`和`getSnapshotBeforeUpdate`)

react生命周期分三个阶段：挂载、更新、卸载 , v16多出了一个异常捕获(`componentDidCatch`)

### 挂载
- constructor
- static getDerivedStateFromProps (之前没注意到这个)
- render
- componentDidMount

这里我们主要看一下`constructor`、`componentWillMount`和`static getDerivedStateFromProps`

- constructor:

1. 初始化 state(注意避免使用props给state赋值)
2. 给方法绑定this
但在v16之后这两件事在`constructor`外面做会更直观更简单(v16之后`constructor`使用场景会变少)
eg：
```javascript
class Test extends Component {
  state = {
    a: 1
  }
  handleClick = (e) => {
    // xxx
  }
}

```

<!-- more -->

- componentWillMount:

v16会告诫开发者`componentWillMount`这个函数会在未来的版本删去。我们可以先想想之前这个生命周期用来做什么？ - AJAX请求，然后执行 setState 重新渲染？ 但是在`componentWillMount`中进行这样的操作是在不是很妥当，特别在SSR项目中(这里不展开)。因此说到底它没什么用，被"删掉"也是合情合理

- static getDerivedStateFromProps

这个就是再次看文档的新发现。上面说到应避免使用props给state赋值。v16给与了我们这个静态方法（挂载组件时，它会在`render`之前执行）
eg：
```javascript
// getDerivedStateFromProps的返回值将作为setState的参数，如果返回null，则不更新state，不能返回object 或 null 以外的值，否则会警告。
class Test2 extends Component {
  state = {
    b: 0
  }
  static getDerivedStateFromProps(props, state): State {
    if(props.b !== state.b) {
      return {
        b: props.b
      };
    }
  }
}
```
另外需要提一下，`getDerivedStateFromProps`是静态方法，故拿不到this，所以我们需要把它设计为纯函数。

同时也会发现有了这个静态方法，`componentWillReceiveProps`好像也没什么用了（这就能解释为什么v16也把它'删掉咯'）

### 更新
- static getDerivedStateFromProps
- shouldComponentUpdate
- render
- getSnapshotBeforeUpdate
- componentDidUpdate

这里就说说，另一个我之前没怎么注意到的点`getSnapshotBeforeUpdate`

在 react 更新 dom 之前调用，此时 state 已更新； 返回值作为 componentDidUpdate 的第3个参数； 一般用于获取 render 之前的 dom 数据

eg：

```javascript
class Test3 extends Component {
  getSnapshotBeforeUpdate (prevProps, prevState): Snapshot {

  }
  componentDidUpdate(prevProps, prevState, snapshot) {
    // snapshot 是从 getSnapshotBeforeUpdate 的返回值，默认是 null
  }
}
```

### componentDidCatch
最后再说说这个异常捕获,用于捕获组件树的异常。v16版本引入了所谓` Error Boundary `的概念,而保证了发生在 UI 层的错误不会连锁导致整个应用程序崩溃。其实可以理解为`try···catch`
通常我们可以使用这个捕获异常的生命周期钩子封装成一个这样的 `decorator `来包裹可能出错的组件

```javascript
import React, { Component } from 'react'

export default (message = '出错啦!') => (TargetComponent) => {

  class ErrorBoundary extends Component {

    constructor (props) {
      super(props)
      this.state = {
        hasError: false,
        error: null,
        info: null
      }
    }
    
    componentDidCatch (error, info) {
      this.setState({
        hasError: true,
        error,
        info
      })
    }

    render () {
      const { hasError } = this.state

      if (hasError) {
        return (
          <div className='error-boundary'>
            <p className='error-boundary-message'>
              { message }
            </p>
            { this.renderError() }
            { this.renderComponentStack() }
          </div>
        )
      }

      return (
        <TargetComponent { ...this.props } />
      )
    }

    renderError () {
      const { error } = this.state

      if (!error) {
        return null
      }

      return (
        <div className='error-boundary-info'>
          <h6>Error Stack:</h6>
          <div>
            {
              error.stack.split('\n').map((line, index) => {
                return (
                  <p key={ index }>{ line }</p>
                )
              })
            }
          </div>
        </div>
      )
    }

    renderComponentStack() {
      const { info } = this.state

      if (!info || !info.componentStack) {
        return null
      }

      return (
        <div className='error-boundary-info'>
          <h6>ErrorInfo componentStack:</h6>
          <div>
            {
              info.componentStack.split('\n').map((line, index) => {
                return (
                  <p key={ index }>{ line }</p>
                )
              })
            }
          </div>
        </div>
      )
    }
  }
  return ErrorBoundary
}
```


### 完整新生命周期

```javascript
import React from 'react'

export default class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    // 初始化state方式（1）
    this.state = {

    }
  }

  static defaultProps = {

  }

  // 初始化state方式（2）
  state = {

  }
  static getDerivedStateFromProps(props, state) {
    return state
  }
  componentDidCatch(error, info) {

  }
  render() {

  }
  componentDidMount() {

  }
  shouldComponentUpdate(nextProps, nextState) {

  }
  getSnapshotBeforeUpdate(prevProps, prevState) {

  }
  componentDidUpdate(prevProps, prevState, snapshot) {

  }
  componentWillUnmount() {

  }

}

```

以上~
---
title: Redux-saga
tags: 
    - react
    - redux
---

公司的react项目，在管理异步action这一块，都用的是redux-saga这个中间件。之前自己在做项目/练习的时候，都是用redux-thunk这个比起saga轻量很多的解决方案。
这两天正好在学习和摸索redux-saga。顺便总结一下。

### redux-thunk/redux-saga
首先redux-thunk和redux-saga都是redux的中间件，目的是解决redux应用中的异步问题。redux本身是不支持异步的，action creator直接返回一个对象。这里redux-thunk通过十几行代码，判断action creator函数返回的是不是一个对象，不是对象是函数的话再用dispatch发出去。之前自己写过一个简单的redux-thunk的模拟。大致就是这样的
```javascript
const thunk = ({dispatch, getState}) => next => action => {
  // 如果是函数，执行以下；参数是dispatch和getState
  if (typeof action === 'function') {
    return action(dispatch, getState)
  }
  // 啥事没干 原样返回
  return next(action)
}
export default thunk

```
redux-saga也是解决同样的问题，但是redux-saga是通过在redux原有的数据流中再加了一层，对action进行监听。捕获到监听的Action后可以派生一个新的任务对state进行维护，更改state来驱动view层变化。saga和thunk都是可以处理异步操作和协调复杂的dispatch。不同额是thunk是在action创建的时候才调用，saga是在应用入口就启动了，一直在监听action，并作出相应处理。说白了就是saga将所有的异步操作逻辑收集在一个地方集中处理。相比thunk的处理方式，更能剥离redux应用的副作用，让数据处理更加优雅（也更加麻烦=。=）

saga核心原理是利用了generator，使异步操作流程更加优雅。sagas都是用Generator函数实现的。
- 以generator形式组织逻辑序列（function\* + yield），把一系列的串行/并行操作通过yield拆分开
- 利用iterator的可“暂停/恢复”特性（iter.next()）分步执行
- 通过iterator影响内部状态（iter.next(result)），注入异步操作结果
- 利用iterator的错误捕获特性（iter.throw(error)），注入异步操作异常

### 使用
下面说说怎么引入redux-saga
```javascript
import { createStore, applyMiddleware } from 'redux'
import createSagaMiddleware from 'redux-saga'
//引入saga文件
import { rootSaga } from './rootSaga'
//使用 redux-saga 模块的 createSagaMiddleware 工厂函数来创建一个 Saga middleware。
//运行 rootSaga 之前，我们必须使用 applyMiddleware 将 middleware 连接至 Store。然后使用 

const sagaMiddleware = createSagaMiddleware()
const middlewares = [ sagaMiddleware ]

const store = createStore(rootReducer, applyMiddleware(...middlewares))
sagaMiddleware.run(rootSaga)
```
<!-- more -->

### 一些Api
在使用上是通过yield Effects的方式完成的。
（An Effect is simply an object which contains some information to be interpreted by the middleware.）

- call:yield call(Generator, param)yield一个call 到Generator,saga等待Generator执行之后,接收返回值继续执行,call是堵塞的,就是等待执行完再继续执行,返回的是执行完正常返回的结果.
- take:是阻塞的,只有监听到他(action.type),才会继续往下执行.
- put:类似dispatch方法,触发一个action
- fork:非阻塞的,遇到它不需要等待他执行完毕,就可以继续往下执行,fork返回的是一个任务,可以被取消
- cancel:针对fork方法返回的任务,进行取消

### redux-saga helper
- take：语义相当于once

- takeEvery：语义相当于on，允许并发action（上一个没完成也立即开始下一个）

- takeLatest：限制版的on，不允许并发action（pending时又来一个就cancel掉pending的，只做最新的）

- takeEvery, takeLatest是在take之上的封装，take才是底层API，灵活性最大，能手动满足各种场景

### 一些使用场景

- 接口访问
```javascript
function* fetchProducts() {
  try {
    const products = yield call(Api.fetch, '/products')
    yield put({ type: 'PRODUCTS_RECEIVED', products })
  }
  catch(error) {
    yield put({ type: 'PRODUCTS_REQUEST_FAILED', error })
  }
}
```
- Login/Logout
```javascript
function* loginFlow() {
  while (true) {
    yield take('LOGIN')
    // ... perform the login logic
    yield take('LOGOUT')
    // ... perform the logout logic
  }
}
```
这样写就能保证action的执行顺序。*LOGOUT*永远在*LOGIN*之后发生。

### 优缺点
- 优：
redux-saga的加入让流程拆分的更加细化。以同步的方式去写异步代码，代码层次结构更加清晰。便于测试
- 缺：
拆分细化意味着流程上多一个环节，增加了代码复杂度，和开发工作量。

---
学习这一块，细化拆分专门写了个例子，感觉把能用的都用了：https://github.com/laclys/Ezreal


以上~
~
~
~

---
title: 函数式编程与Redux
tags: 
	- Redux 
	- 函数式编程
---

说道函数式编程，JS这边Redux是代表中的代表。

以Redux的reducers函数为例，它是标准的纯函数，这里举个最简单的例子，说明为什么要用纯函数：

首先是源码
```javascript
unction posts(
  state = {
    isFetching: false,
    didInvalidate: false,
    items: []
  },
  action
) {
  switch (action.type) {
    case INVALIDATE_SUBREDDIT:
      return Object.assign({}, state, {
        didInvalidate: true
      })
    case REQUEST_POSTS:
      return Object.assign({}, state, {
        isFetching: true,
        didInvalidate: false
      })
    case RECEIVE_POSTS:
      return Object.assign({}, state, {
        isFetching: false,
        didInvalidate: false,
        items: action.posts,
        lastUpdated: action.receivedAt
      })
    default:
      return state
  }
}
```
如果我们不用return，在reducer内修改旧的state变量。变为不纯的函数，是这样的。

<!-- more -->

```javascript
  switch (action.type) {
    case INVALIDATE_SUBREDDIT:
      state = Object.assign({}, state, {
        didInvalidate: true
      })
    case REQUEST_POSTS:
      state = Object.assign({}, state, {
        isFetching: true,
        didInvalidate: false
      })
    case RECEIVE_POSTS:
      state = Object.assign({}, state, {
        isFetching: false,
        didInvalidate: false,
        items: action.posts,
        lastUpdated: action.receivedAt
      })
    default:
      return state
  }

```
在Redux中combineReducers.js中看下redux是如何处理的
```javascript
    let hasChanged = false
    const nextState = {}
    for (let i = 0; i < finalReducerKeys.length; i++) {
      const key = finalReducerKeys[i]
      const reducer = finalReducers[key]
      const previousStateForKey = state[key]
      const nextStateForKey = reducer(previousStateForKey, action)
      if (typeof nextStateForKey === 'undefined') {
        const errorMessage = getUndefinedStateErrorMessage(key, action)
        throw new Error(errorMessage)
      }
      nextState[key] = nextStateForKey
      hasChanged = hasChanged || nextStateForKey !== previousStateForKey
    }
    return hasChanged ? nextState : state
  }
```
这两行
```javascript
 const nextStateForKey = reducer(previousStateForKey, action)
```
```javascript
hasChanged = hasChanged || nextStateForKey !== previousStateForKey
```

reducer将旧的状态（prev）和要修改的数据一起传进去，然后返回一个新的（next）状态，prev和next相比较来确定storge数据是否改变。如果我们用不纯的函数，prev和next将一致，就算数据改变，hasChanged也会是false。通过函数式的方式没有什么“副作用”

Redux全篇都是函数式编程，这里只是冰山一角。Redux的源码写得非常优雅。
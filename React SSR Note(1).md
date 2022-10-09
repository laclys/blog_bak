---
title: React SSR Note(1)
tags: 
  - React
  - SSR
---


SSR已经不是什么新词、新概念了。为了解决React、Vue这类单页面（SPA）带来的首屏过慢以及SEO等问题随之而出。Vue、React官方也一直再跟进相关的支持。
同时社区也诞生了Next.js(React)以及Nuxt(vue)这类已经配置好，开箱即用的SSR解决方案。

之前在研究学习Webpack的时候，折腾过React SSR的配置和使用。[从零配置Webpack4.0搭建一个React工程](https://laclys.github.io/2018/04/09/%E4%BB%8E%E9%9B%B6%E9%85%8D%E7%BD%AEWebpack4.0%E6%90%AD%E5%BB%BA%E4%B8%80%E4%B8%AAReact%E5%B7%A5%E7%A8%8B/)并且在蛮多自己瞎搞的项目中使用过，这几周自己再折腾SSR与Redux的结合。这一全面总结一下，就当温故知新了。

### 什么是服务端渲染/为什么使用服务端渲染
服务端渲染顾名思义浏览器通过url向服务器发送请求，服务器处理好页面的内容将整个页面返还给浏览器。每当请求一个url，服务器要将该url对应的页面内容发送给浏览器。

而SPA单页面应用则是在一开始一股脑将整个bundle.js加载进来。具体流程如下：

浏览器发送请求 -> 服务器返回HTML(空的骨架) -> 浏览器发送bundle.js -> 服务器返回bundle.js ->浏览器执行React/Vue代码 

之后所有的url变成操作都通过这个js来处理，不需要再重新加载整个页面。web应用更具响应性，用户体验更好。但是由于一开始要加载一大坨bundle.js就会带来首屏过慢的问题。由于整个网页的内容都在js中，自然而然html源码什么都没有，不利于SEO。

为了解决痛点SSR就来了。它不是传统意义的服务端渲染，只是`首屏渲染`，首屏通过服务器直接生成，之后的由客户端（浏览器）接管。这样既能解决首屏速度、SEO还能同事拥有单页面应用带给用户的流畅性。(这里有一个专有名词叫做：同构--一套代码在服务器执行一遍，在客户端执行一遍) 具体流程如下：

浏览器发送请求 -> 服务器运行React代码生成页面 -> 服务器返回页面 -> 浏览器加载bundle.js -> bundle.js的React代码在浏览器重新执行 -> bundle.js的React重新接管页面操作。

这里主要说说React中的SSR核心由于虚拟DOM的存在(真实DOM的JS对象映射)
在SPA中
```javascript
ReactDom.render(<Home />, id/root)
```
在SSR中虚拟dom的存在让SSR变得很简单
```javascript
renderToString(<Home />)
```
把虚拟dom转化为字符串，返给服务器就是了

<!-- more -->

### Simple Demo

首先可以在分别创建server文件夹及client文件夹。我们这里简单的写写一套相同内容的代码
server/index.js
```javascript
import express from 'express'
import React from 'react'
import { renderToString } from 'react-dom/server'
import { StaticRouter } from 'react-router-dom'

import Routes from '../routes'

const app = express() // 通过express搭建一个服务器

app.use(express.static('public')) // 只要请求静态文件就在public这个文件夹中找

app.get('/', function(req, res) {

  const content = renderToString(  // 在server使用react-router-dom需要用StaticRouter
    <StaticRouter location={ req.path } context={{}} >
      { Routes }
    </StaticRouter>)
  // 通过 <script src='/index.js' ></script> 在调用client
  res.send(`
    <html>
      <head>
        <title>ssr</title>
      </head>
      <body>
        <div id='root' >${ content }</div>
        <script src='/index.js' ></script> 
      </body>
    </html>
  `)
})

var server = app.listen(3000)

```
client/index.js
```javascript
import React from 'react'
import ReactDom from 'react-dom'
import { BrowserRouter } from 'react-router-dom'
import Routes from '../routes'

const App = () => {
  return (
    <BrowserRouter>
      { Routes }
    </BrowserRouter>
  )
}

ReactDom.hydrate(<App />, document.getElementById('root'))
```
client端就是我们熟悉的React代码这里就不在废话

sever、client共用了一个路由配置
routes.js

```JavaScript

import React from 'react'
import { Route } from 'react-router-dom'

import Home from './containers/Home' //一个简单的Component

export default (
  <div>
    <Route path='/' exact component={ Home } ></Route>
  </div>
)
```

这里着重说一下webpack配置.这里拆成个三个文件。webpack.base.js 、webpack.client.js、webpack.server.js

```javascript
// webpack.base.js 
module.exports = {
  module: {
    rules: [{
      test: /\.js?$/,
      loader: 'babel-loader',
      exclude: /node_modules/,
      options: {
        presets: ['react', 'stage-0',['env', {
          targets: {
            browsers: ['last 2 versions']
          }
        }]]
      }
    }]
  }
}
```
```javascript
// webpack.client.js
const path = require('path')
const merge = require('webpack-merge')
const config = require('./webpack.base')

const clientConfig = {
  mode: 'development',
  entry: './src/client/index.js',
  output: {
    filename: 'index.js',
    path: path.resolve(__dirname, 'public')
  }
}

module.exports = merge(config, clientConfig)
```

```javascript
// webpack.server.js
const path = require('path')
const nodeExternals = require('webpack-node-externals') // 排除 node_modules 目录中所有模块
const merge = require('webpack-merge')
const config = require('./webpack.base')

const serverConfig = {
  target: 'node',
  mode: 'development',
  entry: './src/server/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'build')
  },
  externals: [nodeExternals()]
}

module.exports = merge(config, serverConfig)
```
这样一个最简单的SSR Demo就搞定了

### SSR与Redux、React-Router
SSR同构配合Redux使用还蛮复杂的，中间涉及到数据的注水和脱水。首先我们面临的第一个问题是server端怎么根据路由，往store里面加数据。
这里我们通过数组对象的方式设置Route。完整Project： https://github.com/laclys/rengar

比如：
```javascript
export default [{
  path: '/',
  component: App,
  loadData: App.loadData,
  key: 'app',
  routes: [{
      path: '/',
      component: Home,
      exact: true,
      loadData: Home.loadData,
      key: 'home'
    },
    {
      path: '/translation',
      component: Translation,
      exact: true,
      loadData: Translation.loadData,
      key: 'translation'
    },
    {
      component: NotFound,
    }
  ]
}]
```
这么一个路由。用过`loadData`进行server端对应数据的拉取（server端没有生命周期）。比如在Home这个组件里我们定义了一个loadData静态方法
```javascript
Home.loadData = (store) => {
  // 负责在服务器端渲染之前，把这个路由需要的数据提前加载好
  return store.dispatch(getHomeList())
}
```
顺便提一下客户端拉取数据当然
```javascript
  componentDidMount() {
    if (!this.props.list.length) { // 这层判断为了避免server端已经拉取数据了，没必要重复拉取。至于怎么从server渲染的页面拿到数据后面再说
      this.props.getList()
    }
  }
```

有了这些准备就可以在server端生成首屏的html。这里`import { matchRoutes } from 'react-router-config'`使用matchRoutes来遍历routes
```javascript
app.get('*', function (req, res) {
  const store = getStore(req)
  // 根据路由的路径，往store里面加数据
  const matchedRoutes = matchRoutes(routes, req.path)
  const promises = []
  matchedRoutes.forEach(item => { // 匹配含有loadData的路由，执行并赋值到store中
    if (item.route.loadData) {
      const promise = new Promise((resolve, reject) => {
        item.route.loadData(store).then(resolve).catch(resolve)
      })
      promises.push(promise) // 不管数据加载成功还是失败，把能准备的数据都准备好。更好的容错不必担心那个接口请求失败。都会执行promise.all
    }
  })
  Promise.all(promises).then(() => {
    const context = {}
    const html = render(store, routes, req, context)  // 生成html。

    if (context.action === 'REPLACE') {
      res.redirect(301, context.url)
    } else if (context.NOT_FOUND) {  // 处理404页面
      res.status(404)
      res.send(html)
    } else {
      res.send(html)
    }
  })
})
```
渲染html的render函数

```javascript
export const render = (store, routes, req, context) => {

    const content = renderToString(
      <Provider store={ store } >
        <StaticRouter location={ req.path } context={ context } >
          <div>
            { renderRoutes(routes) }
          </div>
        </StaticRouter>
      </Provider>)
      
    return `
      <html>
        <head>
          <title>ssr</title>
        </head>
        <body>
          <div id='root' >${ content }</div>
          <script>
            window.context = {
              state: ${ JSON.stringify(store.getState()) }  
            }
          </script>
          <script src='/index.js' ></script>
        </body>
      </html>
    `
}

```

其中
```html
<script>
  window.context = {
    state: ${ JSON.stringify(store.getState()) }  
  }
</script>
```
通过这种方式将server端已经获取的store数据进行注入。到时客户端js接管之后直接从context中拿

client/index:
```javascript
const store = getClientStore() 

const App = () => {
  return (
    <Provider store={ store } >
      <BrowserRouter>
        <div>
          { renderRoutes(routes) }
        </div>
      </BrowserRouter>
    </Provider>
  )
}

ReactDom.hydrate(<App />, document.getElementById('root'))
```
getClientStore
```javascript
export const getClientStore = () => {
  const defaultState = window.context.state  // 客户端getstore时，拿server已经获取过得数据。这块叫做脱水
  return createStore(reducer, defaultState, applyMiddleware(thunk.withExtraArgument(clientAxios)))
}
```
`thunk.withExtraArgument`是redux-thunk中一个可以传入一个值。在使用redux-thunk时候调用

顺道提一下服务器端获取请求cookie这块。可以通过axios的create方法拿到
```javascript
const createInstance = (req) => axios.create({
  baseURL: 'http://47.95.113.63/ssr/',
  headers: {
    cookie: req.get('cookie') || ''
  }
})

export default createInstance

```

以上！
捣鼓之后SSR确实是一个有意思的东西。很多东西不能说是坑，只能说是自己还没了解到。后续还会再看看CSS样式在server端中的处理和更好的SEO处理。
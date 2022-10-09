---
title: ReactSSR样式及SEO
tags: 
  - React
  - ssr
  - seo
---

前一篇主要记录了一下SSR配置以及结合Redux的使用。这里简单说一下React SSR中样式处理和更优雅的SEO

### SSR样式
在React客户端渲染，添加样式很容易。写一个css样式文件，在对应组件中引用。标签上通过className这个属性调用对应样式就万事Ok了。当然我们需要在webpack中配置loader来解析css文件。一般的配置如下（使用css modules）：
```javascript
  module: {
    rules: [{
      test: /\.css?$/,
      use: ['style-loader', {
        loader: 'css-loader',
        options: {
          importLoader: 1,
          modules: true,
          localIdentName: '[name]_[local]_[hash:base64:5]'
        }
      }]
    }]
  }
```
需要先通过css-loader解析css文件，之后再通过style-loader将样式放在html的style标签中。

那么SSR也这样行吗~
```bash
yarn dev
```
跑一下服务，发现命令行报这个错误：
```
      return window && document && document.all && !window.atob;
      ^

  ReferenceError: window is not defined
```

<!-- more -->

原因在于服务器端渲染哪里有window对象，哪里有DOM啊。我们是通过虚拟DOM。`renderToString`这个方法生成出来的html字符串。`stackoverflow`搜了一下发现了[`isomorphic-style-loader `](https://github.com/kriasoft/isomorphic-style-loader)这个专门用于同构的style-loader。
话不多少搞起来。客户端的webpack配置不需要变更还是使用css-loader+style-loader。服务器端就使用css-loader+isomorphic-style-loader了(和style-loader用法一波一样)
```javascript
// webpack.server.js
  module: {
    rules: [{
      test: /\.css?$/,
      use: ['isomorphic-style-loader', {
        loader: 'css-loader',
        options: {
          importLoader: 1,
          modules: true,
          localIdentName: '[name]_[local]_[hash:base64:5]'
        }
      }]
    }]
  }
```
配置好了Run一下，不报错了但是会闪一下屏。禁用掉js发现server端生成的html并没有样式，当客户端JS接管程序之后才会有样式出现。这样的体验相当糟糕。
当然我们确实没有向服务器端生成的HTML添加style标签。
现在服务器返给我们的html是这样的
```javascript
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
```
这时我们想到了context这个玩意。在server端render之前。我们设置一个
```javascript
  let context = {
    css: []
  }
```
我们还知道在服务端渲染的时候有`this.props.staticContext`这样一个props拿到我们设置`context`。另外`isomorphic-style-loader`提供给我们了
_getCss()这个方法。可以在SSR过程中拿到样式。有了这两个必要条件。我们就可以在每一个用到样式的Component中通过`componentWillMount`这个生命周期
添加这样一段代码：
```javascript
  componentWillMount () {
    if (this.props.staticContext) { // 只有服务端渲染时候有this.props.staticContext以及_getCss()
      this.props.staticContext.css.push(styles._getCss())
    }
  }
```
这样样式就存储在`context`这个变量的css数组中咯，改造一下server端的html输出代码：
```javascript
const cssStr = context.css.length ? context.css.join('\n') : ''
  return `
      <html>
        <head>
          <title>ssr</title>
          <style>${cssStr}</style>
        </head>
        <body>
          <div id='root' >${content}</div>
          <script>
            window.context = {
              state: ${JSON.stringify(store.getState())}
            }
          </script>
          <script src='/index.js' ></script>
        </body>
      </html>
    `
```

万事👌，当然我们可以进一步优化，把componentWillMount所做的事情提出来搞一个HOC（高阶组件）。


withStylesHOC.js
```javascript
import React, {
  Component
} from 'react'

export default (DecoratedComponent, styles) => {
  return class NewComponent extends Component {
    componentWillMount () {
      if (this.props.staticContext) {
        this.props.staticContext.css.push(styles._getCss())
      }
    }

    render () {
      return <DecoratedComponent {...this.props} />
    }
  }
}

```
这样简单的封装一个HOC，之后涉及样式的时候直接通过`withStylesHOC`包裹一下就好。例如一个结合Redux的Home组件：
```javascript
export default connect(mapState, mapDispatch)(withStyle(Home, styles))
```
![](/assets/blogImg/20181020-1.jpg)
### SSR-SEO

费大力气通过一个node中间层去实现首屏的SSR，除开首屏速度之外，就是SEO这一大块了，对于一个商业网站来讲真的很重要。
SEO（Search Engine Optimization）-- 通过一些技术手段让网站在搜索引擎的排名尽量靠前一点。由于客户端渲染出来的网站只有`<div id='root'>`这样的html节点。大多数搜索引擎分析不出来网站上有什么。SSR直接渲染出来HTML，这样对搜索引擎就友好了很多。

#### SSR中的SEO

这里我们使用github上的一个库`react-helmet`首先需要在对应的页面组件中引入`react-helmet`，就可以在Helmet标签内自由添加title、meta咯
```javascript
// Home.jax
import { Helmet } from 'react-helmet'
class Home extends Component {
  render() {
    return (
      <Fragment>
        <Helmet>
          <title>SRR-Home</title>
          <meta name='description' content='this is a home Component' />
        </Helmet>
          ...
          ...
      </<Fragment>>
    )
  }
}
```
之后按照readme所说的。在server端这样处理
```javascript
ReactDOMServer.renderToString(<Handler />);
const helmet = Helmet.renderStatic();
```
并在返回的html字符串中 `${helmet.title.toString()}`  `${helmet.meta.toString()}`进行填充
```html
  <html>
    <head>
      ${helmet.title.toString()}
      ${helmet.meta.toString()}
      <style>${cssStr}</style>
    </head>
    <body>
      <div id='root' >${content}</div>
      <script>
        window.context = {
          state: ${JSON.stringify(store.getState())}
        }
      </script>
      <script src='/index.js' ></script>
    </body>
  </html>
```
重新跑一下 搞定！

当然SSR-SEO绝不这么简单。仅仅在页面上添加head标签内加上title 和meta标签影响是有限的。8102年的搜索爬虫已经不单单去匹配title和 description，而是全稳的匹配（也就是说title和descript有影响但是影响很小）搜索爬虫会把整个网站所有的文本收集起来进行分析。

#### 那么如何做好SEO
题外话顺便说一下如何做好SEO。一个网站无非三大块内容，文字、多媒体、链接。要做到的是文字的原创性，图片的原创性以及高清度还有站内链接尽量和站内内容相关。

就这样吧~
以上
![](/assets/blogImg/20181020-1.jpg)
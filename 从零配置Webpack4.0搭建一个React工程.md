---
title: 从零配置Webpack4.0搭建一个React工程
tags: 
	- webpack
	- React
	- ReactSSR
---

最近一直大部分精力都在搞App以及Node。前端这块作为初心，还是不能落下。这里不用cli。一步一步记录从零配置Webpack，来搭建起React项目。复习一下Webpack的配置。以及一些前端工程化的一些思考

### 前段工程化思考
说道前端工程化，最重要的一个目的就是：解放生产力。对源代码进行预处理、自动打包/自动更新页面显示、去处理图片依赖和正式环境统一这几点在开发中极大提高了开发的效率。在搭建起项目工程之后开发人员只需要关注业务/代码即可。第二点就是保证项目质量，在多人协作，不同环境下开发。通过code lint等约束以及git commit预处理保证代码风格的一致统一，这一点对后续的代码维护很重要。第三就是优化。通过前端工程化、自动化去合理的压缩合并资源文件。这些大部分恰巧webpack帮我们完成了。
在vue和react开发中，通过脚手架工具，生成项目之后，只要run一下项目就跑起来了。然而cli给我们提供的只是一个泛模板。然而我们想要去定制它、修改它。就需要深入了解它，知道他是怎么跑起来的。我想考虑的前端工程化，这是每一个前端er绕不开的话题。坑越早开越好>_<

### Webpack基础配置

这里首先我们要明确webpack工程架构的核心是什么？
-- loader机制 （不同loader去处理不同的文件。甚至你可以自己创造一个文件，并作出对应的loader来处理它）

<!-- more -->

首先 我们npm init创建一个项目。接下来安装初始化需要的依赖：
```bash
npm i react --save
npm i react-dom --save
npm i webpack --save
npm i webpack-cli --save
```
最基本的依赖包安装完毕之后，我们需要创建WebApp的入口文件。这里我们在根目录创建一个src文件夹，并创建两个空文件app.js(作为入口文件) 以及App.jsx（声明我们app页面上的内容）。
有了入口文件，就可以进行Webpack的基本配置。这里我们在根目录下创建build文件夹在下面webpack.config.js里进行配置：
![](/assets/blogImg/20180317-1.png)
```javascript
const path = require('path') // 引入‘path’，为了在这里使用绝对路径，避免相对路径在不同系统时出现不必要的问题

module.exports ={
  // 应用入口
  entry: {
    app: path.join(__dirname, '../src/app.js')  // app.js作为打包的入口
  },
  // 输出目录
  output: {
    filename: '[name].[hash].js',  //name代表entry对应的名字; hash代表 整个app打包完成后根据内容加上hash。一旦整个文件内容变更，hash就会变化
    path: path.join(__dirname, '../dist'), // 打包好之后的输出路径
    publicPath: '/public' // 静态资源文件引用时的路径（加在引用静态资源前面的）
  }
```
有了入口和输出。我们就可以启动webpack了。这里在app.js随便写一段简单的js代码执行：
```bash
webpack --config build/webpack.config.js --mode development
```
为了方便我们把这段代码加到script里：
```bash
  "build": "webpack --mode development --config build/webpack.config.js"
```

（Webpack4 不添加mode，会有：The ‘mode’ option has not been set. Set ‘mode’ option to ‘development’ or ‘production’ to enable defaults for this environment.警告。对应option：development or production）

就会在项目根目录下多出一个dist文件夹。里面就是压缩打包好的代码

### Webpack Loader基础
我们要处理.jsx以及.js文件。这里我们就需要在上面的webpack.config.js加入新配置
```javascript
  // 配置loader
  module: {
    rules: [
      {
        test: /.jsx$/, //使用loader的目标文件。这里是.jsx
        loader: 'babel-loader'
      },
      {
        test: /.(js)$/, //使用loader的目标文件。这里是.js
        loader: 'babel-loader',
        exclude: [
          path.join(__dirname, '../node_modules')  // 由于node_modules都是编译过的文件，这里我们不让babel去处理其下面的js文件
        ]
      }
    ]
  }
```
这里我们用babel-loader处理jsx文件。光在这里配置不想我们需要安装一些依赖
babel-loader以及babel-core(这是babel的核心处理代码)。有了这些我们在app.js和App。jsx写一个最简单的react代码：
```javascript
//app.js
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App.jsx'

ReactDOM.render(<App />, document.body)

//App.jsx
import React from 'react'

export default class App extends React.Component {
  render () {
    return (
      <div>this is app</div>
    )
  }
}

```
执行一下npm run build，发现报错。这是因为我们没有进行babel的配置。在根目录加上一个.babelrc的文件
```
{
  "presets": [
    ["es2015", {"loose": true}],
    "react"
  ]
}
```
并安装相关依赖：babel-preset-es2015、babel-preset-es2015-loose（这个是宽松模式）、babel-preset-react。

再次build，dist文件夹就会出现新打包的js文件咯

### 生成一个HTML
这里我们想要在dist文件夹生成一个html页面去查看 我们处理过后的js代码可不可以用。这里我们需要借助'html-webpack-plugin'这个plugin
```bash
npm i html-webpack-plugin -D
```
在webpack.config.js里添加
```javascript
const HTMLPlugin = require('html-webpack-plugin')
```
以及底部追加：
```javascript
  plugins: [
    new HTMLPlugin()   // 生成一个html页面，同时在webpack编译的时候。把我们所生成的entry都注入到这个html页面中,路径都是根据我们output配置的来走的。
  ]

```
另外不要忘了 将output配置中的publicPath设为空，便于我们测试。

 上面步骤走完，我们在build一下，发现dist文件夹中多出来了一个index.html文件。
![](/assets/blogImg/20180317-2.png)

 打开测试一下。发现我们的chrome上出现了“this is app”的字样 ~搞定！

### React服务端渲染配置
现在单页面应用大行其道的年代，为了解决SEO不友好以及首屏过长的纹理。服务端渲染（SSR）也是绕不开的。
react-dom是react专供web端开发渲染的工具。而服务端没有window/document等属性：ReactDOM.render(<App />, document.body)这句是用不了的。

下面就来说说node服务端执行环境代码在webpack下怎么搞定：
首先我们需要在src目录下创建一个server渲染的入口
```javascript
//server-entry.js
import React from 'react'
import App from './App.jsx'

export default <App />

```
之后在build中在区别开web端的webpack配置，另外创建一个新的配置文件暂且叫：webpack.config.server.js大部分内容都和web端的配置一样：

```javascript
const path = require('path') // 引入‘path’，为了在这里使用绝对路径，避免相对路径在不同系统时出现不必要的问题

module.exports ={
  target: 'node', //webpack打包出来的内容使用在什么环境下
  // 应用入口
  entry: {
    app: path.join(__dirname, '../src/server-entry.js')  // app.js作为打包的入口
  },
  // 输出目录
  output: {
    filename: 'server-entry.js',  // node端没有浏览器缓存这个概念，并且需要在node中直接import这个文件。故直接命名就好
    path: path.join(__dirname, '../dist'), // 打包好之后的输出路径
    publicPath: '',
    libraryTarget: 'commonjs2' // 打包出来js模块所使用的方案（umd、amd、cmd、commonJS）这里我们使用commonjs2，适用于node端
  },
  // 配置loader
  module: {
    rules: [
      {
        test: /.(jsx)$/, //使用loader的目标文件。这里是.jsx
        loader: 'babel-loader'
      },
      {
        test: /.(js)$/, //使用loader的目标文件。这里是.js
        loader: 'babel-loader',
        exclude: [
          path.join(__dirname, '../node_modules')  // 由于node_modules都是编译过的文件，这里我们不让babel去处理其下面的js文件
        ]
      }
    ]
  }
}
```
值得注意的就是我们需要加上target和output中libraryTarget这两个配置。
之后改一下我们的npm script：
```json
    "build:web": "webpack --mode development --config build/webpack.config.js",
    "build:server": "webpack --mode development --config build/webpack.config.server.js",
    "clear": "rimraf dist",
    "build": "npm run clear && npm run build:web && npm run build:server"
```
(rimraf是一个非常好用删除文件夹的库，当然也可以用rm的方式)
```bash
npm run build
```
走一波。发现dist多出了server-entry.js这个打包好的文件。打开秋一眼，有别于web打包（是一个自执行的方法）。
![](/assets/blogImg/20180317-3.png)
已经变成了node模块导出的方式咯。
这里我们用express搭建一个服务，看看是否成功：
```javascript
const express =require('express')
const ReactSSR = require('react-dom/server')
const serverEntry = require('../dist/server-entry').default
const app = express()

app.get('*', (req, res) => {
  const appStr = ReactSSR.renderToString(serverEntry)
  res.send(appStr)
})

app.listen(2333, () => {
  console.log('server is listening on 2333')
})
```
react SSR这块不是这篇blog的重点就不细说。这里需要注意的是。我们的server-entry是通过export default导出的与import配套。在node中用require的方式需要require('../dist/server-entry').default一下
这一块的知识点可以看阮一峰老师写的博文

启动服务器：

chrome上出现：this is app

搞定！

### 添加html模板

然而我们会发现，我们通过服务端渲染出来的只是：
![](/assets/blogImg/20180317-4.png)
显然不是我们想要的，二来，之前我们将<App/>直接挂在在body上其实也是不对的。这里我们迫切需要一个html模板。
在src下创建一个template.html文件：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
    <div id="root"><!--app--></div>
</body>
</html>
```
更改webpack.config.js配置，在HTMLPlugin中添加选项：
```javascript
    new HTMLPlugin({
      template: path.join(__dirname, '../src/template.html') // 以template.html作为模板文件生成html
    })   
```
因为牵扯静态文件。web端和werver端webpack的配置：输出目录中的publicPath: 'public'

当然app.js中就需要挂载在root节点上了。ReactDOM.render(<App />, document.getElementById('root'))

说完web端，再搞服务端。在server.js中改为：
```javascript
const express =require('express')
const fs = require('fs')
const ReactSSR = require('react-dom/server')
const serverEntry = require('../dist/server-entry').default
const path = require('path')
const tplPath = path.join(__dirname, '../dist/index.html')
const template = fs.readFileSync(tplPath, 'utf8') // 不指定utf-8,默认是buffer
const app = express()

app.use('/public', express.static(path.join(__dirname, '../dist'))) //静态文件指定请求返回

app.get('*', (req, res) => {
  const appStr = ReactSSR.renderToString(serverEntry)
  res.send(template.replace('<!--app-->', appStr))
})

app.listen(2333, () => {
  console.log('server is listening on 2333')
})

```
因为改了之前的代码，我们需要重新build一下再启动服务
```bash
npm run build && node server/server.js
```
![](/assets/blogImg/20180317-5.png)

这就出来了

最后 ：
![](/assets/blogImg/20180317-6.png)
告诉我们服务端渲染，需要用这个react16新加的这个方法ReactDOM.hydrate()
大概意思就是如果我们使用了服务端渲染，我们需要用ReactDOM.hydrate()，在web端去渲染内容。react回去对比客户端和服务端生成的代码
之间的差别。如果有差别，他会用客户端的代码替换掉服务端的代码。
```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App.jsx'

ReactDOM.hydrate(<App />, document.getElementById('root'))

```
再走一波就没有警告了。

### webpack-dev-server
webpack dev server是一个通过webpack配置启动的server

配置起来也很简单。
首先：
```bash
npm i webpack-dev-server -D
```
之后需要判断是否为开发环境
```javascript
const isDev = process.env.NODE_ENV === 'development'
```
将webpack.config.js中的moudle.exports,拿出来。赋值为const config
之后：
```javascript
if (isDev) {
  config.devServer = {
    host: '0.0.0.0',  // 我们可以允许我们用任意方式进行访问（127.0.0.1，localhost, 本机ip）
    port: '8888',
    contentBase: path.join(__dirname, '../dist'),
    // hot: true,  //启动热加载
    overlay: {  // 错误提醒弹窗小遮层
      errors: true //只显示error
    },
    // 和output配置对应起来
    publicPath: '/public',  // 访问所有静态路径都要前面加/public才能访问生成的静态文件
    historyApiFallback: {
      index: '/public/index.html' // 所有404的请求全部访问该配置下的url
    }
  }
}

module.exports = config

```

之后启动可以使用这条命令：
```bash
  cross-env NODE_ENV=development webpack-dev-server --config build/webpack.config.js --mode development
```
其中cross-env也需要安装。这里他是为了解决mac下(*nix)和win环境变量设置不一致这个问题。
执行完毕，http://localhost:8888/就可以使用了

### Webpack3 -> Webpack4一些变化
不是很全，自己配置时候碰到的，不断更新中···
- Webpack4之后，命令行启动都拆分开来放到了webpack-cli里。需要单独安装
- webpack4加入了mode这个option。不写会有警告（development/production）
- 4默认加了devtool这个选项
- webpack3中webpack.optimize.CommonsChunkPlugin在4里面被废弃掉了，
在config加配置，基本等同
```javascript
// node_modules里的代码打包到vendor里
optimization: {
  spliteChunk: {
    chunks: 'all'
  },
  runtimeChunk: true
}

```
- 4废弃
```javascript
plugins中defaultPlugins.concat([
  new webpack.NoEmitOnErrorsPlugin()
])
```

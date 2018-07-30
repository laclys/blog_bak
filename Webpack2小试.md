---
title: Webpack2小试
tags: 
	- webpack
	- 前端
---

前端的水很深很杂，因而前端相关的工具也像雨后春笋一个一个的冒出来。最近大热的应该就数webpack--这个模块化解决方案工具。

### Gulp/Grunt VS Webpack

首先这三者都是前端工具，都等让我们高效自动化的解决一些问题。但实质上webpack和Gulp/Grunt有本质的区别。

先谈谈前者。Gulp/Grunt两者的区别，暂且不在这说了。用处的话，两者可以帮我们自动刷新页面，压缩css/js/html以及便宜sass/less。简单的说只要你配置你需要的插件，就可以把以前需要手动做的事情让它帮你完成。那官方的话来说就是----TaskRunner。可以做到node能做到的任何事。

而Webpack，简单的说：就是一个针对Javascript代码模块的打包工具。作为一个 module bundler 存在。
在我的理解来看：webpack 更多的是用来打包前端代码。 webpack 并不暴露文件处理的细节，
你只是把一堆文件丢给它，告诉它用什么 loader 来处理什么文件，然后就可以等着输出了。

webpack相比gulp来说功能单一一些，更简化一些。当然两者也有交际例如什么预编译,压缩代码之类的。

<!-- more -->

### How to use Webpack2

首先需要npm install 相关的模块：
	npm i webpack --save-dev

之后在根目录下建一个webpack.config.js,来配置webpack。
```javascript
var webpack = require('webpack');
module.exports = {
    entry: './src/app.js',
    output: {
        path: __dirname + '/dist',
        filename: 'js/[name].bundle.js',
    }}
```
entry为webpack寻找的入口。output便是处理之后输出文件的地址。(\__dirname这里指根目录)
在entry中的js文件里我们可以通过import或者require引入依赖。
源文件都放在了src目录中:
└─src
    └─www
        ├─css
        ├─img
        └─js
在src目录下有一个app.js。如下：
```javascript
import './www/js/js-test.js'
```
分别调用了css/js文件夹的内容。
test-css.css
```css
*{
    margin: 0;
    padding: 0;
}
div{
    width: 100px;
    height: 100px;
    /*background: red;*/
    background: url(../img/test-img.jpg);
    background-size: contain;
}
.flex{
    display: flex;
}
```
js-test.js
```javascript
function a(){
    console.log(12345);
}
```
之后在命令行就可以运行webpack了。不过在这之前 我们可以在package.json文件中对webpack命令进行一下小配置：
在script下添加
	    "webpack": "webpack --config webpack.config.js --progress --display-modules --colors --display-reasons"

*分别是显示过程 显示模块 高亮 以及执行原因*

```bash
npm run webpack
```
在dist文件夹就生成了 打包好的js文件：main.bundle.js。

### Loaders

前面 说的都是关于打包JS代码。我们的webpack当然能力不仅仅是这些。通过各种文件的loader，我们可以处理各种文件。。前提是我们将他传入js代码中。
比如处理css的css-loader/style-loader。css预处理loader：postcss-loader。处理sass的sass-loader。图片文件的file-loader和url-loader。这些loader我们都可以通过npm i 来下载使用。
这里简单说一个css-loader/style-loader。
首先我们要在app.js中引入css文件
```javascript
import './www/css/test-css.css';
```
之后在webpack.config.js中配置插件
```javascript
module: {
        loaders: [{
                test: /\.css$/,
                loader: 'style-loader!css-loader'//从右向左执行。先执行css-loader，再执行style-loader
            }
```
之后在命令行运行一下就ok了。
webpack的使用很灵活处理ES6=>ES5。给css代码加前缀····
github：https://github.com/laclys/webpack2_Demo/tree/master/webpack0329

以上~
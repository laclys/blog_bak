---
title: Snowpack
tags:  
	- 前端
	- bundleless
---

### 什么是`Snowpack`
> Snowpack is a modern, lightweight toolchain for web application development. Traditional dev bundlers like webpack or Parcel need to rebuild & rebundle entire chunks of your application every time you save a single file. This introduces lag between changing a file and seeing those changes reflected in the browser, sometimes as slow as several seconds.

> We call this new approach O(1) Build Tooling.

  简而言之就是利用原生`ES Modules`(几乎所有现代浏览器都支持这个特性，~~当然除了IE~~, 支持IE11),无需打包工具（Webpack，Parcel）便能将代码结果实时展现在浏览器中的`No build step needed!`构建工具。还有一个宣传点就是`Snowpack`是一个时间复杂度`O(1)`的构建系统。像webpack这种打包工具是一个时间复杂度`O(n)`的过程，一个文件变更，你不止重新构建这一个文件，而是多个相关文件甚至整体一整块，要经历`build -> bundle recompilation`这个过程。`Snowpack`呢，则是分别构建每一个文件 。在开发过程中，更改了某个文件，只需要重建这一个文件`build(file) => result`单个文件单个缓存(换言之，如果你不变更xx文件，那就永远不用重构它)。加载页面也是，它只会请求当前所需的那些文件。开发环境只要一次`Snowpack`，修改源码能够实时反馈在浏览器上，可以获得更快的开发体验。当然`Snowpack`也不是完完全全的`bundleless`，当然这个是后话

### 支持
- 框架： `React`、`Preact`、`Vue`、`Svelte`、`Tailwind CSS`(`snowpack2.0` 已经内置了 JSX 和 Typescript 文件的处理)
- Tooling: `Babel`、`TypeScript`、`PostCSS`
- 官方提供模板：`Create Snowpack App`
- HMR
- Dev Request Proxy
- Environment Variables
- HTTPS/HTTP2(**HTTP2的多路复用、合并请求就能解决`Snowpack`多构建文件的问题**)

<!-- more -->

### Start
Snowpack官方提供了创建项目的模板，这里使用`@snowpack/app-template-react`
> npx create-snowpack-app demo --template @snowpack/app-template-react

创建完的目录是这样的
![](/assets/blogImg/20200720-1.jpg)

这里说明几点
- public 文件下用来存放公共文件以及模板, 可以直接通过路径访问,如 public 文件下的 favicon.ico 开启可以通过 http://localhost:8080/favicon.ico 来打开

- src 文件 放置组件和页面还有样式等 web 内容, src 里的静态内容通过编译以后可以通过 _dist_ 来访问,如 http://localhost:8080/_dist_/logo.svg
[Demo]: https://github.com/laclys/skarner


- snowpack是支持热更新的
```javascript
// index.jsx
if (import.meta.hot) {
  import.meta.hot.accept();
}

```

> 不像 webpack 使用向页面添加 script 标签来加载新模块，snowpack 直接使用了原生的 dynamic import 来加载新模块,也算符合
node_modules/snowpack/assets/hmr.js
```javascript
/** Called when a new module is loaded, to pass the updated module to the "active" module */
async function runModuleAccept(id) {
  const state = REGISTERED_MODULES[id];
  if (!state) {
    return false;
  }
  if (state.isDeclined) {
    return false;
  }
  const acceptCallbacks = state.acceptCallbacks;
  const updateID = Date.now();
  for (const {deps, callback: acceptCallback} of acceptCallbacks) {
    const [module, ...depModules] = await Promise.all([
      import(id + `?mtime=${updateID}`),
      ...deps.map((d) => import(d + `?mtime=${updateID}`)),
    ]);
    acceptCallback({module, deps: depModules});
  }
  return true;
}
```


### 问题
1. 不完全的`bundleless`： 开发环境业务代码直接`bundleless`。对`node_modules`做了一层bundle处理。生产环境打包提供两套技术`module/nomodule`.官方维护了两套打包插件（`@snowpack/plugin-webpack` / `@snowpack/plugin-parcel`为生产环节打包。也算是一种渐进式 但是需要承担**开发与生产环境构建结果不一致的风险**
2. 对`sass`、`less`处理不够友好，需要手动处理.
```json
"scripts": {
  "run:sass": "sass src/css:public/css --no-source-map",
  "run:sass::watch": "$1 --watch"
}
```
(怎么处理css的)
```javascript
// 这种语法目前浏览器是不支持的
import './style.css'
```
`snowpack`将`css`文件变成一个注入样式的`js模块` （import './style.css.proxy.js';）
同理图片 demo.png -> demo.png.proxy.js

3. 对`ESM`强依赖,随之带来了很多 `CommonJS` 的模块依赖引入问题。
比如antd需要以这种方式使用
```javascript
import Button from 'antd/es/button';
import List from 'antd/es/list';
import Avatar from 'antd/es/avatar';
// css
import 'antd/es/button/style/css';
import 'antd/es/list/style/css';
import 'antd/es/avatar/style/css'
```

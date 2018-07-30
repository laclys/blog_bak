---
title: VSCODE1.8下TypeScript（win10）
tags: 
	- 前端
	- TypeScript
	- vscode
---

### TypeScript
TypeScript是微软开发的一个Js的超集，TypeScript兼容JavaScript，可以载入JavaScript代码然后运行。完全支持ES6规范。
TypeScript也是Google公司推出前端框架Angular2的开发语言。
(可以说是Google，微软强强联手吧。一直想学Angular框架自然的学学这TypeScript)

由于TypeScript 是 JavaScript 的超集，扩展了 JavaScript 的语法，TypeScript 可处理已有的 JavaScript 代码，并只对其中的 TypeScript 代码进行编译。首先就是搭建开发环境。
### compier
编译器分两种一种是在线编译器，在百度搜索typescript，就可以找到：http://www.typescriptlang.org/play/index.html
可以将TS代码转化成JS代码。
另一种就是本地编译器。由于自己一直用vscode这一轻量级ide。网上资料不是很对。就来码一下自己的安装配置过程。
 <!-- more -->
#### Go：
*本文基于node.js下进行*
下载最新TypeScript：
	npm install -g typescript
现在最新的是2.1
更新TypeScript：
	npm update -g typescript
![](/assets/blogImg/20170131-1.jpg)
查看当前版本：
	tsc -v
安装完typescript之后打开vscode，创建一个workspace并在其下添加一个tsconfig.json。设置以下参数：

```json
{
	"compilerOptions": {
		"target": "es5",
		"noImplicitAny": false,
		"module": "amd",
		"removeComments": false,
		"sourceMap": true
	}
}
```
![](/assets/blogImg/20170131-4.jpg)
之后创建一个main.ts测试:（我们使用在线编译器typescriptlang下的helloworld代码）
```javascript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");

let button = document.createElement('button');
button.textContent = "Say Hello";
button.onclick = function() {
    alert(greeter.greet());
}

document.body.appendChild(button);
```
![](/assets/blogImg/20170131-5.jpg)
之后按下快捷键「Ctrl+Shift+B」：
![](/assets/blogImg/20170131-6.jpg)
提示‘配置任务运行程序’。点击选择tsconfig.json 创建TypeScript项目。之后生成一个.vscode文件夹。里面有该项目的运行的配置设置：
![](/assets/blogImg/20170131-7.jpg)
完成设置。
再次按下快捷键「Ctrl+Shift+B」，生成main.js和main.js.map文件。大功告成。
![](/assets/blogImg/20170131-8.jpg)
Visual Studio Code编译TypeScript,并且输出对应的JavaScript档案！

ps：春节就这么过完了=。=
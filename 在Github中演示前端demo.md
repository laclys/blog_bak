---
title: 在Github中演示前端demo
tags: 
	- 前端 
	- github
---
在浏览他人Github时，经常能看到他们的前端项目的Readme.md中，放有在线演示Demo。自己也想实现这样的效果，在网上自己摸索。总结为下文=。=

### 需求：
一般情况，我们将自己写成的网页上传到github仓库，点进相应的html界面出来的是该文件的代码。他人看起来非常不直观。
![](/assets/blogImg/20170207-1.jpg)
因此，我们需要一个在线演示demo的平台。
 <!-- more -->
### 两种方法
#### 方法一：
这种方法比较简单。通过增加路径前缀的方法，实现在线demo浏览。
在github中随便找一个html演示：例如前些天写的瀑布流：*HTML-CSS-JS_practice/JS/瀑布流/瀑布流.html*。其的页面路径为：https://github.com/laclys/HTML-CSS-JS_practice/blob/master/JS/瀑布流/瀑布流.html 这一大长串。我们只需要在这一大长串地址前面加上http://htmlpreview.github.io/? 
即可。
最后的浏览demo地址为：
http://htmlpreview.github.io/?https://github.com/laclys/HTML-CSS-JS_practice/blob/master/JS/瀑布流/瀑布流.html

这种方法看似很简单，可是有缺陷的。会更改css样式，不建议使用。

#### 方法二：
普遍采用的一种方法，通过使用Githubpages。
首先，在我们需要演示的项目中，增加gh-pages分支（正常情况下只有一个master分支）。
![](/assets/blogImg/20170207-2.jpg)
把需要演示的页面代码克隆到这个分支上。
如果本地实在master分支下工作的，工作完成后，输入命令：
	git push origin master:gh-pages 
此命令的意思是把本地master分支的内容推送到远程的gh-pages。
如果远程没有gh-pages分支的话，会自动创建该分支
假如我们想浏览今儿做成的完美拖拽小demo：
HTML-CSS-JS_practice/JS/拖拽/完美拖拽（八个方向）.html
![](/assets/blogImg/20170207-4.jpg)
我们通过：*http://[用户名].github.io/[index.html所在目录]* 这种方式访问。
我的 githubpages为：laclys.github.io（也就是我的blog地址）
（如果有index.html,则会直接访问到主页）
则最后的浏览地址是：*laclys.github.io + HTML-CSS-JS_practice/JS/拖拽/完美拖拽（八个方向）.html*
[DEMO][1]
![](/assets/blogImg/20170207-5.jpg)
最后我们可以将这个地址添加到Readme上面去！

以上！

[1]: https://laclys.github.io/HTML-CSS-JS_practice/JS/%E6%8B%96%E6%8B%BD/%E5%AE%8C%E7%BE%8E%E6%8B%96%E6%8B%BD%EF%BC%88%E5%85%AB%E4%B8%AA%E6%96%B9%E5%90%91%EF%BC%89.html
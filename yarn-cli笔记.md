---
title: yarn-cli笔记
tags: 
	- yarn
	- 前端
---

前一阵看到一句话，搞技术既要仰望星空，又要脚踏实地。特别是前端，三天两头出来新东西。为了跟上时代，趋势。得学。。然而JS/CSS最基础的东西也不能落下。想学的的东西太多，坑越挖越多。亚历山大~，加油吧~
![](/assets/blogImg/20170502.jpg)
（济南·大明湖 2017-04-30）
这两天看了看Yarn，其实之前在微信公众号上看过，自己也照着敲了敲。之后就没在意。还是继续用NPM。没有GET到yarn的优势。恰逢看到一篇yarn vs npm的文章，好好学习了一番。facebook在技术领域的号召力也是没谁了。光看star数都快赶上老大哥npm了。

为什么用yarn？正如官网所说：**FAST, RELIABLE, AND SECURE **。

相比NPM，yarn缓存他下载下来的每一个包，无需重复下载。提高了资源利用率。
超级可靠！由于国内网络的特殊原因。有时候npm下载依赖的时候，不能保证包的完整性。会出现很多的“坑”！yarn的话，在每一个安装包的执行前使用校验码验证包的完整性。就不会面对那些坑了。
还有一个就是目录下的lock文件。Yarn 使用一个格式详尽但简洁的 lockfile 和一个精确的算法来安装，能够保证在一个系统上的运行的安装过程也会以同样的方式运行在其他系统上。
<!-- more -->
### 命令：

命令	操作	参数	标签
yarn add	添加依赖包	包名	--dev/-D
yarn bin	显示yarn安装目录	无	无
yarn cache	显示缓存	列出缓存包：ls，打出缓存目录路径：dir，清除缓存：clean	无
yarn check	检查包		
yarn clean	清理不需要的依赖文件		
yarn config	配置	设置：set <key> <value>， 删除：delete， 列出：list	[-g | --global]
yarn generate-lock-entry	生成锁定文件	无	无
yarn global	全局安装依赖包	yarn global <add/bin/ls/remove/upgrade> [--prefix]	--prefix 包路径前缀
yarn info	显示依赖包的信息	包名	--json：json格式显示结果
yarn init	互动式创建/更新package.json文件	无	--yes/-y：以默认值生成package.json文件
yarn install	安装所有依赖包		--flat：只安装一个版本；--force：强制重新下载安装；--har：输出安装时网络性能日志；--no-lockfile：不生成yarn.lock文件；--production：生产模式安装（不安装devDependencies中的依赖）
yarn licenses	列出已安装依赖包的证书	ls：证书列表；generate-disclaimer：生成免责声明	
yarn link	开发时链接依赖包，以便在其他项目中使用	包名	
yarn login	保存你的用户名、邮箱		
yarn logout	删除你的用户名、邮箱		
yarn list	列出已安装依赖包		--depth=0：列表深度，从0开始
yarn outdated	检查过时的依赖包	包名	
yarn owner	管理拥有者	ls/add/remove	
yarn pack	给包的依赖打包	--filename <filename>	
yarn publish	将包发布到npm		--tag：版本标签；--access：公开（public）还是限制的（restricted）
yarn remove	卸载包，更新package.json和yarn.lock	包名	
yarn run	运行package.json中预定义的脚本		
yarn self-update	yarn自身更新--未实现		
yarn tag	显示包的标签	add/rm/ls	
yarn team	管理团队	create/destroy/add/rm/ls	
yarn test	测试 = yarn run test		
yarn unlink	取消链接依赖包		
yarn upgrade	升级依赖包		
yarn version	管理当前项目的版本号	--new-version <version>：直接记录版本号；--no-git-tag-version：不生成git标签	
yarn why	分析为什么需要安装依赖包	包名/包目录/包目录中的文件名	
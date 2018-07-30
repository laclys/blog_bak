---
title:  JavaScript Standard Style
tags: 
	- javascript
	- react-native 
	- standard
---

最近公司新开的RN项目，使用了这份JS编码规范。在这里简单的介绍一下。

https://github.com/standard/standard
（star超级多=。=）

相比Eslint、JSCS这类代码检查工作，优势就是，自有一套社区规范化的[标准]风格。不用维护 .eslintrc, .jshintrc, or .jscsrc 。开箱即用。
自动代码格式化。在主流的编译器上sublime/vscode/Atom/Vim都有相应的插件提供支持。实际体验很好。

再来说说这一套JS编码规范的细则：
- 2 spaces – for indentation
- Single quotes for strings – except to avoid escaping
- No unused variables – this one catches tons of bugs!
- No semicolons – It's fine. Really!
- Never start a line with (, [, or `
- This is the only gotcha with omitting semicolons – automatically checked for you!
- Space after keywords if (condition) { ... }
- Space after function name function name (arg) { ... }
- Always use === instead of == – but obj == null is allowed to check null || undefined.
- Always handle the node.js err function parameter
- Always prefix browser globals with window – except document and navigator are okay
- Prevents accidental use of poorly-named browser globals like open, length, event, and name.
- And more goodness – give standard a try today!

仔细看这一串规范，大概最大的争议点是：没有分号。 Standard Style的作者Feross（google了一下，很NB的一个人）他自己认为不用分号的 JavaScript 非常好。
分号自动插入是 JavaScript 的一个特性，它可以减少噪点、简化程序。
<!-- more -->
看了这么长也许你会想，如果我不同意某条规则，可以改吗？

答案是不行的，Feross认为制定这套 standard 规范的目的就是让大家都不必再花时间浪费在无谓的代码风格之争上面了。遵循 standard 规范，你就不用再犹豫了，毕竟不管怎样争论总归会选择一种风格的。

----
好一阵子没写blog了，不知不觉在新公司有一阵子时间了。相比日本企业，国内的公司更自由，但压力也多了许多。从日本回来之前，这些都想到了。一点不后悔。在日本清闲了几年，回国了还是要好好努力把空缺补一补。
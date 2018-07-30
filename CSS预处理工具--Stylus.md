---
title: CSS预处理工具--Stylus
tags: 
	- 前端 
	- Stylus
	- CSS
---



![](/assets/blogImg/20171006-1.jpg)
（2017.10.4   兰州·黄河）

### 为什么是Stylus？

Stylus vs Sass ：Sass 在win下中文字符兼容不是太好。出现的比较早，相比之下少一些新晋的功能

Stylus vs PostCSS：势均力敌，速度都很快，支持自己写扩展。都是用node开发。

#### stylus优势：
1，语法上，无限接近JS。
2，极尽简化，又向上兼容。
3，强力的@extend （不仅可以继承类还有继承选择器）。
4，不需要复杂的配置。

#### 安装：
npm install stylus -g

#### 编译css：
stylus [options][文件|目录...] -o css/
eg: stylus .\index.stylus -o css

<!-- more -->

#### 常用option：
- -i 启动一个交互工具 （有很多有用的小函数）

```bash
> darken(#fff, 10%)
=> #e6e6e6
> rgb(201,163,59)
=> #c9a33b
```

- -U 把图片转化为Data URI
- -w 监视文件状态，自动编译
- -c 压缩输出的css
- -m 生成SourceMap
- --include-css 输出文件中包含@import导入的CSS

http://stylus-lang.com/try.html(官方游乐场= =)

#### 日常：
package.json:
```json
{
  "scripts": {
    "stylus": "mkdir css & stylus -m -w styl/screen.styl -o css/"
  }
}
```
GULP:
```bash
npm i gulp-stylus gulp-clean-css -D
```
gulpfile.js
```javascript
gulp.task('stylus', () => {
  return gulp.src('./styl/screen.styl')
    .pipe(stylus({
      compress: true,
      'include css': true
    }))
    .pipe(cleanCSS({
      level: 2,
      rebaseTo: 'css/'
    }))
    .pipe(gulp.dest(DEST + 'css/'));
});
```
#### 一些内部功能：

- 选择器\^[N..M]

```stylus
a
  b
    c
      d
        font-size 14px

        .abc ^[-1..-1]:hover
          color red
```
编译为：
```css
a b c d {
  font-size: 14px;
}
.abc d:hover {
  color: #f00;
}
```

- 引用属性值

```stylus
p
  margin 10px
  padding (@margin / 2)
```
编译为：
```css
p {
  margin: 10px;
  padding: 5px;
}
```
eg: 
```stylus
// 用绝对定位居中
#logo
  position absolute
  top 50%
  left 50%
  width 150px
  height 80px
  margin-left -(@width / 2)
  margin-top -(@height / 2)
  margin (-@height / 2) 0 0 (-@width / 2)
```
- 循环
```stylus
  for n in 1..10
  .col-{n}
    width 10% * n
```
  编译为：
```css
  .col-1 {
  width: 10%;
}
.col-2 {
  width: 20%;
}
....
```
- 条件判断 IF/ELSE
```stylus
$need-support-ie = true

body
  if $need-support-ie
    padding 5px
  else
    margin 5px
```
(直接修改开关)

- MIXIN & FUNCTIONS
  (全部参数 ARGS...)
```stylus
border-radius(n)
  -webkit-border-radius n
  -moz-border-radius n
  border-radius n

form input[type=button]
  border-radius(5px) // function
  border-radius 5px // mixin
  
box-shadow(args...)
   -webkit-box-shadow args
   -moz-box-shadow args
   box-shadow args

 a
   box-shadow 1px 2px 5px #eee
```

编译为:
```css
form input[type=button] {
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
}
a {
  -webkit-box-shadow: 1px 2px 5px #eee;
  -moz-box-shadow: 1px 2px 5px #eee;
  box-shadow: 1px 2px 5px #eee;
}
```

![](/assets/blogImg/20171006-2.jpg)
---
title: 项目图片优化：整体替换WEBP
tags:  
	- 前端
---


## 具体方案：

### 开发脚本 整体生成 WEBP图片(Eg.: 项目中a.png，再生成一份a.png.webp)

Shell脚本(需安装：brew install webp)(https://developers.google.com/speed/webp/docs/cwebp)
```bash
#!/bin/bash
# Usage: ./cwebp.sh <directory>
echo 'Converting all .png, .jpg, and .jpeg files to .webp...'

find $1 -type f -and \( -iname "*.jpg" -o -iname "*.jpeg" -o -iname "*.png" \) \
-exec bash -c '

if [ ! -f "$0.webp" ]; then
  cwebp -q 90 -mt -m 6 "$0" -o "$0.webp" 2>/dev/null;
fi
' $forced {} \;
```

<!-- more -->

前端项目脚本
```javascript
const { join,resolve } = require('path')
const { execSync } = require('child_process');
const _root = resolve(__dirname, '..')

const arg = process.argv[2];
if (!!arg) {
  const targetPath = join(...[_root].concat(arg))
  const scriptPath = join(...[_root].concat('script/cwebp.sh'))

  execSync(`${scriptPath} ${targetPath}`, { stdio: [0, 1, 2] });
}
```
Usage:
```bash
<pnpm | npm | yarn> run cwebp <directory>
```

### 判断浏览器是否支持WEBP，如支持使用WEBP格式图片，不支持使用原图 (Ref:https://caniuse.com/webp).如支持根节点添加标识: `<html class="webpa">...</html>`
```javascript
const isWebp = document.createElement("canvas").toDataURL("image/webp").includes("data:image/webp");
const htmDom = document.documentElement;
if(isWebp) {
  htmDom.classList.add("webpa");
}
```

### css/less...样式图片处理
以less为例其他样式预处理器同理
```css
/* webp mixin */
.webpbg(@url) {
      background-image: url(@url);
  .webpa & {
      background-image: url('@{url}.webp');
  }
}

// Usage

+ .webpbg(url)
- background-image(url)
```
项目mixin可通过webpack插件：`style-resources-loader` 统一引入

### JSX img标签处理
使用 兼容webp
Eg.
```javascript
import React from 'react';

const Img = (props) => (
  <picture className={props?.className}>
    <source
      className={props?.className}
      srcSet={`${props?.src}.webp`}
      type="image/webp"
    />
    <img className={props?.className} src={props?.src} alt={props?.alt} loading="lazy" />
  </picture>
  );

export default Img;

```
*并不是一股脑儿把所有图片转化成webp就是最优解，面对线性、矢量、渐变图片转化成svg反而更好。
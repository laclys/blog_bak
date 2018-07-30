---
title: node-path
tags: 
	- node
---



最近在看node，仿照anywhere，自己也撸了一个静态资源服务器。说道静态资源服务器，
大量运用了路径层级这块。一开始又是相对路径，绝对路径，暴露的接口也是一大堆，有点懵逼。
遂对着官方文档撸一遍，这里笔记总结一下。


### path.basename(path[, ext])

- path <String> 路径
- ext <String> 可选参数

path.basename()返回路径的最后一部分

eg:

```javascript
path.basename('/foo/bar/baz/asdf/quux.html');
// returns 'quux.html'
path.basename('/foo/bar/baz/asdf/quux.html', '.html');
// returns 'quux'
path.basename('/foo/bar/baz/asdf/quux.html', 'ux.html');
// returns 'qu'
```

<!-- more -->

### path.dirname(path)

- path <String> 路径

path.dirname()返回path的所在路径

eg:
```javascript
path.dirname('/foo/bar/baz/asdf');
// returns '/foo/bar/baz'
path.dirname('/foo/bar/baz/asdf/quux.html');
// returns '/foo/bar/baz/asdf'
path.dirname('quux.html');
// returns '.'
```

### path.extname(path)

- path <String> 路径

返回路径最后一部分中的最后一个.字符到结尾截取的字符串，如果没有字符.，或者路径最后一部分的开头是字符.，则会返回空字符串。

eg:
```javascript
path.extname('index.html')
// returns '.html'
path.extname('index.coffee.md')
// returns '.md'
path.extname('index.')
// returns '.'
path.extname('index')
// returns ''
path.extname('.index')
// returns ''
```

### path.normalize(path)

- path <String>

使字符串路径标准化

eg:
```javascript
path.normalize('/foo/bar///baz/asdf/quux/../');
    // return '/foo/bar/baz/asdf/' posix
    // return '\\foo\\bar\\baz\\asdf\\' windows
path.normalize('');
    // return '.'
```

### path.join([path1][, path2][, ...])

- pathX <String>

把所有字符串参数合到一起并返回标准化后的路径

eg:
```javascript
path.join('/foo/', 'bar', 'baz//asdf', 'quux', '..', 'test.html')
    // return '/foo/bar/baz/asdf/test.html'
path.join('/foo', '', 'test.html')
    // return '/foo/test.html'  如果有空字符串参数，则会被忽略
path.join('')
    // return '.'  如果所有参数合到一起是空字符串，则会返回'.'，表示当前目录
```

### path.isAbsolute(path)

- path <String>

判断path是否是绝对路径

eg:
```javascript
// Posix
path.isAbsolute('/foo/bar') // true
path.isAbsolute('/baz/..')  // true
path.isAbsolute('qux/')     // false
path.isAbsolute('.')        // false

// Windows
path.isAbsolute('//server')  // true
path.isAbsolute('C:/foo/..') // true
path.isAbsolute('bar\\baz')  // false
path.isAbsolute('.')         // false
```

### path.format(pathObject)

根据参数pathObject返回路径字符串。

eg:
```javascript
// If `dir` and `base` are provided, `dir` + platform separator + `base`
// will be returned.
path.format({
    dir: '/home/user/dir',
    base: 'file.txt'
});
// returns '/home/user/dir/file.txt'

// `root` will be used if `dir` is not specified.
// `name` + `ext` will be used if `base` is not specified.
// If only `root` is provided or `dir` is equal to `root` then the
// platform separator will not be included.
path.format({
    root: '/',
    base: 'file.txt'
});
// returns '/file.txt'

path.format({
    dir: '/',
    root: '/',
    name: 'file',
    ext: '.txt'
});
// returns '/file.txt'

// `base` will be returned if `dir` or `root` are not provided.
path.format({
    base: 'file.txt'
});
// returns 'file.txt'
```

### path.parse(pathString)

和path.format相反

eg:
```javascript
path.parse('/home/user/dir/file.txt')
// returns
// {
//    root : "/",
//    dir : "/home/user/dir",
//    base : "file.txt",
//    ext : ".txt",
//    name : "file"
// }
```

### path.relative(from, to)

该方法根据两个绝对路径from和to解析出一个相对路径（说白了就是从一个绝对路径到另一个绝对路径是怎么走的）

eg:
```javascript
path.relative('/data/orandea/test/aaa', '/data/orandea/impl/bbb')
// returns '../../impl/bbb'
```

### path.resolve([from ...], to)

简单讲就是执行一连串的cd命令，执行后返回一个绝对路径

eg:
```bash
cd foo/bar
cd /tmp/file/
cd ..
cd a/../subfile
pwd
```


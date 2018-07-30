---
title: Swift学习杂记(一)
tags: 
	- 移动端 
	- swift
---

半路出家搞RN，还是有必要学习学习原生的东西，最近在看Swift语法。这里杂乱的记一下。

![](/assets/blogImg/20171105-01.jpg)
（2017.11.3 ·水立方）

let关键字和var关键字分别表示常量和变量

Swift是一种let关键字和var关键字分别表示常量和变量

Swift是一种非常简洁的语言，其允许开发者省略分号，自动换行来分割语句，同事也支持在一行中编写多句代码

如果需要改变变量的值，直接对变量再赋值即可。需要注意：所复制的类型必须和变量的类型保持一致。

<!-- more -->

swift语言的注释一个有趣的特性：可以进行注释的嵌套
```swift
//单行注释//注释中的注释
/*
多行注释
    /*嵌套注释*/
*/
```

swift整数数据分有符号整数类型和无符号整数类型。

swift一个十分有意思的特性：无论是整数数据还是浮点数据。都可以在数字前加任意的0来进行位数填充，也可以在数字中加入下划线_进行分割，今儿增加可读性。

swift的BOOLl类型十分严格。只有true和false两种值。

swift元祖：允许一些不相关的类型进行自由组合成为新的集合类型。OC并不支持这种数据类型。

swift语言中常常使用“\_”来表示匿名的概念，因此“\_”也被称为匿名标识符

Swift语言中提供了一种包装的方式来对普通类型进行Optional包装，实现对空值情况的监控。。在swift语言中如果使用了一个没有进行复制的变量，程序是会直接报错终止的。

在普通类型后面添加符号“？”，既可以将普通配型包装为Optional类型。

optional类型不会独立存在，其总是会附着于某个具体的数据类型之上。

Optional类型是对普通类型的一种包装，因此在使用的时候也需要对其进行拆包操作。
```swift
//声明obj为String?类型
var obj:Srting? = "HS"
if obj != nil {
    obj!
}
```

为变量起别名：typealias

Swift是一种弱化指针的语言，它提供了String和Character类型来描述字符串与字符。

Swift语言有3种集合类型：Array，Set，Dictionary

字符串组合：除了+进行字符串拼接。swift提供了一种十分便捷的字符串插值方法。
```swift
// 使用\()进行字符串插值
var d = "Hello \(123)" //"Hello 123"

```
Swift语言中可以使用MemoryLayout枚举来获取某个类型所占的内存空间，其单位为字节：
```swift
MemoryLayout<String>.size //24字节
```

判断字符串变量是否为空：
```swift
obj1.isEmpty
obj1.characters.count == 0
```

```swift
// 获取字符串起始下标
var stringIndex = string.startIndex
// 结束下标
var endIndex = string.endIndex
```
注意：除非为空字符，否则结束下标志总会比字符串中最后一个字符的下标值大1；
另外startIndex和endIndex获取到的值是index类型，并不是整数类型

检查字符串前后缀：
```
// 检查字符串是否有MY前缀
string2.hasPrefix('MY')
// 检查字符串是否有jaki后缀
string2.hasSuffix("jaki")
```

Swift语言中Array采用结构体来实现，对于大量重复元素的数组，可以使用：
```swift
// 创建是个“Hello”元素的数组
var arr = [String](repeating: "Hello",count: 10)
```

\*只有当Array实例为变量的时候才可以使用增删改查等方法。常量let不行。

Array实例中有一个enumerated()的方法，这个方法会返回一个元祖集合，将数组的下标和对应元素返回。

Array排序函数：sort(isOrderedBefore:)的方式进行排序。
```swift
var arr = [1,3,5,7]
//从大到小
arr.sorted(isOrderedBefore: >)
//从小到大
arr.sorted(isOrderedBefore: <)
```
Set一个独有的的点可以进行数学的集合运算：交集、并集、补集等

Set虽然不强调顺序。但是可以先排序再遍历
```swift
for item in set1.sorted(isOrderedBefore: >) {
    print(item)
}

```
Swift字典类型：通过[param1:param2]这种结构用于表示字典类型，或者Dictionary<Int，String>这种方式。


区间运算：
swift支持Range结构体来描述范围外，还可以这样：
```swift
// 创建范围大于等于0，小于等于10
var range1 = 0...10
// 大于等于0，小于10.半开闭
var range2 = 0..<10
```
通过~=来检查某个数字是否包含在范围中
```swift
print(range1 ~= 8)
```
Swift提供了for-in,while,repeat-while三种循环结构
```swift
// 如果不需要获取循环中的次序，可以使用匿名函数的方式
var sum = 0
for _ in 1...3 {
    sum += 1
}
```
生成随机数
```swift
//1~7
// arc4random()为Swift标准库中的随机数生成函数
var rand = arcrrandom() % 7 + 1
```
### 闭包和函数
闭包和函数有着密不可分的关系：函数是有名称的功能代码块，闭包在绝大多数情况是没有名称的功能代码块。
swift的函数格式：
```
func 函数名(p1,p2...) ->returnValue {实现部分}
```
如果函数也不需要返回值，可以选择返回Void或者直接省略掉返回值部分。

Swift语言中有一个使用技巧，开发者可以通过Optional类型来标识函数的执行是否成功。在调用函数时使用if-let进行安全检查。
eg：
```swift
func onMyOMG(param: Int) -> Int? {
    guard param > 100 else {
        return nil
    }
    return param - 100
}
if let temp = ohMyOMG(param: 101) {
    print(temp)
}
```

Swift语言的参数传递，有一个特点：传递的如果是值类型的参数，那么参数值在传递进函数内部时会将原值拷贝为一份常量。且在函数内不能修改。

Any在Swift语言中代表任意类型

Swift语言中创建数值变量时可以通过+“0b”前缀的方式讲数值设为二进制

swift使用enum关键词来进行枚举的创建

结构体 枚举以及之前提到的除类之外的所有数据类型都属于值类型。只有类是引用类型。

值类型和引用类型最大的区别是当进行数据传输时，值类型总是被复制。而引用类型不会被复制。

结构体vs类：结构体开发者并不需要提供构造方法，结构体会根据属性自动生成一个构造方法。而类则要求开发者自己提供构造方法。

Swift语言中Array，String，Dict都是通过结构体实现的。

Swift属性分存储属性和计算属性。
存储属性用于描述存储值；计算属性：用于描述计算过程并获取计算结果

Swift属性还支持延迟存储属性。只有当开发者调用到类实例的这个属性才完成构造。
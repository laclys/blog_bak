---
title: JS原型与原型链学习笔记
tags: 
	- 前端
	- javascript
---
这一周在慕课以及网上搜索，好好学习理解了一下JS的原型/原型链。在这里笔记一下自己觉得重要的东西，在这总结一下。
### 普通对象与函数：
JavaScript 中分为普通对象和函数对象。凡是通过 new Function() 创建的对象都是函数对象，其他的都是普通对象。
### 构造函数：
```javascript
function Person(name, age, job) {
 this.name = name;
 this.age = age;
 this.job = job;
 this.sayName = function() { alert(this.name) } 
}
var person1 = new Person('Zaxlct', 28, 'Software Engineer');
var person2 = new Person('Mick', 23, 'Doctor');
```
上面的例子中 person1 和 person2 都是 Person 的实例。这两个实例都有一个 constructor （构造函数）属性，该属性（是一个指针）指向 Person。
**person1 和 person2 都是 构造函数 Person 的实例** 

!!!实例的构造函数属性（constructor）指向构造函数。
<!-- more -->
### 原型对象：
在默认情况下，所有的原型对象都会自动获得一个 constructor（构造函数）属性，这个属性（是一个指针）指向 prototype 属性所在的函数（Person）。
eg：
	Person.prototype.constructor == Person
**可以理解为：原型对象（Person.prototype）是 构造函数（Person）的一个实例** 
### ._proto_:
JS 在创建对象（不论是普通对象还是函数对象）的时候，都有一个叫做__proto__ 的内置
属性，用于指向创建它的构造函数的原型对象。
对象 person1 有一个 __proto__属性，创建它的构造函数是 Person，构造函数的原型对象
是 Person.prototype ，所以：
	person1.__proto__ == Person.prototype

![](/assets/blogImg/20170210-1.jpg) via internet
从而可得到：
	Person.prototype.constructor == Person;
	person1.__proto__ == Person.prototype;
	person1.constructor == Person;
### 构造器：
构造器都是函数对象 “function”。
## 总结：
1,原型和原型链是JS实现继承的一种模型。
2,原型链的形成是真正是靠__proto__ 而非prototype

以上！！！
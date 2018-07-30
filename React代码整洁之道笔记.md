---
title: React代码整洁之道笔记
tags: 
	- ReactNative 
	- React
---


因为RN开始接触React，JSX相比HTML，更加简明。由于All in JS，如果在编写React代码的时候不注重代码的规范和整洁。后期维护起来会相当的痛苦。

正如文章所说:编程也是艺术行为，当我们丝毫代码复用、变量命名时，就是在进行艺术的思考。

![](/assets/blogImg/20171207-01.jpg)

<!-- more -->

### 不冗余
```javascript
// Dirty
const MyComponent = () => (
  <div>
    <OtherComponent type="a" className="colorful" foo={123} bar={456} />
    <OtherComponent type="b" className="colorful" foo={123} bar={456} />
  </div>
);

// Clean
const MyOtherComponent = ({ type }) => (
  <OtherComponent type={type} className="colorful" foo={123} bar={456} />
);
const MyComponent = () => (
  <div>
    <MyOtherComponent type="a" />
    <MyOtherComponent type="b" />
  </div>
);
```
但是要记住不要过度的优化，拆分。过度和破坏没什么区别。

### 可预测、可测试

这方面自己接触的不是很多，抽空有必要好好学习一下前端方面的测试

### 自我解释 斟酌变量名
尽可能减少代码中的注释。可以通过让变量名更语义化、只注释复杂、潜在逻辑，来减少注释量，同时也提高了可维护性，毕竟不用总在代码与注释之间同步了。

- 布尔值或者返回值是布尔类型的函数，命名以 is has should 开头
- 函数以其效果命名，而不是怎么做的来命名
eg：
```javascript
// Dirty
const done = current >= goal;
// Clean
const isComplete = current >= goal;

// Dirty
const loadConfigFromServer = () => {
  ...
};
// Clean
const loadConfig = () => {
  ...
};

```
### 遵循设计模式

这里所说的设计模式并不是工程上的，而是更广泛开发中的设计模式。比如使用standrad进行代码规范···

对于 React，遵循以下几个最佳实践：

- 单一责任原则, 确保每个功能都完整完成一项功能，比如更细粒度的组件拆分，同时也更利于测试。
- 不要把组件的内部依赖强加给使用方。
- lint 规则尽量严格。
- 

### 总结

编写整洁规范的代码，也许在一开始的时候会放慢开发速度，因为你需要转变自己的思维模式，但随着不断迭代，它的带来的效率提升会逐渐弥补前面的损失，并不断带来开发效率的提升。

最开始自己写RN的时候，需求来的急，用脚写固然可以快速完成。之后在维护的时候，就很痛苦不得不重构代码。自己非常喜欢函数式编程。很羡慕函数式工作环境的开发者，他们几乎只要为每个功能写一遍，剩下的就是记住并调用它。


以上~
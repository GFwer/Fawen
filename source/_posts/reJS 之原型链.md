---
title: reJS 之原型链
tags: [Javascript]
category: JavaScript
date: 2020-04-03 23:53
---
# reJS 之原型链

原型链是 JavaScript 中非常重要的内容，每次看原型链相关的知识都能有新的体会，希望写出来这篇文章后，每次看都能温故而知新。

## 原型
首先，在几乎所有 JavaScript 对象被创建的时候它都会被赋予一个非空的值，这个值指向它的原型对象。

根据 ECMA 标准，由`obj[[Prototype]]`来表示指向`obj`的原型，它实际上等同于许多浏览器实现的`__proto__`的值（虽然是非标准的，应该以`Object.getPrototypeOf()`访问），不过为了行文方便，以下就以`__proto__`指代原型。

### `__proto__` 和 prototype 
那么，`__proto__`和`prototype`又有什么区别？

- `__proto__`，JavaScript 中任意对象都拥有这个属性，用于指向创建这个对象的函数的原型，他用来构成原型链。
- `prototype`，每个函数被创建之后都有这个属性指向该方法的原型对象，它保存着实例能够共享的对象和方法，它用来实现原型继承和属性共享，同时内部有一个构造器指回构造函数。

举个栗子：
```javascript
function Foo(){}

var obj = new Foo()

console.log(obj.__proto__ === Foo.prototype); // true

console.log(obj.__proto__.constructor === Foo); // true
console.log(Foo.prototype.constructor === Foo); // true

// Foo 是 Function 的实例
console.log(Foo.__proto__ === Function.prototype); // true
// 原型链
console.log(obj.__proto__.__proto__ === Object.prototype); // true
// 同理
console.log(Function.prototype.__proto__ === Object.prototype); // true
// 到头了
console.log(Object.prototype.__proto__); // null
```

## 原型链
来看下面的例子：
```javascript
function Foo(){}
Foo.prototype.sayHello = ()=> console.log('hello')

var obj = new Foo();
obj.sayHello(); // 'hello'

console.log(obj.a); // undefined
Object.prototype.a = 5
console.log(obj.a); // 5

obj.a++
console.log(obj.a, Object.prototype.a); // 6 5

obj.n = undefined
Object.prototype.n = 5
console.log(obj.n); // undefined
```

在新生成了`obj`实例之后，分 4 部分执行：
1. 要执行对象的`sayHello`方法，这时候发现对象内部没有这个方法，于是就向上去构造函数的原型中找（即`Foo.prototype`），也就是看`obj.__proto__.sayHello`这个值存不存在，一找发现存在，就执行了这个方法。
2. 要找对象的`a`属性，一找，发现对象内部没有，就再上对象的构造函数`obj.__proto`中找，一找发现还是没有，那就再继续向上查找，在`obj.__proto.__proto__`还是没找到，怎么办，再向上找？是`null`了，没办法，找到头了，返回`undefined`。
3. 设置了`Object.prototype.a`的值，这次再执行第二步，在`obj.__proto.__proto__`找到了，赶紧返回。
4. 设置`obj.a++`，这时候其实相当于`obj.a = obj.a + 1`，对`obj.a`进行`RHS`应用，溯源查找发现是 5，于是计算完成给`obj.a`赋值 6。
5. 再继续查找`obj.a`，这次在对象内部就找到了，返回一个`6`。
6. 给`obj.n`和`Object.prototype.n`赋值，这次再找找`obj.n`，在对象内部找到了，虽然是`undefined`还是返回了。

刚刚赋值的操作很有趣，那么再来一个赋值：
```javascript
function Foo(){}
var obj = new Foo();

Object.defineProperty(Object.prototype, 'v', {
  value: 3,
  // 这次不可写了，看你怎么办
  writable: false
})

console.log(obj.v); // 3
obj.v++
console.log(obj.v); // 3
```
如果一直向上找，找到原型链上的某个值，虽然存在但是是不可写状态，那么这种赋值操作将会忽略（严格模式甚至会报错）。

## 其它
实际上`instance of`就是检查是否在原型链上：
```javascript
var a = {}
function b(){}

a.__proto__ = b

console.log(a instanceof b); // true
```
可以通过`Object.create`来创建一个对象：
```javascript
var a = {
    say:()=>console.log('nihao')
}

var b = Object.create(a)
// 完美关联上了
b.say(); // nihao
```
事实上还可以通过它创建一个完全无污染，没有原型链的对象：
```javascript
var obj = Object.create(null);
// 纯天然
console.log(obj.__proto__); // undefined
```

## 结论
实际上原型机制就是存在于对象中的一个内部链接，它会引到另一个对象。它的作用在于，如果对象上没有找到对应的属性或者方法，那么就会在这个链接关联的对象继续查找，如果没有就在继续查找...这一系列链接就被称为原型链。

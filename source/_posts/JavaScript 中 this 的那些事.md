---
title: JavaScript 中 this 的那些事
tags: [JavaScript]
category: JavaScript
date: 2020-3-26 17:39
---
# JavaScript 中 this 的那些事

## 前言
`this`及其周边是 JavaScript 中最经典问题之一了，算是一个大坑，今天详细来聊一聊。
**以下均为在非严格模式下运行的结果**

## 调用那些事
### 函数中使用
```javascript
var color = "blue";

function getColor() {
  var color = "red";
  console.log(this.color);
}
getColor(); // 'blue'
```
这是一个最简单的例子，也表明了 this 指向的核心：**this 指向调用函数的那个对象**。在这个例子之中，实际上是全局对象`window`调用了函数，所以结果自然是`window.color`。


### 对象中使用
```javascript
var name = "wenfa";
var person = {
  name: "fawen",
  getName: function () {
    console.log(this.name);
  },
};
person.getName(); // 'fawen'
```
这里调用函数的对象是`person`，所以输出`person.name`。

再来一个日常经常遇到的问题：
```javascript
var name = "wenfa";
var person = {
  name: "fawen",
  getName: function () {
    console.log(this.name);
  },
};

var print = person.getName;
print(); // 'wenfa'
```
在将函数中`person`对象将`getName`方法赋给了变量`print`后，但这时候`getName`并没有被调用，之后`print`才被全局对象调用，所以输出全局对象底下的 name。

### 在闭包中使用
```javascript
var name = "wenfa";
var person = {
  name: "fawen",
  getName: function () {
    return function(){
        console.log(this.name);
    }
  },
};
person.getName()(); // 'wenfa'
```
结果输出了`wenfa`，实际上在闭包中使用 this 可能会出现一个问题。
> 正常来说，this 的指向是在运行的时候基于函数的执行环境绑定的，当函数作为对象的方法被调用时，this 指向这个对象，不过匿名函数的执行环境具有全局性，所以其 this 对象一般指向 window。

在该例子中，闭包无法访问`getName`函数之外的变量，如果想要按照之前的例子一样，就需要提前把 this 对象保存在能访问到的函数中；
```diff
var name = "wenfa";
var person = {
  name: "fawen",
  getName: function () {
+   var that = this;
    return function(){
+       console.log(that.name);
    }
  },
};
person.getName()(); // 'fawen'
```
先把 this 保存在`that`变量之中，而闭包能够访问这个`that`，所以函数返回了`fawen`。

## 他改变了 this
除了上面所说的创建变量存储 this，之外，其他还有很多方法来改变 this 走向，下面一一来列举一下。

### call、apply、bind 三大将
通过原生提供的`call`、`apply`、`bind`方法我们能够比较方便地改变 this 指向：
- `call`接收 this 和数组/类数组形式参数；
- `apply`接收 this 和列表形式的参数；
- `bind`根据提供的 this 创建一个函数。

用他们尝试解决上面的一些问题：
闭包：
```javascript
var person = {
  name: "fawen",
  getName: function () {
    return (function(){
        console.log(this.name);
    }).bind(this)
  },
};
person.getName()(); // 'fawen'
```

赋值调用：
```javascript
var name = "wenfa";
var person = {
  name: "fawen",
  getName: function () {
    console.log(this.name);
  },
};

var print = person.getName;
print.call(person); // 'fawen'
print.apply(person); // 'fawen'
```
另外，`call`和`apply`还有一些其他，比如说：
```javascript
Object.prototype.toString.call(item);

Math.max.apply(this, [1,2,3,4,5]);

Array.prototype.slice.call(arrayLike);

....
```

### 箭头函数
箭头函数一个比较重要的特点就是没有 this，所以**箭头函数的 this 指向不是根据调用来确定的，是根据定义来确定的**。通过查找作用域，箭头函数指向最近一层非箭头函数的 this，否则 this 为 undefined（非严格模式下为 window 对象）。

又是闭包这个例子，通过箭头函数能够把 this 指向 `getName`：
```javascript
var name = "wenfa";
var person = {
  name: "fawen",
  getName: function () {
    return () => {
        console.log(this.name);
    }
  },
};
person.getName()(); // 'fawen'
```

### new
通过 new 可以调用构造函数创建一个新的对象，构造函数中的 this 指向新的对象。
以下面的代码为例：
```javascript
var name = 'wenfa'
function person(name){
    this.name = name
}

var fawen = new person('fawen');
console.log(fawen.name); // 'fawen'
```

又到了`new`一个对象的过程了：
1. 创建一个空的对象；
2. 将空对象的`__proto__`指向构造函数的`prototype`；
3. **把 this 指向新对象**；
4. 如果返回值是一个对象，那么返回该对象，如果无返回值，这返回这个新对象。


## 参考
- [this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)
- [JavaScript 深入之从 ECMAScript 规范解读 this](https://juejin.im/post/58eee3eda0bb9f006a7eea12)
- [this、apply、call、bind](https://juejin.im/post/59bfe84351882531b730bac2#heading-15)
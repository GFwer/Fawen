---
title: 关于 new 的优先级问题
tags: [Javascript]
category: JavaScript
date: 2020-3-31 22:49
---
# 关于 new 的优先级问题


## 问题
最近经常看到一道很有趣的且经典的题目，是关于涉及非常综合的一道题目，其中运算符优先级我不太明白，觉得值得学习，且在此记录一下。

来看题目：
```javascript
function Foo() {
  getName = function () {
    console.log(1);
  };
  return this;
}
Foo.getName = function () {
  console.log(2);
};
Foo.prototype.getName = function () {
  console.log(3);
};
var getName = function () {
  console.log(4);
};
function getName() {
  console.log(5);
}

Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```

虽然觉得出这种题目没有意义，但是好歹涉及到了多个原理性的问题，所以在此记录一下


## 解题

第一个，`Foo.getName`，没啥好说的，输出`2`；

第二个，`getName`，涉及到变量提升，函数声明的`getName`提前与变量，所以输出`4`。

第三个，`Foo().getName()`，按顺序执行，`Foo`函数内重新定义了全局变量`getName`，接着执行，输出`1`。

第四个，`getName`，同上输出`1`。

第五个，`new Foo.getName()`，先执行`Foo.getName()`，生成新对象，生成过程中输出`2`。

第六个，`new Foo().getName()`，相当于`(new Foo).getName()`,先执行`new Foo()`，返回一个新的空对象，然后执行该对象的`getName`方法，由于没找到属性，就到原型链上找，找到了原型上的属性，输出`3`。

第七个，`new new Foo().getName()`，相当于`new ((new Foo()).getName)()`，前面执行步骤同上，所以还是输出`3`。

关于`new`的优先级，MDN 上描述如下：

| 优先级    | 运算类型          | 示例         |
|--------|---------------|------------|
| 20     | 圆括号           | `()`       |
| 19     | 成员访问          | `a.b`      |
| 19     | 需要计算的成员访问     | `a[b + c]` |
| **19** | **带参数的** new  | new a()    |
| 19     | 函数调用          | a()        |
| **18** | **不带参数的** new | new a      |

从上面可以得知，如果`new`后面第一个参数如果不带括号，它的优先级是低于成员访问的，所以有了上面例子的情况，另外，相同优先级的运算还是从左到右依次计算。

## 其他例题

```javascript
function task() {
    console.log(1)
    return this;
}

task.getName = function() {
    console.log(2)
}

task.prototype.getName = function () {
    console.log(3);
};

var Task = {}
Task.t = task

new Task.t.getName()  
new Task.t().getName()
```

同样的，第一个把表达式化成`new ((Task.t).getName)()`，在里层计算的时候就输出了`2`。

第二个把表达式化成`(new (Task.t)()).getName()`,先输出`1`，然后新建一个空对象，调用其原型链上的函数，输出`3`。

## 后记
最近需要重新开始学习 JS 和 CSS，后续的文章应该会命名为 reJS 和 reCSS 吧，打好基础，理解原理，才能融会贯通。


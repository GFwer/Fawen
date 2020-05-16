---
title: JavaScript 之 Iterator 接口
tags: [JavaScript]
category: JavaScript
date: 2020-3-27 19:50
---
# JavaScript 之 Iterator 接口

## 前言
最近这两天稍显浮躁，没写出啥能看的内容，希望接下来几天能好好调整吧，摆脱现在的学习困境，从现在开始。

## 迭代协议

ES6 提供了一种新的迭代思路，它补充了两个协议：可迭代协议和迭代器协议，任何数据结构只要遵守了这两个协议，它们都是可以被迭代的。
 
### 可迭代协议

> 可迭代协议允许 JavaScript 对象去定义或定制它们的迭代行为

可迭代协议就是明确应该迭代谁，比如让`for...of`知道什么值可以被循环：
```javascript
const arr = [0, 1, 2];
for(let item in arr){
    console.log(item)
}
```

为了支持可迭代协议，需要迭代的对象或者他的原型链上的某个对象必须部署了`Symbol.iterator`属性，这个属性的 value 值是一个无参函数，返回接下来需要实现迭代器协议的对象。

### 迭代器协议
> 该迭代器协议定义了一种标准的方式来产生一个有限或无限序列的值，并且当所有的值都已经被迭代后，就会有一个默认的返回值。

迭代器协议就是定义了如何迭代一个对象，他需要拥有一个`next`方法，并且这个方法至少需要返回拥有两个对象的属性：
- `done`，表示迭代是否完成
- `value`，返回每次迭代的值

下面是一个简单的例子：
```javascript
const obj = {
  [Symbol.iterator] : function () {
    return {
      _first: true,
      next: function () {
        if(this._first < 3){
            
            return { value: this._first++, done: false };
        }else{
            return {done: true}
        }
      }
    };
  }
};

let iterator = obj[Symbol.iterator]();

iterator.next(); // {value: 1, done: true}
iterator.next(); // {value: 2, done: false}
iterator.next(); // {done: true}

console.log([...obj]); // [1, 2]
for(let i of obj) { console.log(i); } // 1,2
```

通过返回遵循这两个协议的对象，使得`obj`获得了被迭代的能力（奇怪的能力增加了！）。


## Iterator
Iterator（遍历器）就是这样一种机制，只要部署了`Iterator`接口，任何对象都能够被遍历，一但一个数据结构内实现了 Iterator 接口，那么迭代的时候返回的数据只和 Iterator 之中有关。

它的遍历过程如下：
1. 创建一个指针对象，指向起始位置
2. 不断调用对象的`next()`方法，返回`value`值，直到`done === true`

以下场景都会调用`iterator`接口：
```javascript
const obj = {
  "a": "b",
  "c": "d",
  [Symbol.iterator] : function () {
    return {
      _first: true,
      next: function () {
        if(this._first < 3){
            
            return { value: this._first++, done: false };
        }else{
            return {done: true}
        }
      }
    };
  }
};

// 扩展
[...obj] // [1,2]
// Array.from 等可以使用类数组对象的函数
console.log(Array.from(obj)); // [1,2]
console.log(new Set(obj)); // Set(2) {1, 2}
```


### return 和 throw
Iterator 对象内除了 next 方法，还可以有 return 和 throw 方法。

return 方法主要用途是如果遍历提前退出（出现错误或者 break），就会调用 return 方法，这个方法必须返回一个对象，如果需要清理或者释放资源，那么可以调用 return。

```javascript
// 两种情况都会调用 return 函数
for (let i of obj) {
  break;
}

for (let i of obj) {
  throw new Error();
}
```

或者也可以直接调用：
```javascriptf
const iter = obj[Symbol.iterator]()

iter.return();
iter.throw();
```

### Generator
Generator 函数就是 Iterator 的生成函数，所以直接把 Generator 函数赋给`Symbol.iterator`也可以使对象变得可以迭代：
```javascript
const obj = {}
obj[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
}
console.log([...obj]); // [1, 2]
```

一般来说不需要自己部署 Iterator 接口，因为一些内置的类型已经默认部署了`Iterator`：
- Array
- String
- Map
- Set
- TypedArray
- arguments


## 后记
`for`循环太麻烦，`forEach`无法退出、`map`返回数组且同样无法退出，简单情况下的遍历用什么？`for...of`用起来！

## 参考
- [迭代协议](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)
- [Iterator 和 for...of 循环](https://es6.ruanyifeng.com/#docs/iterator)
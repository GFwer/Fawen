---
title: 细说 JavaScript 运行机制 —— Event Loop
tags: [Javascript]
category: JavaScript
date: 2020-3-18 21:50
---
# 细说 JavaScript 运行机制 —— Event Loop

## 前言
关于 Event Loop，之前理解不是很透彻，只知道简单的空栈拉队列云云，其他微任务、宏任务什么的还不是很了解，今天就来细细品一品 Event Loop。

## 开始
首先需要清楚堆、栈、队列的含义和它们的作用。
### 堆
堆（heap）指程序运行时申请的动态内存，JavaScript 运行时用来存放对象。
### 栈
一个先入后出数据结构，这里的栈指的是执行 JavaScript 主线程的调用栈，其中存放了所有函数的调用信息，调用某个函数函数的时候会将其参数和局部变量等入栈，执行完成后执行出栈。
### 队列
一个先入先出数据结构，JavaScript 中存在任务队列，用来存放等待执行的函数。

## 事件循环
众所周知，JavaScript 是单线程，那么所有的任务都是遵循的一个顺序依次执行的。完整的事件循环机制如下图：
![事件循环](https://static.gongfangwen.com/2020-03-18-15845344823350.jpg)
可以对事件循环做如下描述：
 - JavaScript 主线程在执行栈清空之后，会读取任务队列，将其中的函数入栈并依次运行，直到执行栈清空，然后再去读取任务队列，如此不断循环。
 - 异步操作（如 HTTP 请求、setTimeout 等）由其他线程执行，执行完成之后将回调处理插入任务队列中。
 - 主线程阻塞的时候，任务队列依然能够被推入任务。

## 宏任务（Macro Task）与 微任务（MicroTask）
那实际上执行的时候，因为有异步操作，代码未必是按照写的顺序执行的，如：
```javascript
setTimeout(()=>console.log(4))

new Promise(resolve=>{
    resolve(3)
    console.log(1)
}).then(data=>{
    console.log(data)
})

console.log(2)
```
这是一个常见的例子，实际上输出是按照 1、2、3、4 顺序输出的，这里就有了微任务和宏任务的概念了，他们都有一个队列，称为微任务队列和宏任务队列。
宏任务，我们平常写的代码和 `setTimeout`、`setInterval`都属于宏任务，这些顺序按顺序执行。
微任务，指的是`Promise`、`process.nextTick`(node)、`MutationObserver`，一般来说，只要执行栈中的代码执行完成之后，微任务会立即执行，并且在这个微任务执行中，有新的微任务加入了队列中，这个微任务之后也会被立即执行。
知道这些之后，可以尝试解释上面的代码：
1. 代码入栈。
2. 执行`setTimeout`，将其回调函数放到宏任务队列上。
3. 执行 `Promise`，立即执行其中代码，输出 1 ，碰到一个`promise.then`，把它放进微任务队列。
4. 碰到一个`console`，执行输出 2
5. 执行栈空了，查看微任务队列，发现微任务队列有东西，那么将微任务优先入栈，执行输出 3。
6. 执行栈空了，查看发现微任务，发现微任务队列空，那么查看宏任务队列，入栈执行，输出 4。

### async
对于`async`来说，可以将其看做`Promise`封装的对象，其中`await`之前的代码立即执行，`await`及其之后相当于放在`then`之中。


## 参考
[JavaScript Event Loop 机制详解与 Vue.js 中实践应用](https://zhuanlan.zhihu.com/p/29116364)
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop)
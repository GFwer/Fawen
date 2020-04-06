---
title: reJS 之 async
tags: [Javascript]
category: JavaScript
date: 2020-04-03 00:08
---
# reJS 之 async/await
Promise 解决了回调问题，可以使用链式调用，但是非常多的`then`依然中依然要处理各种回调函数，以及处理多个 Promise 非常麻烦，async 作为异步问题的另一个解决方案在 ES7 被提出来了。

## 定义
async 在定义之后会返回一个 Promise：
```javascript
function fun *(){
    return 1
}
fun(); // Promise {<resolved>: 1}
```

事实上在使用`async`的函数都类似于在 Promise 中返回的行为，无论有没有返回，都会返回一个 Promise，状态类型由内部决定：
```javascript
async function fun() {}

fun(); // Promise {<resolved>: undefined}

async function fun() {
  return Promise.resolve(123);
}

fun(); // Promise {<resolved>: 123}

async function fun() {
  return Promise.reject(321);
}

fun(); // Promise {<rejected>: 321}

async function fun() {
  return new Error("321");
}

fun(); // Promise {<resolved>: Error: 321
```
可以看出行为和在 Promise 中完全一样。

## await
在 async 函数中可以使用 await 取到 Promise 对象返回的结果。

```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => resolve(123), 3000);
});

async function format() {
  const data = await p;
  console.log(data);
}

format(); // 123
```
在执行 await 表达式的时候，async 函数会暂停执行，等待 Promise 返回一个值，如果正常返回，皆大欢喜，如果 reject，那么 await 会把内容抛出；

```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => reject(123), 3000);
});

async function format() {
  const data = await p;
  // 不往下执行了
  console.log('I am here');
}

format(); // Uncaught (in promise) 123
```

如果 await 后面不是 Promise ，是一个其他值呢？

```javascript
// 立即执行函数
async function format() {
  const data = await (function(){
    return 123
  })();
  console.log(data);
}

format(); // 123

// 数字
async function format() {
  const data = await 123;
  console.log(data);
}

format(); // 123

// 函数
async function format() {
  const data = await function(){ return 4 };
  console.log(data);
}

format(); // ƒ (){return 4}
```
可以看出，如果 await 后面是一个非 Promise 对象，那么会直接返回该对象。

## 同步？
看起来 await 好像是同步执行的，真的是这样吗？来做一个实验：
```javascript
const p = new Promise((resolve, reject) => {
  setTimeout(() => resolve(123), 5000);
});

async function format() {
  const data = await p;
  console.log('I am here');
  clearInterval(a);
}

var a = setInterval(()=>{console.log(1)},1000)
format();

// 1 1 1 1
// I am here
```
在等待的时候，计数器还是在疯狂输出，证明 await 实际上不是同步的。

事实上，上面例子中的 await 相当于：
```javascript
async function format() {
  p.then(res=>{
    const data = res;
    console.log('I am here');
    clearInterval(a);
  });
}
```
await 后面的内容相当于被放进了 then 中。

## 原理
asnyc 是不是很像下面这种形式？
```
function *fun(){
    const a = yield 1
    const b = yield 2
}
```
async 的原理就是，将 Generator 函数和自动执行器包装在函数中。

Generator 实现暂停，自动执行器实现下一步。

所谓自动执行器，就是执行 Generator 的 next 方法，直到`done === true`，大致如下：
```javascript
function run(fun){
    var g = fun()
    
    function next(data){
        var result = g.next(data)
        if(result.done) return result.value
        result.value.then(function(data){
            next(data)
        })
    }
    next()
}
```

加上 await 的 Promise 处理，大概就成了这样：
> ```javascript
> function fn(args) {
  return spawn(function* () {
    // ...
  });
}

> function spawn(genF) {
  return new Promise(function(resolve, reject) {
    const gen = genF();
    function step(nextF) {
      let next;
      try {
        next = nextF();
      } catch(e) {
        return reject(e);
      }
      if(next.done) {
        return resolve(next.value);
      }
      Promise.resolve(next.value).then(function(v) {
        step(function() { return gen.next(v); });
      }, function(e) {
        step(function() { return gen.throw(e); });
      });
    }
    step(function() { return gen.next(undefined); });
  });
}
> ```

## 参考
- [async 函数](https://es6.ruanyifeng.com/#docs/async)
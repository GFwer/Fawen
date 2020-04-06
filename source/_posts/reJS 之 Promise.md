---
title: reJS 之 Promise
tags: [Javascript]
category: JavaScript
date: 2020-04-02 21:48
---
# reJS 之 Promise

## 回调地狱
在 JavaScript 中，回调是处理异步逻辑非常常用的一部分，之前我们经常这么处理代码：
```javascript
function add(a, b, callback){
    setTimeout(()=>callback(a + b),1000)
}
```
这么处理看上去逻辑没问题，但是如果`a`和`b`都需要从不同的地方获取，代码可能就会变成这样：
```javascript
getA(function (A) {
  getB(function (B) {
    add(A, B, (sum) => console.log(sum));
  });
});
```
需要的数据越多，嵌套的层数也就越多，这种情况被称作”回调地域“，上面展示的还都是数据获取成功的情况，要是再加上失败的情况...结果不忍直视，可以肯定的是，回调地狱大大减低了代码的可读性和可维护性。

## 未来值
那么能否有一个代表未来的值，让我们可以这么写代码：
```javascript
add(getA(), getB()).then(
  function (sum) {
    console.log(sum);
  },
  function (err) {
    console.log(err);
  }
);
```

Promise 就此诞生，它提供了一套完善的 API 来解决各种复杂的异步情况。

## 定义
Promise 代表“承诺”，它保存这一个目前不可用，但是未来可用的操作或值，它具有两个主要特点：
- Promise 对象代表着异步操作，它有三种状态：`pending`、`fulfilled`和`rejected`，只有异步操作的结果才能改变这个状态，外界无法改变。
- 一旦 Promise 决议完成，那么它就永远保持在这个状态了，要不是完成（`fulfilled`），要不就是失败（`rejected`），状态确定后，它就成为了不可变值。

### resolve 和 reject
一般来说在执行了`resolve`之后，对象状态变成完成状态，
对于`resolve`操作来说，如果向它传递一个非 Promise 或者 非 thenable 类型，它也会自动把这个值填充进一个 Promise：
```javascript
var p1 = new Promise(function(resolve, reject){
    resolve(13)
})

var p2 = Promise.resolve(13)
// 两种写法实际上是等价的
```

如果像其传递一个 Promise 对象，那么只会返回同一个 Promise：
```javascript
var p1 = Promise.resolve(13);

var p2 = Promise.resolve(p1);

console.log(p1 === p2); // true
```

所以实际上`resolve`也能返回一个`reject`状态：

```javascript
var p1 = Promise.reject('noooo~')
// nooooo~
var p2 = Promise.resolve(p1)
```

上面例子也表明，给`reject`传入一个非 Promise 对象/thenable 对象，它会直接设置成拒绝理由，如果是个 thenable 或者 Promise 对象：
```javascript
var p1 = Promise.resolve('123');
var p2 = Promise.reject(p1);
// Uncaught (in promise) Promise {<resolved>: "123"}
```
它同样会直接返回这个 Promise 对象。

### catch
对于每一个 Promise 来说，第二个回调函数代表 promise 中的错误，如果在回调函数中的错误将不能被捕获：
```javascript
Promise.resolve(123)
    .then(data=>{
        console.log(data)
        console.log(a)
    },err=>{
        console.log('error is ' + err)
    })
// Promise {<rejected>: ReferenceError: a is not defined
```

catch 代表捕获整个运行过程中发生的错误之后的回调函数，同时 catch 返回的还是一个新的 Promise 对象：
```javascript
Promise.resolve('123').then(data=>{
    console.log(a)
    // 以下将不会执行
    console.log(123)
}).catch(err=>{
    console.log(err)
}).then(res=>{
    console.log('continue')
})
// ReferenceError: a is not defined
// continue
// 如果没有 catch 那么错误将会直接被抛出
```


### then
then 的调用可能有很多种情况，分成返回值，没返回值，但他都返回了一个新的 Promise 对象。

如果返回了值，那么 then 返回的 Promise 将会成为接受状态，并且将返回值作为下一个`then`的参数：
```javascript
Promise.resolve("123")
  .then((res) => "321")
  .then((res) => console.log(res));
// 321
```

如果没有返回值，那么同样变成接受状态，同时下一个 then 的参数变成了 undefined（因为没有返回）：
```javascript
Promise.resolve('123')
    .then(res=>{Promise.resolve('321')})
    .then(res=>console.log(res));
// undefined
```

如果抛出一个错误，那么 then 返回将变成拒绝状态，同时把错误作为下一个 then/catch 的参数：
```javascript
Promise.resolve("123")
  .then((res) => {
    throw new Error("no~~");
  })
  .then(
    (res) => console.log("success: " + res),
    (err) => console.log("error:" + err)
  );
// error:Error: no~~

Promise.resolve("123")
  .then((res) => {
    throw new Error("no~~");
  })
  .then(
    (res) => console.log("success: " + res),
    (err) => console.log("error:" + err)
  )
  .catch((err) => console.log("catch:" + err));
// 先被回调捕获了
// error:Error: no~~

Promise.resolve("123")
  .then((res) => {
    throw new Error("no~~");
  })
  .catch((err) => console.log("catch:" + err));
// catch:Error: no~~
```

如果返回一个接受状态的 Promise，那么 then 也变成接受状态，同时把返回的这个 Promise 的接受状态的参数值变成下一个 then 的参数值（可以理解成返回的 Promise 代替了原来的）：
```javascript
Promise.resolve("123")
  .then((res) => Promise.resolve("321"))
  .then((res) => console.log(res));
// '321'
```

如果返回了一个拒绝状态的 Promise，那么 then 也变成拒绝状态，同时把返回的这个 Promise 的拒绝状态的参数值变成下一个 then 的参数值（同上）：
```javascript
Promise.resolve("123")
  .then((res) => Promise.reject("321"))
  .then((res) => console.log(res),err=>console.log('err:' + err));
// err:321
```

如果返回一个还在 pending 的 Promise，那么等待这个 Promise 完成在进行下一步操作：
```javascript
var p1 = new Promise((resolve, reject) => {
  setTimeout(() => console.log("321"), 5000);
});

var p2 = Promise.resolve(p1);

p2.then((res) => console.log(res));

// Promise {<pending>}
// 等待中...5 秒之后..
// 321
```
### race 和 all
其他主要的 Promise API 还有`Promise.race([])`和`Promise.all([])`，它们都可以接收一个 Promise 对象（非 Promise 也会自动转成 Promise）。

其中 race 是一种竞态 Promise ，它表示先结束谁继续，无论是否成功，利用这个特性可以限定一些函数在固定时间内完成。

all 是一种等待 Promise ，它需要等待其中所有 Promise 全部完成才能执行回调，但是如果有一个被拒绝，那么直接返回拒绝。

## 参考
- 你不知道的 JS
- [Promise 对象](https://es6.ruanyifeng.com/#docs/promise)
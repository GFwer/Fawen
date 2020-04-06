---
title: reJS 之 Generator
tags: [Javascript]
category: JavaScript
date: 2020-04-03 16:34
---
# reJS 之 Generator
Generator 同样是一种异步解决方案，不过它的语法和之前遇到的不一样，它采用了`*`和`yield`来实现异步。

## 基本语法
`*`加在前，`yield`跟在后：
```javascript
function *foo(){
    const data = yield 1 + 1
    console.log(data);
}

const iter = foo();
iter.next(); // {value: 2, done: false}
iter.next(); // {value: undefined, done: true}
```

执行 Generator 函数会自动返回一个迭代器，每当执行`next`方法的时候，迭代器会执行到下一个`yield`，直到没有`yield`或者`return`。

## next 的参数
来看看下面的例子：
```javascript
function *foo(){
    const data = yield 1 + 1
    const data2 = yield data + 2
}

const iter = foo();
iter.next(); // {value: 2, done: false}
iter.next(3); // {value: 5, done: true}
iter.next(2); // {value: undefined, done: true}
```
从上面可以看出，next 的传入的参数会被上一次 yield 的结果。

## 迭代器
既然 Generator 函数返回了一个迭代器，那么肯定可以用`for...of`进行迭代：
```javascript
function *foo(){
    yield 1
    yield 2
    yield 3
}

for(let item of foo()){
  console.log(item)
}

// 1 2 3
```

如果在 yield 一个 Generator 函数呢？
```javascript
function *bar(){
    yield 9
    yield 8
    yield 7
}
function *foo(){
    yield 1
    yield *bar() // 如果后面是遍历器，这里需要加 *，否则只会返回一个遍历器对象
    yield 2
}

for(let i of bar()){
    console.log(i)
}
// 1 9 8 7 2
```
可以看出来，`for...of`在`bar`中遇到了一个 Generator，它会先将新的执行完，再去执行原来的，这符合同步的逻辑。

## 异步？
之前的例子都是同步的，那如果是异步的请求遇到`yield`会怎么处理？
```javascript
function *foo(){
    const data = yield fetch('https://www.baidu.com')
    console.log(data)
}

const getData = foo();
const data = getData.next();
data.value.then(console.log); // Response {type: "basic", url: "https://www.baidu.com/"}
```
例子中的`yield`就代表着异步请求，当执行到`yield`的时候，函数还是会暂停执行，next 方法返回 fetch 的 Promise，再执行 Promise 的 then 方法，不过这样异步请求看起来流程管理还是略复杂了。

## return
遍历器对象提供了一个 return 方法，可以终结遍历：
```javascript
function *foo(){
    yield 1
    yield 2
}

const g = foo();
g.next();    // { value: 1, done: false }
g.return(9); // { value: 9, done: true }
g.next();    // { value: undefined, done: true }
```

## 错误处理
出错处理很容易就想到了`try...catch`，先试试把它用在 Generator 函数上：
```javascript
function *foo(){
    try{
        yield console.log(a);
        yield console.log(1);
        yield console.log(2);
    }catch(err){
        console.info(err)
    }
}

const iter = foo();
iter.next(); // ReferenceError: a is not defined
// {value: undefined, done: true}
```
在遇到错误后，遍历器直接会指向完成状态。
如果内部没有`try..catch`方法，那么在外部也可以捕获：
```javascript
function *foo(){
    yield console.log(a);
    yield console.log(1);
    yield console.log(2);
}
try{
    const iter = foo();
    iter.next();
}catch(e){
    console.log(e);
}
// ReferenceError: a is not defined
```
Generator 提供了一个 throw ，用于向函数内部抛出错误，如果内部没有处理这个错误，那么这个错误会抛向函数外：
```javascript
function *foo(){
    try{
        // 在第一个 yield 位置抛出，所以还是由这个语句处理
        yield console.log(1);
    }catch(e){
        console.log('position 1')
    }
    // 执行完成后跳出 try...catch 继续执行
    yield console.log(2);
}

const g = foo()

g.next(); // 1
g.throw(123); // position 1
// 2 throw 后自动执行了一次 next 方法
```

## 构造函数
Generator 能当做构造函数吗？来试一下：
```javascript
function *foo(){
    yield 1
    yield 2
}

const g = new foo(); // Uncaught TypeError: foo is not a constructor
```
结果是不行，实际上 Generator 返回的遍历器对象就是它的实例了：
```javascript
function *foo(){
    yield 1
    yield 2
}
foo.prototype.name = 'fawen'

const g = foo();

console.log(g instanceof foo); // true
console.log(g.name); // 'fawen'
```

## 自动执行
既然每次都要手动调用 next 方法，能不能写个函数自动调用呢？可以简单实现一个来看看：
```javascript
function run(g){
    var gen = g();
    
    function next(data){
        var result = gen.next(data)
        if(result.done) return
        next(result.value)
    }
    
    next()
}

function *foo(){
    yield console.log(1)
    yield console.log(2)
    yield console.log(3)
}

run(foo)
// 1
// 2
// 3
```


## 参考
- [Generator 函数](https://es6.ruanyifeng.com/#docs/generator)
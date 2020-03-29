---
title: 容易忘的 Tips 
tags: [JavaScript]
category: JavaScript
date: 2020-2-20 23:05
---

# Tip 
## 作用域
动态作用域：执行的函数内如果找不到变量，那么就在函数调用那层寻找；
静态作用域：执行的函数内如果找不到变量，那么就在函数定义那层寻找，一直往上找直到 window；
JavaScript用的是静态（词法）作用域


## 变量提升
进入执行上下文的时候，首先处理函数声明、其次处理变量声明、如果变量声明和已经声明的形参或函数相同，则变量声明不会干扰
```javascript
console.log(foo);

function foo(){
   console.log("foo")
}

var foo = 1;
// 结果是打印函数
```

**闭包**指的是能够访问自由变量的函数。
**自由变量**是指在函数中使用的，但既不是函数参数也不是函数的局部变量的变量。


## 参数传递
大部分都是拷贝传递，即函数内部修改参数值不影响外部
如果是应用类型（对象等）则是共享传递，传递对象引用的副本，只能修改其属性值，不能修改其本身。
```javascript
var o = {a:1}
function a(o){
    o.a = 2
}
a(o);
console.log(o) // 输出 {a:2}
```
```javascript
var o = {a:1}
function a(o){
    o = 2
}
a(o);
console.log(o) // 输出 {a:1}
```

## this
`this`关键字总是指向函数所在的当前对象，而 ES6 的 `super`关键字指向当前对象的原型对象。
```javascript
const proto = {
  foo: 'hello'
};

const obj = {
  foo: 'world',
  find() {
    return super.foo;
  }
};
oobj.find() // undefined
Object.setPrototypeOf(obj, proto);
obj.find() // "hello"
```

## ?? 运算符
`null`和`undefined`判断
```javascript
const a = b || '1'
```
原意是`b`为`undefined`或者`null`的时候给予默认值，但是现在空字符串、0、false都能变成默认值，
ES2020 的 `??`运算符可以解决此问题：
```javascript
const a = b ?? '1'
```

## defer 和 async
`script`标签中的`defer`和`async`都能异步加载脚本，区别在于`defer`是下载完成之后在`DOMContentLoaded`之前按照书写顺序执行，`async`是下载完成后立即执行，不保证顺序，`async`比较适合无依赖的脚本。

## integrity
`integrity`可以对`script`和`link`标签生效，它的 value 值是一个 hash，通过比对文件 hash 和 integrity 值，可以防止 CDN 收到攻击篡改文件，浏览器在遇到两者不一致的时候拒绝执行该脚本/样式并返回一个错误。
```html
<script src="https://js.cdn.framework.js"
        integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"></script>
```

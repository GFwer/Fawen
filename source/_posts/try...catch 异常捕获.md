---
title: try...catch 异常捕获
tags: [JavaScript]
category: JavaScript
date: 2020-4-30 22:38
---
# try...catch 异常捕获

## 前言
try...catch 是我们经常用到的捕获异常的语句，不过简单的语句也有许多需要注意的地方。

## 用法

### 基本用法
基本用法就是把执行语句放在里面执行，抛出的任何异常都会到 catch 语句中。
```javascript
try {
  throw new Error("fawen");
} catch (e) {
  console.log(e);
}
```

### 嵌套
如果嵌套的话，异常只会被最近的一个 catch 语句捕获到：
```javascript
try {
  try {
    throw new Error("fawen");
  } catch (e) {
    console.log(e);
  }
} catch (e) {
  console.log("outer:" + e);
}
// Error: fawen
```

## 捕获规则
`try...catch`能捕获所有的异常吗？下面就来试验一下：

### 定义函数在其中
定义在块内部的错误可以被捕获：
```javascript
try {
  function error() {
    nihao;
  }
  error();
} catch (e) {
  console.log(e); // nihao is not defined
}
```

### 定义函数在外部
定义函数外部的错误同样可以被捕获：
```javascript
function error() {
  nihao;
}
try {
  error();
} catch (e) {
  console.log(e); // nihao is not defined
}
```

### 异步
异步的任务不能被捕获：
```javascript
function error() {
  setTimeout(() => {
    nihao;
  });
}
try {
  error();
} catch (e) {
  console.log(e);
}
// 不能捕获
// nihao is not defined
```
promise 同理：
```javascript
function error() {
  Promise.resolve(1).then(() => {
    nihao;
  });
}
try {
  error();
} catch (e) {
  console.log(e);
}
// 不能捕获
// nihao is not defined
```

### 语法错误
在语法分析时发现的语法错误的异常同样也不能被捕获：
```javascript
function error() {
  setTimeout(() => {
    nihao.
  });
}
try {
  error();
} catch (e) {
  console.log(e);
}
// 不能捕获
// nihao is not defined
```

总的来说，必须真正在`try...catch`内部执行的程序才能被捕获。
---
title: 聊聊 IntersectionObserver
tags: [Javascript]
category: Javascript
date: 2020-3-30 23:37
---
# 聊聊 IntersectionObserver

## 前言
还在用`Element.getBoundingClientRect()`来做懒加载吗？快来试试`IntersectionObserver`吧！

> IntersectionObserver V2 新增了一些 options 参数等，由于兼容性不高，故本文暂不做说明

## 作用
IntersectionObserver 提供了一种异步观察目标元素和祖先元素或顶级文档 viewport 的交集变化的方法。

以下列举了一种它的实际用途：
- 懒加载图片；
- 无限滚动类，滚动到一定距离自动加载；
- 检测用户是否滚动到某个区域，执行相应动画；
- 检测广告是否被用户看到，计算收益；

## 过去
过去获取一个元素相对 viewport 是否可见怎么做？用`element.getBoundingClientRect()`，它返回如下结果：
```javascript
var DOMRect = {
    // 元素底部距离页面视口最上的距离
    bottom: -66.5,
    // 元素高度
    height: 51,
    // 元素最左侧相对于视口最左的距离
    left: 164,
    // 元素最右侧现对于视口最左的距离
    right: 1516,
    // 元素顶部相对于视口最上方的距离
    top: -117.5,
    // 元素宽度
    width: 1352,
    // 兼容性原因，相当于 left
    x: 164,
    // 兼容性原因，相当于 top
    y: -117.5,
}
```
如果是竖直滚动，那么判断一个元素是否在视口就很简单了：

```javascript
const inViewPort = top <= window.innerHeight

```

## Intersection observer

先来看看 Intersection observer 相关 API 吧。

### 创建监听


以下语句创建一个 IntersectionObserver ：
```javascript
var options = {
    root: document.querySelector('#scrollArea'), 
    rootMargin: '0px', 
    threshold: 1.0
}

var observer = new IntersectionObserver(callback, options);
```

options 是相关配置，它有以下可选项：
 - `root`，指定根元素，必须是监听元素的父级元素，如果不设置或值为 null 则默认为`window`；
 - `rootMargin`，根元素的外边距值，格式和 CSS 中 margin 值相同（如`10px 20px`或`10px 15px 11px 13px`），默认值为`0`；
 - `threshold`，设定一个相交程度，当达到该程度的时候出发回调函数，该值可以是`number | number[]`；

`threshold`示例如下：
```javascript
// threshold 表示交界状态触发回调
// 如值为 1 时，有三种情况会触发回调
// 1. 最初设定
// 2. 完全显示
// 3. 从完全显示到被遮盖 1 像素
const threshold = 1.0; // 完全可见
const threshold = 0.5; // 一半可见时
const threshold = [0, 0.25, 0.5, 0.75, 1]; // 每出现 25% 触发一次
const threshold = 0; // 有一个像素出现就触发
```

### 开始监听
配置一个目标以开始监听：
```javascript
const target = document.querySelector('#fawen');
observer.observe(target);
```
这时候，当`threshold`满足的时候就会触发回调。

### 回调
下面是一个回调的标准格式：
```javascript
var callback = function(entries, observer) { 
  entries.forEach(entry => {
    // do something
  });
};
```

`entries`格式如下：
```javascript
var IntersectionObserverEntry = {
    // 目标元素的 boundingClientRect() 值
    boundingClientRect: {},
    // intersectionRect 和 boundingClientRect 的比值
    intersectionRatio: 0,
    // 一个描述相交区域的 DOMRectReadOnly 对象
    intersectionRect: {},
    // true 表示从非交叉状态到交叉状态，反之为 false
    isIntersecting: true,
    // Intersection Observer V2新增，监听元素是否可见，有无被遮挡，不过经 Chrome 83 测试发现一直为 false，待确定
    isVisible: true,
    // 一个描述根元素的 DOMRectReadOnly 对象
    rootBounds: null,
    // 目标元素的 target element
    target: target,
    // 从监听开始到触发的时间，毫秒
    time: 12345
}
```
可以得知，要监听一个元素是否在视口可见，只要查看`intersectionRatio`属性就行了，`intersectionRatio === 1`时元素完全可见，`intersectionRatio === 0.5`时正好一半可见。

### 其他 API
和`MutationObserver`类似，`IntersectionObserve`一样给实例提供了`disconnect`、`takeRecords`等方法，还额外增加了`unobserve(target)`方法，用于取消观察目标。


### 注意事项
- `IntersectionObserve`监听是异步的，意味着回调函数不会马上执行。
- 回调函数将在主线程执行，MDN 建议，如果回调非常耗时间，应放在`requestIdleCallback`（即在重排、重绘、渲染后的帧结束的空闲时间）中处理。

### 兼容性
![兼容性](https://static.gongfangwen.com/2020-03-30-15855825542967.jpg)


## 参考
- [Intersection Observer API](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)
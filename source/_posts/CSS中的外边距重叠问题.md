---
title: CSS中的外边距重叠问题
tags: [CSS]
category: CSS
date: 2020-3-18 17:07
---
# CSS中的外边距重叠问题

## 前言
没错又是面试遇到的，我对于一个`margin`合并问题没有很好地解释清楚，回答的解决方法也只有改用`padding`，双方都不太满意这个回答，所以今天正经地来看看这个问题。

## BFC
BFC，即 Block Formatting Contexts，块级格式化上下文。

## 问题
以下三种情况会形成外边距重叠：
1. 同一层相邻元素之间。
```css
.a,
.b {
  background-color: red;
  width: 100px;
  height: 100px;
}
.a {
  margin-bottom: 10px;
}
.b {
  margin-top: 20px;
}
// 从代码上来看 a 和 b 应该间隔 30 px，但是实际上会发生重叠，挑选最大的一个边距留下，所以这个范围应该是 20px
```

2. 没有内容将父元素和后代元素分开。
例1：
```css
.parent {
  background-color: red;
  width: 100px;
  height: 100px;
}
.child {
  margin-top: 20px;
  background-color: blue;
  height: 50px;
  width: 50px;
}
```
![图1](https://static.gongfangwen.com/2020-03-18-15845157585345.jpg)
例2：
```css
.parent {
  background-color: red;
  // height: 30px;
  width: 500px;
  margin: 10px 0px;
}
.child {
  background-color: blue;
  margin-bottom: 50px;
}
```
![图2](https://static.gongfangwen.com/2020-03-18-15845192292750.jpg)

从实际上可以看到，子元素的`margin-top`似乎跑到了父元素上，其实也是发生了外边距重叠，它可能有这两种原因导致：
- 无`border`，无`padding`，无行内元素，没有创建 BFC 或者清除浮动，这时候父子元素的`margin-top`是重叠在一起的，如例 1 所示。
- 同理对于没有设定高度相关的父元素来说，他的`margin-bottom`和最后一个子元素的`margin-bottom`也是重叠在一起的，如例 2 所示。

3. 空的块级元素。
当一个空的块级元素没有设定边框，高度，清除浮动，的时候，它的`margin-top`其实适合`margin-bottom`重合在一起的：
```css
.a,
.c {
  margin: 0;
  background-color: red;
}
.b {
  margin-top: 15px;
  margin-bottom: 30px;
}
```
![结果 a 与 c 的间距是 30px](https://static.gongfangwen.com/2020-03-18-15845196815725.jpg)

如果这其中参与折叠的值包含负值呢？我试验了一下得出以下结论：
 - 在上例中，如果外边距一正一负，那么得到的结果是两数相加，如一个为 -15px，一个为 40px ，结果 a 和 b 相距 -25px。
 - 在上例中，如果外边距都是负数，那么得到的结果是两数之中取较小的数，如一个为 -15px，一个为 -10px，结果 a 和 b 的实际距离是 -15px。

这些还属于比较简单的计算，如果 a 和 b 都加上外边距再参与计算就比较复杂了，可以先将 b 忽略计算 a 和 c 在没有 b 时的距离，再与 b 的计算结果作为比较及：
- 如果都是正值，那么取所有数最大的一个作为 a 和 b 的距离；
- 如果都是负值，取较小的一个作为真正的边距；
- 如果一正一负那么取和；

## 避免重叠
上述逻辑非常不符合正常的编程思维，实际上我们应该尽量避免发生这种情况。以下列举了几种避免重叠的方法：
这种现象实际上是由于元素`margin`相互重叠引起的，我们可以避免元素间（或元素自身）的`margin`相互重叠；
- 如果是父子元素，我们可以增加`padding`、`border`，使得两个元素的外边距相互隔开，就没有这个问题了；
- 使用`padding`代替`margin`；
- 创建 BFC，这种现象实际上只发生在同一个块级格式上下文的块级元素中，那么我们就可以通过[创建块格式上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)来避免这种情况。

## 参考
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)
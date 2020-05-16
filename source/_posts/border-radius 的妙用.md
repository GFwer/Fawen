---
title: border-radius 的妙用
tags: [CSS]
category: CSS
date: 2020-4-22 22:13
---
# border-radius 的妙用

## 前言
`border-radius`，一个常见的 css 属性，背后却隐藏着不为人知的故事。

## 多值

我们通常使用`border-raidus`一般是使用单个值的形式，如果多个值的话可能会分为下面几种：
- `border-top-left-radius`：左上方
- `border-top-right-radius`：右上方
- `border-bottom-right-radius`：右下方
- `border-bottom-left-radius`：左下方

通常这么使用：
```css
border-radius: 5px;
border-radius: 50%;
```
有没有想过如果`border-radius`多个值的情况该怎么形容？
```css
border-radius: 50% 30%;
border-radius: 50% / 30%;
```
上面例子展示了两种用法，第一种是 CSS 中比较常见的缩写写法，第二种就是比较特殊的写法了，来看看两种写法展示上的区别：
<div style="text-align:center;line-height:200px;height:200px;width:300px;background-color:gray;color:#fff;border-radius:50% 30%">50% 30%</div>
<div style="text-align:center;line-height:200px;height:200px;width:300px;background-color:gray;color:#fff;border-radius:50% / 30%">50% / 30%</div>

第一种写法：左上和右下 50% 圆角，右上和左下 30% 圆角
第二种写法看起来很怪异，实际上它对四角每一个值都应用上了`50% 30%`，为了探究这两个值到底代表什么，来写一个动画：
```css
@keyframes radius {
    from{
        border-top-left-radius: 0;
    }
    to {
        border-top-left-radius: 10% / 50%;
    }
}

.radius-container{
    height: 200px;
    width: 200px;
    text-align: center;
    line-height: 200px;
    background-color: gray;
    color: #fff;
}
.radius-left{
    animation: radius 5s infinite;
}
```
<style>
@keyframes radius {
    to {
        border-top-left-radius: 10% 50%;
    }
}

.radius-container{
    height: 200px;
    width: 200px;
    text-align: center;
    line-height: 200px;
    background-color: gray;
    color: #fff;
}
.radius-left{
    animation: radius 3s linear infinite;
}
</style>
<div class="radius-container radius-left">
top-left 从 0 变化至 10% 50%
</div>

从上面的例子可以注意到：
- 两个值，代表一个角的两条边，那么两个值正好控制两条边
- 两个值控制对应变量的对应边，比如说`border-top-right-radius: 50% 10%;`，左边的`50%`控制`top`这条边的圆角，右边的`10%`控制`right`这条边的圆角。
- 另外如果任意一个值为为`0`，则对应角没有圆角


这样基本就搞懂了圆角的规则了，同时缩写规则和`/`可以同时使用：
```css
border-radius: 50% 10% / 30% 20%;
// 对应
border-top-left-radius: 50% 30%;
border-top-right-radius: 10% 20%;
border-bottom-right-radius: 50% 30%;
border-bottom-left-radius: 10% 20%;
```

通过这些可以写一些奇怪的动画~~史莱姆~~，比方说：
```css
@keyframes radius-rotate {
    50% {
        border-radius: 45% / 42% 38% 58% 49%;
    }
    100% {
        transform: translate(-50%, -50%) rotate(720deg);
    }
}
.radius-rotate-container{
    height: 100px;
    width: 100px;
    text-align: center;
    line-height: 200px;
    background-color: gray;
    color: #fff;
    border-radius: 42% 38% 62% 49% / 45%;
    transform: translate(-50%, -50%) rotate(0);
    animation: radius-rotate 5s linear infinite both;
}
```
<style>
@keyframes radius-rotate {
    50% {
        border-radius: 45% / 42% 38% 58% 49%;
    }
    100% {
        transform: translate(-50%, -50%) rotate(720deg);
    }
}
.radius-rotate-container{
    height: 100px;
    width: 100px;
    margin: 60px 0 0 50px;
    text-align: center;
    line-height: 200px;
    background-color: gray;
    color: #fff;
    border-radius: 42% 38% 62% 49% / 45%;
    transform: translate(-50%, -50%) rotate(0);
    animation: radius-rotate 7s linear infinite both;
}
</style>
效果：
<div class="radius-rotate-container"></div>

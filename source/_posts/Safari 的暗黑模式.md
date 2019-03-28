---
title: Safari 的暗黑模式
tags: [Browser, Tips]
category: Browser
date: 2018-10-31 23:08
---

# Safari 的暗黑模式

众所周知，苹果在 Mojave 上推出了暗黑模式，可以让整个系统都变“暗”，整体体验非常不错，但是这对于浏览器这样的内容载体来说不是那么容易。

# Safari 12.1

在这个背景下，前几天苹果发布了新的 Safari Technology Preview 版本，让我们来看看其中的某一项更新：
![-w823](https://static.gongfangwen.com/15409989133789.jpg)

其中添加了一个实验性的 media API：`prefers-color-scheme`，通过它可以较方便的定义在深色模式下的样式和颜色~

下面就是一个示例：

``` scss
@media (prefers-color-scheme: light) {
    body {
        background-color: #ddd;
    }
}

@media (prefers-color-scheme: dark) {
    body {
        color: #ddd;
        background-color: #222;
    }
}
```

通过这样可以定义不同主题下的颜色（其实 light 主题下的条件可以不用写出来）。效果如下图：

![darkMode](https://static.gongfangwen.com/darkMode.gif)

兴奋之余不免遗憾，这还是一个实验性的属性，未来还不知道会是什么样子，好像 Windows 也退出了类似的”黑夜“模式，感觉未来各大浏览器支持的可能性还是比较大的，不过问题就是会如何支持。。我们只能希望有一个统一的、完善标准吧..

最后惯例催一催 Chrome 的 dark mode~

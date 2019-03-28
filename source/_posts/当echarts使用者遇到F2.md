---
title: 当echarts使用者遇到F2
tags: [F2, Echarts, Visualization]
category: Visualization
date: 2018-11-10 00:54
---

因为一些原因需要寻找一个比较适合移动端的可视化图标库，之前用的是 echarts，感觉 echarts 理念、UI、交互都还是更偏向桌面一点点，在移动端虽然能够兼容，但是很多时候使用体验并不会太好。

我第一个就想到的是 AntV 的 [F2](https://antv.alipay.com/zh-cn/f2/3.x/index.html) ，蚂蚁金服的轮子大部分都是很好看的（不过 echarts 的官网、配色也是好久以前的设计了），不像 echarts 一个库吃遍天下，AntV 分 G2（桌面）、G6 （关系）和 F2（移动）三大模块，这次我就先研究研究 F2。

## 开始

我承认，看到语法和文档一开始我是蒙的，感觉它的 API 的文档对初学者并不友好..（可能是我比较傻？）其配置方式可以说和 echarts 是完全不同...echarts 把所有的可配置项全部浓缩在一个 Option 对象内，然后通过实例 setOption 的形式生成图表，而 F2 一般只有一个唯一的数据来源（echarts 4 才刚刚支持 dataSet），然后其具体在 F2 之下分出几个大的方法，以实例调用方法的形式来对图表进行配置，我觉得这使得 F2 配置可读性较 echarts 差不少，而且 F2 的引用貌似做的并不好：

``` jsx
//如果要引入 interaction 的引入方式就不能从 @antv/f2 引入 F2 了
`const F2 = require('@antv/f2/lib/index');

require('@antv/f2/lib/interaction/');
```

## 问题

被需求驱动我的，就急切的开始要完成一个脑海中的图了，然而，事实总不是顺利的，首先在测试的时候我发现 F2 有些地方并不可以定制，我翻转了一个折线图，这时手指查看数据应该是和 y 轴平行移动并且实际上也是这样的，问题就在于 tooltip 的方向是不可以配置的，对于这个例子来说 ，tooltip 中的 crosshair 相当于平行于手指滑动方向了，可能想实现就需要借助 Guide 了~
接下来我在配置缩放和滚动的时候又遇到了个问题：配置初始缩放值，我很“理所应当”的往 interaction 中找，却发现配置只有寥寥数字。一番查找后在 issues 上看到了要配置另一个配置终点 values 值。。。而且这个值还是数组，在我的理解中在缩放的配置中应该有一个 echarts 中的 dataZoom.startValue 差不多才对。（何况 echarts 还支持百分比的 value）
总的来说，作为一个经常使用 echarts 的人来说，虽然个人感觉在移动端 F2 的理念的展现方式更为先进，但是不免还是遇到很多问题，大部分还是有些“水土不服”吧，

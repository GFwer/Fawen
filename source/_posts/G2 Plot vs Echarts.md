---
title: G2Plot 
tags: [G2, Echarts]
category: Visualization
date: 2019-12-15 23:20
---

~~## G2Plot  

`G2Plot`发布了！之前我试用`G2`非常不习惯的一点就是它不是类似`highcharts`或者`echarts`那样传统的`option`模式，而是将图表的每个部分分成一步步来创建，这种好处就是创建的逻辑很清晰，但是易用性和可读性却大大降低了，前段时间 AntV 团队又发布了`G2Plot`，试用了一下，接下来看看他能不能解决`G2`的那些问题，如果能解决，那么它和`echarts`对比起来呢？

## 图表类型
`echarts`完胜，不过`G2`也有词云图（`echarts`要额外引入）、子弹图、迷你图等它没有的图表类型

## 文档
在文档上，`G2Plot`差太多了，虽然好看，但是在翻阅时太麻烦了

## 代码量
这是一个我能想到的最最简单的`line`图
echarts:
```js
const option = {
    xAxis: {
      type: 'category',
    },
    yAxis: {},
    dataset:{
      source,
    },
    series: {
      type: 'line'
    }
};
```
G2Plot:
```js
const option = {
  data,
  xField: 'year',
  yField: 'value',
};
```
虽然这种对比没有意义，但是**在图表比较简单的情况下**，`G2Plot`的代码量确实少于`echarts`，代码也比较容易理解，即使图表复杂的话，G2Plot相比以前的用法也是相当简单了。

## 默认样式
G2 完胜

## G2Plot 缺少的细节
1.的环图在切换legend的时候是没有过度效果；
2.dataZoom 好像暂时缺失了；~~

准备重写。。
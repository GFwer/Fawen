---
title: CSS 固定宽高比
tags: [CSS, Tips]
category: CSS
date: 2018-11-23 23:19
---

# CSS 长宽比

想到这个是因为前一段时间想一个子元素宽度为百分比值，高度和宽度相等的方案的时候延伸出来的，当时的解决方案是这样的：

``` css
div{
  width:20%;
  padding-top:20%;
  background:red;
  position:relative;
  height:0;
}
```

我们都知道，元素的 padding/margin 在值为百分比的时候，它们的值是根据其父元素的 width 来计算的。在这个场景下，固然可以通过 vw 解决问题，但是还想找一种更暴力（O-O）的方法，然后就有了这种方法..设定高度为 0，width 和 padding-top 均为 20%，这时候在个元素就是一个正方形了，然后子元素设定 absolute 就可以继续布局了；

那么要定制一款固定宽高比的元素，如果条件满足，可以先试试 vw 看看能不能一键满足，不然就可以尝试这种 padding-top/bottom 的方法，比如对于一个要求宽高比是 16:9 的元素：

``` css
height:0;
padding-top:calc(calc(100% * 9 / 16));
```

或者你可以给伪元素使用同样的方法，这样，一个具有宽高比的元素就出炉了！

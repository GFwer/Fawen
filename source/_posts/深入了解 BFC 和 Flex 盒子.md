---
title: 深入了解 BFC 和 Flex 盒子
tags: [CSS]
category: CSS
date: 2020-3-30 21:25
---
# 深入了解 BFC 和 Flex 盒子

## 前言

提前提过 BFC，关于 BFC 内容其实还挺多，值的好好研究研究。

## 概念
BFC 即块级格式化上下文，一个 BFC 就是一个区域，它规定了自己内部该如何布局，同时这个布局和外部不相干；

## 形成
BFC 的创建和块级/行内块元素 CSS 相关，常用的有：
- `html`标签；
- `position`为`absolute`或`fixed`；
- `overflow`不为`visible`；
- `display`为`inline-block`、`flex`、`grid`、`table-cell`、`table-caption`等（其中 flex 创建 FFC，grid 创建 GFC，与 BFC 相同，故归入 BFC）；
- `float`元素不为`none`。

## 规则/特性
BFC 形成之后有一定的规则，下面列举几个。

1. 同一个 BFC 下的元素外边距会发生折叠，解决方法可以创建不同 BFC（参见<a href="https://blog.gongfangwen.com/2020/03/18/CSS%E4%B8%AD%E7%9A%84%E5%A4%96%E8%BE%B9%E8%B7%9D%E9%87%8D%E5%8F%A0%E9%97%AE%E9%A2%98/"> CSS中的外边距重叠问题 </a>）

2. BFC 会包含浮动元素（清除内部浮动），在计算 BFC 高度的时候，浮动欧冠元素也要参与计算。

```html
<div id="parent">
    <div id="child" style="float:left;height:200px;"></div>
</div>
```

如上述代码所示，如果在没有 BFC 的情况下，parent 元素高度将会是 0（即高度塌陷），这时候如果创建一个 BFC，那么它将会包含浮动元素， parent 将会和 child 等高。

3. BFC 可以让其中元素不被外部的浮动元素遮盖。

```html
<div id="float" style="background:#eee;float:left">我是一个浮动元素啊啊啊</div>
<div id="text" style="background:red;height:200px;">我是一个前端匠，前端本领差</div>
```

展示如图：
![被遮盖了](https://static.gongfangwen.com/2020-03-30-15855634801450.jpg)

可以看到，浮动元素覆盖在文字的上方了，这时候如果让文字元素创建 BFC 就可以避免这个问题：

![给文字元素添加了 overflow: hidden](https://static.gongfangwen.com/2020-03-30-15855638529165.jpg)

在我的理解中，正常的文档流就是由一个个盒子组成的，每一个盒子根据自己的属性，创建不同的格式化上下文，其中就有 BFC（块） 和 IFC（行内），在 BFC 中，一个个元素垂直排列，在 IFC 中，一个个元素横向排列，其中的元素又可以根据各自的属性形成不同的格式化上下文，正是由这些格式化上下文，才完整地组成了一个页面。


## flex 弹性盒子
flex box 即弹性盒子，在盒子内部，子元素沿着其主轴或交叉轴排布，由`flex-direction`控制主轴方向，同时子元素由其他属性控制伸长和缩短来适应 flex 容器。
我认为 flex 中比较难以理解的是`grow`、`shrink`和`basis`三大将（他们可以合并为`flex`）
```css
div1 {
    flex: 1; 
    // 其实只设置了 flex-grow，等价于
    flex-grow: 1;
    flex-shrink: 1; // 我是默认值
    flex-basis: 0%;
}

div1 {
    flex: auto; 
    // 只要设置了不为数字的值，等价于设置 basis 同时 flex-grow 设置为 1
    flex-grow: 1;
    flex-shrink: 1; // 我是默认值
    flex-basis: auto;
}
```

### 拉伸 or 收缩
1. `grow`需要容器空间分完之后，还有剩余空间的时候才生效。
首先`flex-grow`表示拉伸的单位，默认值为`0`，如果值为`0`，那应该表示，”我只占用我应该占用的空间，其他我不要“，如果有不为零的值，那么除去所有必须的宽度后，就按照比例相应分配宽度；

2. `shrink`需要在没有剩余空间的时候才生效（子元素宽度和大于容器）
`flex-shrink`表示收缩的单位，默认值为`1`，那么`shrink`会生效，以`shrink`为`1`、`2`、`2`，超出宽度`40px`为例，收缩单位空间为`40/5 = 8px`，如果没有边框，那么三个元素分别收缩：`8px`、`16px`、`16px`

### basis 和 width
`flex-basis`是个很奇妙的值，因为事实宽度不一定完全按照设定来的，他代表一个假想值，”在空间足够，内容没超出假想值的情况下的一个理想宽度“。分为以下几种情况：
- flex 容器很大，完全容下了所有子项，所有子项的内容没超过`basis`的宽度，那么此时实际宽度等于`basis`值（默认`content-box`下`basis = width - border = content`，`border-box`下`basis = width + border`，以下不在赘述）；
- flex 容器足够大，但是子项内容超过了`basis`宽度，那么此时实际宽度为刚好能容纳下内容的宽度；
- flex 容器不够大，无论`basis`能容纳下内容，但是耐不住父元素不够啊，此时元素实际宽度为正好能容纳内容的宽度，这时候可能会超出容器宽度。

`flex-basis`和`width`同时应用的时候该如何计算？遵从以下规则吧：
- `flex-basis`优先级比`width`高；
- `flex-basis`宽度还是受到`max-width`和`min-width`的限制。

#### flex-basis 0 和 auto 的区别
- `flex-basis: 0`代表能有多小有多小，即宽度等于子元素最小宽度（其中内容可能换行，即使父容器宽度不换行也能容纳）；
- `flex-basis: auto`代表最小展示需要多少给多少，即宽度等于能展示出来的最小宽度（如果容器够大就不换行了，容器不够大可能还会换行）

### 那些与 flex 相冲的属性
查阅了 W3C 相关内容，对 flex 容器相关内容如下（渣翻译）：
> flex 容器创建了一个新的、除了 flex 布局其他都与 BFC 相同的 flex 格式化上下文（我偷偷命名为 FFC）。比如浮动元素不会覆盖其上方、flex 容器也不会和子元素发生外边距折叠现象，和块级容器一样，flex 容器对齐内容形成一个包裹块，overflow 属性也同样适用于 flex 容器。
> 但是 flex 容器不是块（block）容器，因此一些对于块容器设定的属性对于 flex 容器不生效，特别是
> - `float`和`clear`不会使 flex 子项浮动或清除浮动，也不会令他们脱离文档流。
> - `vertical-align`不影响 flex 子项。
> - `::first-line`和`::first-letter`不会应用在 flex 容器。
> 
> `inline-flex`在某些写特殊情况会被计算为`flex`

从上可知，有些属性在 flex 上是不生效的，由于一些属性是为 block 容器设定的，同时 flex 已经接管了容器内子元素的布局、居中相关属性，所以子元素的`float`、`clear`和`vertical-align`将不起作用。


## 参考
- [块格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
- [flex](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex)
- [CSS Flexible Box Layout Module Level 1](https://www.w3.org/TR/css-flexbox-1/)
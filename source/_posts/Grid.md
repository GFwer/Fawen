---
title: Grid布局
tags: [CSS, Grid]
category: CSS
date: 2018-11-30 15:12
---

# Grid

## Grid 与 Flex

flex 一般用于一维的布局（一行、一列），grid 可以用于二维的布局。

## Grid 容器

``` scss
grid-template-column: size | [name] size;
grid-template-row:    size | [name] size;

grid-template-column: 90px 80px auto 80px; //共四列，从左到右列的宽度为50% 20px 自动和50px
grid-template-row:[第一行] 60px [第二行] 90px [第三行] auto; //[第一行] 60px [第一行结束 第二行开始] 90px [第三行] auto [最后]; //共三行，"[]"内为命名，可以使用空格分割多个，从上到下高度分别为50px 30px和自动
//如果二者只设置其中一个属性，则另一个属性自动按照设置并且平分

```

结果如下：
![-w418](https://static.gongfangwen.com/15438161860960.jpg)
如果某一行/列的值为`auto`，并且其他行/列的值之和超过的 grid 容器所设置的宽高，那么其他行/列依旧正常显示，设置为`auto`的行/列超出的属性为子元素的宽/高（0px），如下设置第一列的宽度为容器宽度的结果：

<p data-height="265" data-theme-id="dark" data-slug-hash="YRgZPp" data-default-tab="css,result" data-user="gfwer" data-pen-title="YRgZPp" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/YRgZPp/">YRgZPp</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

如果子元素很多可以使用`repeat()`:

``` scss
grid-template-columns: repeat(5, 20%) //五列，平分宽度
```

### fr

fr 是 grid 布局中可以使用的一个单位，使用它可以比较好的设置各行列之间的比例关系：

``` scss
grid-template-columns:repeat(3, 1fr); //三列，1:1:1三等分
grid-template-columns:2fr 1fr 1fr; //三列，2:1:1的关系
grid-template-columns:50px 1fr 1fr 1fr; //四列，第一列50px，剩余等分，即 calc((100% - 90px)/3)
grid-template-columns:auto 1fr 1fr 1fr; //四列，第一列宽度等于子元素宽度，剩余三列平分
grid-template-columns:auto 0.4fr 0.4fr 0.4fr; //没看懂，结果同上
grid-template-columns:auto 0.2fr 0.2fr 0.2fr; //没看懂..
```

看到最后两个其实有点蒙的
[W3C](https://www.w3.org/TR/css-grid-1/#fr-unit)是这样说的：

> Note: If the sum of the flex factors is less than 1, they’ll take up only a corresponding fraction of the leftover space, rather than expanding to fill the entire thing. This is similar to how Flexbox acts when the sum of the flex values is less than 1.

为了更好计算，于是更改成以下设置

``` scss
grid-template-columns:auto 0.25fr 0.25fr 0.25fr 0.25fr;
grid-template-columns:auto 0.2fr 0.2fr 0.2fr 0.2fr;
```

结果是第一个与之前结果一样，第二个的第一列比之前宽，经过一番计算，得出以下结论：
~~如果 fr 之和小于 1，那么 fr 元素的宽度之和等于其 fr 之和所占比例(之前)~~
如果 fr 之和小于 1，那么 fr 元素的宽度等于父元素减去固有宽度乘以其 fr 值（之后）
按照之前的规则来算的话，父元素宽度为 400px,那么上面的 0.2fr 宽度=400 \* 0.8/5,auto 宽度为剩余之和。
不过之后发现搞错了优先级，grid 子元素宽度实际上和其子元素宽度息息相关，如果 auto 元素的子元素大于最后分配的值，那么之前的规则就失效了，
所以正确的规则应该是：fr 元素宽度=(父元素-固有宽度)\*fr 值；

<p data-height="265" data-theme-id="dark" data-slug-hash="YRgZPp" data-default-tab="css,result" data-user="gfwer" data-pen-title="YRgZPp" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/YRgZPp/">YRgZPp</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>

### grid-template-area

在上面设置中，只能简单的把一个整体分层一个个区域，而且还要书写 row\*column 个 div 块，要解决这个问题，还需要通过`grid-template-area`划分区域：

``` scss
grid-template-areas:
    “name | . | none”
    “name | . | none”; //name 代表名字，. 代表空的格子（有元素分配的话就有个字，没有的话效果约等于none），none 表示没定义网格
grid-template-areas:
    "一大块 一大块 两大块 两大块"
    "一大块 一大块 两大块 两大块"
    "一大块 一大块 三大块 三大块"
    "一大块 一大块 三大块 三大块";
```

之前把 grid 容器划分成了 4\*4 的格子，通过这个步骤相当于命名了每个格子的名字，同时在给 grid 容器的子元素命名：

``` scss
  .yi{
    grid-area:一大块;
  }
  .er{
    grid-area:两大块;
  }
  .san{
    grid-area:三大块;
  }
```

实际效果如下：

<p data-height="265" data-theme-id="dark" data-slug-hash="eQoGZp" data-default-tab="css,result" data-user="gfwer" data-pen-title="eQoGZp" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/eQoGZp/">eQoGZp</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>

### gap

通过`grid-column-gap`和`grid-row-gap`定义间隙：

``` scss
grid-column-gap:10px;
grid-row-gap:5px;
//grid-gap:5px 10px; //合并后
```

<p data-height="265" data-theme-id="dark" data-slug-hash="pQBWBb" data-default-tab="css,result" data-user="gfwer" data-pen-title="pQBWBb" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/pQBWBb/">pQBWBb</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>

### align/justify items

`align-items`和`justify-items`定义了每个格子的垂直和水平的排列方式，类似于对每个格子的子元素使用 flex 布局的`align-items`(只不过 flex 没有 justify-items)，除了`stretch`是充满，其他会根据子元素大小自动调整实际显示大小（不过不会超出初始格子范围）

``` scss
align-items: start | end | center | stretch;
justify-items: start | end | center | stretch; //开始，结尾，居中，充满
```

两者都设定为`center`结果如下：

<p data-height="265" data-theme-id="dark" data-slug-hash="pQBWBb" data-default-tab="css,result" data-user="gfwer" data-pen-title="pQBWBb" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/pQBWBb/">pQBWBb</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>

### align/justify content

和`align-items`和`justify-item`无法超出初始定义格子位置不同的是，`align-content`与`justify-content`可以超出初始格子位置，其用法和值类似于 flex 布局中的`justify-content`(相当于对每个 area 使用，前提是 grid 元素尚未被充满)

``` scss
justify-content: start | end | center | space-between | space-around | space-evenly | stretch;
align-content: start | end | center | space-between | space-around | space-evenly | stretch;
```

两个值都设置为`center`的结果如下：

<p data-height="265" data-theme-id="dark" data-slug-hash="pQBWBb" data-default-tab="css,result" data-user="gfwer" data-pen-title="pQBWBb" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/pQBWBb/">pQBWBb</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>

### grid-auto-columns/grid-auto-rows

这两个属性用在定义行列之外的格子时使用，如果我定义了一个 4\*4 的格子，后面突然需要在第五列加上一个格子，又不想改变原来 4\*4 的配置，这时候（定义的格子超出规定范围之外）可以使用 grid-auto-columns/grid-auto-rows 创建“隐式网格”，也就是在在规定之外的非正常的网格。

``` scss
//LESS
.container{
  display:grid;
  width:200px;
  height:200px;
  border:1px solid #000;
  grid-template-columns:100px 50px;
  grid-template-rows:100px 100px;
  grid-auto-columns: 70px;
  grid-auto-rows:100px;
  >div{
    outline:1px solid red;
  }
  .a{
    grid-column:~"3/4";//列的第三条线至第四条线
    grid-row:~"2/3";//行的第二条线至第三条线
  }
  .b{
    grid-column:~"2/3";
    grid-row:~"1/2";
  }
}
```

在上面的例子中定义了一个 2\*2 的布局，但是我突然想加一个格子到所谓的第三列上，但是这又不是一个\*3 的布局，name 就可以使用这两个 auto 属性，容器自动生成了两个 70\*100 的格子，并把 a 放在了上面，这时候实际的格子划分如下：
![-w234](https://static.gongfangwen.com/15439110017310.jpg)

这时候，grid 容器实际上已经变成了一个 3\*3 的布局，所以其余格子的布局也可能改变。

<p data-height="324" data-theme-id="dark" data-slug-hash="pQBWBb" data-default-tab="css,result" data-user="gfwer" data-pen-title="pQBWBb" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/pQBWBb/">pQBWBb</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>

### grid-auto-flow

该属性规定未被约束位置的格子的排列方式。

``` scss
grid-auto-flow: row | column | row dense | column dense;//dense表示排列尽可能紧凑，可能与DOM顺序不一致
```

下面是`column`和`column dense`的差异：

<p data-height="430" data-theme-id="dark" data-slug-hash="dQLmmE" data-default-tab="css,result" data-user="gfwer" data-pen-title="dQLmmE" class="codepen">See the Pen <a href="https://codepen.io/gfwer/pen/dQLmmE/">dQLmmE</a> by Fawen (<a href="https://codepen.io/gfwer">@gfwer</a>) on <a href="https://codepen.io">CodePen</a>.</p>

## grid 子项

### grid-column 和 grid-row

实际上相当于`grid-column-start`、`grid-column-end`与`grid-row-start`、`grid-row-end`,表示该子项的行/列从第几条线开始值第几条线结束。

### align/justify self

类似于 flex 中的`align-self`属性（只不过 flex 中没有 justify-self），它规定每个格子内元素的垂直/水平对齐方式。

## 参考资料

[张鑫旭的博客](https://www.zhangxinxu.com/wordpress/2018/11/display-grid-css-css3/#grid-auto-columns-rows)

---
title: viewport 那些事
tags: [HTML]
category: HTML
date: 2020-04-10 23:56
---
# viewport 那些事

## 前言

自从智能手机操作系统推出以来，手机浏览器浏览是越来越方便，但同时也带来了问题：PC 端的页面只针对 PC 设计，手机打开啥样？由于 PC 的视口很大，所以手机只能等比地缩小手机上的页面以展示内容，这就导致了手机上的元素都特别小。

## viewport
所以 viewport 就出现了，如果是 CLI 工具初始化项目，一般都会生成这么一行代码：
```html
<meta name="viewport" content="width=device-width,initial-scale=1.0">
```

所谓 viewport，就是视口，代表了实际可视窗口的大小，实际上根据[规范](https://www.w3.org/TR/2016/WD-cssom-view-1-20160317/#dom-element-clientwidth)，它实际上等于 window 的 `clientWidth`大小：
> If the element is the root element and the element’s node document is not in quirks mode, or if the element is the HTML body element and the element’s node document is in quirks mode, return the viewport width excluding the size of a rendered scroll bar (if any).

这样，通过在配置不同的视口大小就能适配不同的设备了，下面是 viewport 的一些配置：

| 名字 | 可能的值 | 意义 |
| :-- | :-- | :-- |
| `width` | 正整数/`device-width` | 视口的**像素**宽度 |
| `height` | 正整数/`device-height` | 视口的高度，**未被任何浏览器使用** |
| `initial-scale` | (0,10)之间的正整数 | 设备宽度和视口大小之间的比率 |
| `maximum-scale` | (0,10)之间的正整数 | 可放大的最大级别，iOS 10+ 无效 |
| `minimum-scale` | (0,10)之间的正整数 | 可缩小的最大级别，iOS 10+ 无效 |
| `user-scalable` | `yes`/`no` | 用户是否可以放大网页，iOS 10+ 无效 |
| `viewport-fit` | `auto`/`contain`/`cover` | `auto` 表示整个网页可见 `contain` 表示视口缩放至最大的级别以适应显示器（完全覆盖视口，可能内容不可见）
`cover` 表示视口缩放以填充设备显示（完全展示所有内容，可能内容宽度小于视口）
感觉效果类似`background-size` |

根据 CLI 生成的模板，默认已经配置了初始比率和视口宽度，所以都能够正常显示。

## DPR
DPR，即 Device Pixel Ratio，设备像素比，大家都知道每英寸像素数量 PPI，PPI 低的手机常被形容“大果粒”，为了提高 PPI，只能在相同的空间中塞入更多的像素，那么这时候设备的原生分辨率就和实际分辨率不一样了，所谓 DPR 就是两者的比值，在 CSS 中设定的 1px 是逻辑值，实际显示出来的大小是乘以 DPR 的大小。

DPR 相关的有著名的 1px 问题，另外也经常用于布局，比如头条主页，根据 DPR 动态更改 html 标签的`data-dpr`以适应多种布局：
![dpr](https://static.gongfangwen.com/2020-04-11-15865340909388.jpg)

### 参考
[MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta)
---
title: 简单实现一个B站视频动态预览效果
tags: [实践]
category: 实践
date: 2020-04-05 18:32
---
# 简单实现一个B站视频动态预览效果

## 前言
很久没上 PC 版的 B 站，看到一个挺有意思的功能，鼠标悬浮在视频上可以动态预览视频截图（是不是在哪里见过？），上方还有进度条，随着弹幕穿插而过，仿佛真的是在播放一样，看到这个有趣的功能，不禁琢磨起它的实现，从零开始自己实现一个。
![动态预览](https://static.gongfangwen.com/2020-04-05-bilibili_vidio.gif)

## 分布拆解
首先把上面的效果一步步拆解出来分析出其原理：
### 非交互状态
1. 左上角皇冠表示播放量排名，应该就是后台传回来的一个`rank`，根据 rank 类型拿对应图标，这里看到的是`gold`、`sliver`之类的，具体不再赘述。
2. 播放量、点赞数量、视频长度，这个三个数直接排布，没什么好说的，主要还是交互状态。

### 交互状态
1. 鼠标悬浮，上方产生进度条，目测进度条根据鼠标在整体封面中的位置来确定百分比。
2. 显示预览效果，根据进度条百分比显示相应位置的画面，这里应该是预先内置了一部分图片，雪碧图动态改变`background-position`，实际上这是一个 10 x 10 的包含 100 张截图的图片，每一行正好对应了 10%。
3. 弹幕效果，预先内置 10 条左右的弹幕，悬浮大概一秒后飘出弹幕，弹幕速度看上去不太固定，猜想可能是随机的从速度列表中选的，一条弹幕发射后一定时间发射第二条，如此循环。

分析完了这些功能后，可以开始一个个功能实现了。

## 实现
## 图片格式
发现 B 站大多数都使用了`WebP`格式的图片，这种格式相比`jpg`格式有着更好的压缩性能，相同图片质量能够很好地优化图片大小，缺点主要是兼容性问题。
![WebP 兼容性](https://static.gongfangwen.com/2020-04-05-15860743099758.jpg)
所以这里其实还有兼容性设置，对于 Chrome 等兼容的浏览器使用 WebP，不兼容的浏览器使用 jpg，不过这不在我们讨论范围内。

## 来实现！

<p>
<iframe
     src="https://codesandbox.io/embed/bilibili-danmu-27017?fontsize=14&hidenavigation=1&theme=dark&view=preview"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="bilibili-danmu"
     allow="geolocation; microphone; camera; midi; vr; accelerometer; gyroscope; payment; ambient-light-sensor; encrypted-media; usb"
     sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"
   ></iframe>
</p>

写的还是有点仓促，没考虑太多，有些地方还是有逻辑问题，感觉难点主要在于：
- 弹幕列表，位置，动画等
- 性能提升
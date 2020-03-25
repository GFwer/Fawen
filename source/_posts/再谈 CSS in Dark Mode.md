---
title: 再谈 CSS in Dark Mode
tags: [Browser, CSS]
category: CSS
date: 2020-3-22 23:43
---
# 再谈 CSS in Dark Mode

## 前言
一年多以前，我在 <a href="https://blog.gongfangwen.com/2018/10/31/Safari%20%E7%9A%84%E6%9A%97%E9%BB%91%E6%A8%A1%E5%BC%8F/" target="_blank">Safari 的暗黑模式</a> 中提到过对于 Dark Mode 的使用，一年多过去了，各大系统纷纷适配黑暗模式，其中`prefers-color-scheme`如今主流浏览器均已经支持：
![media 支持](https://static.gongfangwen.com/2020-03-22-15848885734400.jpg)

除了不知道使用什么内核/不知道什么时候才升级内核的国产手机厂商，支持很完善，不禁令人感叹，“黑夜”将至。

## 状态
根据 4 天前的的[Media Queries Level 5](https://drafts.csswg.org/mediaqueries-5/#prefers-color-scheme)，目前`prefers-color-scheme`还处于草案状态，意味着这个标准依然可能会被更改，不过根据主流的Chromium、Safari 已经实现来看，几乎能确定未来就是这种形式了。

## 用法
依然是之前的用法，不过我们结合 CSS 变量使用效果更佳：
```css
:root {
  --primary-background: #ddd;
  --primary-text: #000;
}
@media (prefers-color-scheme: dark) {
  :root {
    --primary-background: #000;
    --primary-text: #ddd;
  }
}
body {
  background-color: var(--primary-background);
  color: var(--primary-text);
}
```
题外话，最近准备修改博客的样式，适配黑暗模式，同时最近发现一个问题，修改了样式文件之后需要几天才能生效，而每次更新我都刷新了 CDN，这个缓存搞得我百思不得其解，最近准备抽空好好研究研究。

## 思考
今天微信终于适配了黑暗模式，微信算是近几年移动 App 中的一个特殊的例子，其他国产厂商做 App 是什么功能都往上加，疯狂地想把流量变现，微信不一样了，它反而是越变越简单，以至于用户一直想在微信上加功能，后来就有了“教张小龙做产品这个梗”。
我在想，也许微信这种模式才是一个移动 App 应该有的吧，从语音到公众号，从红包到微信支付，从小程序到微信生态，每个更新都实实在在影响了整个行业。当一个产品真正成为国民应用，不再为流量不够而担心的时候，产品是不是才能成为它原来应该有的样子？每一个微信功能相关话题都能上微博热搜，这在移动 App 之中，怕是独此一家吧？这就是微信的魅力吧。
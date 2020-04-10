---
title: Polymer
tags: [Web Components]
category: web components
date: 2020-04-08 21:15
---
# Polymer

## 前言

今天晚上看 Youtube 随便看了看竟然是用 Web Components 写的...

## 往事不堪回首 
想想这些年，从最开始的静态页面，到后来的 Bootstrap，再到现在 React/Vue/Angular 和在其之上搭建的 UI Framework，浏览器见证了太多，然而 Web Components 在国内没见到什么风浪，到底虚拟组件和原生组件谁会更胜一筹？

## Polymer Project
Polymer 是谷歌推出的一套原生 Web Components 框架。
不过在 2018 Google I/O 后：
> This is the current stable version of the Polymer library. At Google I/O 2018 we announced a new Web Component base class, LitElement, as a successor to the PolymerElement base class in this library.

嗯...官方已经推荐使用`LitElement`了，可以把它看做是`Polymer`中的`PolymerElement`单独拿出来了。

## 要想页面写得好，DOM 操作少不了

从[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Web_Components)上来看，频繁的原生 DOM 操作是 Web Components 不可避免的问题，看看`LitElement`怎么做（说真的`Polymer Project`的官网 UI 风格太戳我了吧， Material 性冷淡风！ ）：

```javascript
import { LitElement, html, property, customElement } from 'lit-element';

@customElement('simple-greeting')
export class SimpleGreeting extends LitElement {
  @property() name = 'World';

  render() {
    return html`<p>Hello, ${this.name}!</p>`;
  }
}
```

```html
<simple-greeting name="Everyone"></simple-greeting>
```

第一眼，怎么这么像 Angluar？想了想，毕竟一个妈生的嘛...

看起来还不错，不过依靠惯性思维想了一想，路由、状态管理等等问题接踵而来，我想到了[SVELTE](https://svelte.dev/)..

## 思考
所以使用 Web Components 还会是未来吗？
回顾现在，大量应用还在停留 Jquery 时代，这似乎并没有影响我们的使用，都说科技以换壳为本，真正的科技却是让人感觉不到科技的存在。虚拟组件？ 原生组件？其实都不重要，就如同 Vue/React 这对冤家来说，相互借鉴，相互成长有什么不好？适合自己的才是最好的。
在这个人人全家桶的年代，原生支持已经不是最大的优势了，就像 React 未来肯定不会成为标准，但可能会实实在在影响标准，当人人都能通过简单的 API 一键创建理想的应用的时候，那才是真正的 Web Components 吧。

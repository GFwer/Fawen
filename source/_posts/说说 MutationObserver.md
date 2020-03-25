---
title: 说说 MutationObserver
tags: [JavaScript]
category: JavaScript
date: 2020-3-25 21:00
---
# 说说 MutationObserver

## 前言
我在<a href="https://blog.gongfangwen.com/2020/03/18/%E7%BB%86%E8%AF%B4%20JavaScript%20%E8%BF%90%E8%A1%8C%E6%9C%BA%E5%88%B6%20%E2%80%94%E2%80%94%20Event%20Loop/" target="_blank"> Event Loop </a>中提到过微任务包括`Promise`和`MutationObserver`，那么`MutationObserver`又为何物？今天详细来了解了解。

## 简述
`MutationObserver`提供了监视对 DOM 树所做更改的能力，通过它，我们能够监测到 DOM 的增删、属性变化、文本的改动等。

可以通过构造函数`MutationObserver()`新建一个监听器，它接收一个回调函数，返回一个监听对象，通过对象的方法就可以监测到 DOM 的改动。

```javascript
var observer = new MutationObserver(callback);

```


它的兼容性如下图：

![兼容性](https://static.gongfangwen.com/2020-03-25-15851395373137.jpg)


## 方法

新建完一个监听对象之后，通过以下这些对象的方法来控制监听：

- `observe()`，配置监听器开始监听，回调函数开始被调用；
- `takeRecords()`，从`MutationObserver`的通知队列中删除所有待处理的通知，同时将它们返回到`MutationRecord`对象中；
- `disconnect()`，终止监听，回调函数不再被调用；

下面一个个来说一下：

### `observe()`

新建监听对象之后，需要设置监听的`DOM`和配置，这些通过`oberve`方法来完成：

```javascript
const oberve = new MutationObserver(callback);

oberve.observe(node[,option])
```

`option`为一个配置对象，它可以提供的配置属性如下（他们都是非必须的）：

| 属性                      | 描述                                                   |
|-------------------------|------------------------------------------------------|
| `attributes`            | 是否监听元素属性的变化，无默认值                                     |
| `attributeOldValue`     | 是否在元素属性变化时记录原来的属性值，无默认值                              |
| `attributeFilter`       | 要监听的属性名组成的数组，默认对所有属性进行监听                             |
| `childList`             | 是否监听元素的子节点的添加/删除，默认为`false`                          |
| `characterData`         | 是否监听目标节点包含的字符数据的变化，无默认值                              |
| `characterDataOldValue` | 目标节点或子节点中节点包含的字符数据变化时，记录变化之前的值，无默认值                  |
| `subtree`               | 是否将监听范围扩展至目标元素的子节点，如果为`true`，则其他属性也对子节点生效，默认为`false` |

监测之后会给到回调函数一个数组，它包含了改变 DOM 的集合数组，通过其中的`type`字段识别是哪个字段的变化，数据格式如图所示：

![MutationRecord](https://static.gongfangwen.com/2020-03-25-15851379049133.jpg)


- `addedNodes`: 被添加的元素
- `attributeName`: 发生变动的属性
- `attributeNamespace`: 变动属性的命名空间
- `nextSibling`: 下一个同级节点
- `oldValue`: attribute/Content 变化前的值
- `previousSibling`: 上一个同级节点
- `removedNodes`: 被删除的元素
- `target`: 变化作用到的元素
- `type`: DOM 更改的类型


### `takeRecords()`
需要说明的是，`MutationObserve`监听是异步的，它不会在每次变化的时候都触发回调函数，变化的时候可以假定监听到的变化先存在一个数组之中等待执行，等待微任务执行之后才触发回调函数，`takeRecords`方法就是获取这个数组的数据，然后清空这个临时的数组。

> 此方法最常见的使用场景是在断开观察者之前立即获取所有未处理的更改记录，以便在停止观察者时可以处理任何未处理的更改。

```javascript
// 创建
var observer = new MutationObserver(callback);
// 监听
observer.observe(targetNode, observerOptions);
// .....此处省略 1w 行，反正就是更改了很多 DOM

// 这时候要停止监听了，先获取还未传给回调的列表，同时清空
var mutations = observer.takeRecords();

if (mutations) {
  // 传给回调
  callback(mutations);
}

// 然后在停止监听
observer.disconnect();
```

### `disconnect()`
这个没啥好说的，`observe`对象调用该方法停止监听；
```javascript
observer.disconnect();
```

### 异步的思考
再说一说异步的问题，如果`MutationObserver`是同步的，那么可能回调函数会被疯狂调用，阻塞代码执行，所以采用了异步形式，减少了对性能的影响，Vue 中的`Vue.nextTick`就是通过`MutationObserver`实现的（源码的话还没看过，不过以后对于基础框架内的源码肯定得好好研究研究，提升逻辑能力和水平，现在就以“可以写一篇如何看源码的文章”为目标吧）。
```javascript
// 使用
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})
// 以下是源码
let counter = 1
const observer = new MutationObserver(flushCallbacks)
const textNode = document.createTextNode(String(counter))
observer.observe(textNode, {
    characterData: true
})
```

## 后记
终于要写一次和主题无关的后记了...在上传这篇文章的时候遇到了一个 hexo 的非常严重的 bug，起因就是在代码块后面加了几个空格，导致 CDN 图片无法解析，显示成 hexo 的模板字符串的形式，然后文章内容还渲染重复了，我还以为是我写的问题，查了半天..`github`上也没查到，难道我就是天选之子？

## 参考
- [MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)
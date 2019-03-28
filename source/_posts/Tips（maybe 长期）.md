---
title: Tips
tags: [Tips]
category: Tips
date: 2018-07-09 22:25
---

## Tips

### filter

``` js
[].filter(function(item){
	reutnr item.id != filter_id
})
```

### Object-fits

[demo](http://jsfiddle.net/v1urb2sf/16/)

- fill

  > 被替换的内容大小可以填充元素的内容框。 整个对象将完全填充此框。 如果对象的高宽比不匹配其框的宽高比，那么该对象将被拉伸以适应。

- contain

  > 被替换的内容将被缩放，以在填充元素的内容框时保持其宽高比。 整个对象在填充盒子的同时保留其长宽比，因此如果宽高比与框的宽高比不匹配，该对象将被添加“黑边”。

- cover

  > 被替换的内容大小保持其宽高比，同时填充元素的整个内容框。 如果对象的宽高比与盒子的宽高比不匹配，该对象将被剪裁以适应。

- none

  > 被替换的内容尺寸不会被改变。

- scale-down
  > 内容的尺寸就像是指定了 none 或 contain，取决于哪一个将导致更小的对象尺寸。

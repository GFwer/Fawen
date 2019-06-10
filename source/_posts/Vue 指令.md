---
title: Vue 指令
tags: [Vue, Directive]
category: Vue
date: 2019-06-08 23:45
---

# Vue 指令

## 默认指令
- `v-text`，展示文本，替换标签的内容
```
<span v-text="nihao">hello</span>
<!-- 展示nihao -->
```
- `v-html`,展示 html,替换标签内容
```
<span v-html="'<p>nihao</p>'">hello</span>
<!-- 展示 p 标签 nihao -->
```
- `v-show`，根据其中值判定元素是否展示（控制`display`值）
```
<span v-show="show">display when show is true</span>
```
- `v-if`，根据其中值判定元素是否展示（`false`时移除 `DOM`）
- `v-else-if`，同上，接在`v-if`之后
- `v-else`，同上。
```
<span v-if="number === 1">show when number === 1</span>
<span v-else-if="number === 2">show when number === 2</span>
<span v-else>show when number !== 1 && number !== 2</span>
```
- `v-for`，循环。
```
<span v-for="item in [1,2,3,4]" :bind="item">
    {{item}}
</span>
```
- `v-on`，事件监听。
```
<button v-on="flag = !flag">click me</button>
```
- `v-bind`，绑定一个值。
```
<span v-bind="msg" ></span>
```
- `v-model`，双向绑定一个值。
```
<input v-model="msg" />
```
- `v-pre`，渲染"{{ }}"并防止其被解析。
```
<span v-pre="{{ show }}"></span>
<!-- 展示 {{ show }} -->
```
- `v-cloak`, 示例渲染未完成时存在于元素上，渲染完成自动移除属性。
```
[v-cloak] {
  display: none;
}
<div v-cloak>
  {{ message }}
</div>
<!-- message will show after render -->
```
- `v-once`，只渲染一次。
```
<span v-once="msg"></span>
```
- `v-slot`，插槽，之后详细讲。

## 自定义指令
`Vue.directive('difine')`定义全局指令，组件中在`directive`中定义:
```
directive: {
    define: {
        bind: function(){},
        inserted: function(){},
        update: function(){},
        componentUpdated: function(){},
        unbind: function(){}
    }

}

```
之后使用`v-define`来使用指令，Vue 提供了生命周期钩子帮助构建指令：
- `bind`：调用一次，指令第一次绑定到元素时调用，用于初始化设置；
- `inserted`：被绑定元素插入父节点时调用，保证父元素存在，但是不一定在文档中；
- `updated`：所在组件的`VNode`更新时调用，其中指令的值可能更新可能还未更新，所以需要比对值来忽略不必要的计算；
- `componentUpdated`：指令所在组件的`VNode`及其子`VNode`全部更新后调用；
- `unbind`：调用一次，指令和元素解除绑定时调用；

钩子函数有如下参数：
```
el：绑定的元素
binding:{
    name:指令名字，如果指令是`v-define`值为`define`,
    value:指令绑定的值，如果是`1+1`形式的表达式是计算后的值，
    oldValue:指令绑定的前一个值，在 update 和 componentUpdated 两个更新生命周期中可用，
    expression：指令表达式，如果是计算形式的是计算前的值，
    arg：传递给指令的参数，`v-define:age`中值为 age，
    modifiers：传递给指令的修饰值，`v-define.stop`中值为{ stop : true }
},
vnode：Vue 生成的虚拟节点，
oldVnode：上一个虚拟节点，同样只能在 update 和 componentUpdated 两个更新生命周期中可用
```
---
title:  Vue 插槽
tags: [Vue, Slot]
category: Vue
date: 2019-06-10 23:51
---


# Vue 插槽

在 Vue 中，插槽是一个极为重要的概念，它扩展了组件的用法，使得我们能够更加灵活地使用组件。
个人感觉类似 React 中的`children`。

## 基础

### 简单用法
可以这么使用插槽：
```
// 使用 title 组件
<title>sora</title>
// title 组件
<h2>
    <slot></slot>
</h2>
```
组件渲染的时候，会把`<slot>`标签替换成`sora`，事实上`sora`可以替换成任意的元素或组件。

### 后备内容
如果在`<slot>`标签内书写内容，那么该内容会在组件实际插槽内容为空时显示该内容：
```
// title 组件
<slot>backup</slot>
// 使用不在其中书写任何内容，显示 backup
<title></title>
```

### 具名插槽
如果想要在使用组件时书写多个插槽就需要使用具名插槽。
```
// 组件 news 定义
<div>
    <slot name="title"></slot>
    <slot name="comment"></slot>
</div>
// 使用
<news>
    <template v-slot:title>
        Test title
    </template>
    <template v-slot:comment>
        No comment
    </template>
</news>
```
插槽默认名字是`default`，如果不包裹`<template v-slot>`标签内则会被当做默认插槽内容。

## 作用域
在使用插槽的时候，如果想在其中书写组件作用域中存在的变量怎么办？那么其实就需要组件中传递值给组件外。
在某个组件中获取了用户信息，现想要在后备内容中显示用户名：
```
// 定义组件，使用 v-bind 将 user 的内容绑定给 user
<slot v-bind:user="user">
    {{ user.message }}
</slot>

// 使用1，slotProps 即组件内传递过来的包含所有 Props 的对象
<user>
    <template v-slot:default="slotProps">
        {{slotProps.user.name}}
    </template>
</user>
// 使用2，独占插槽，当只有一个模板的时候才可以使用
<user v-slot="slotProps">
    {{slotProps.user.name}}
</user>

// 使用3，解构 slotProps，将 user 重命名为 person
<user v-slot="{ user: person }">
    {{person.name}}
</user>
```

### 动态的插槽名
具名插槽的名字也可以是动态的：
```
<news>
    <template v-slot:[slotNames]></template>
</news>
```

### 缩写
具名插槽可做如下缩写：
```
// 原
<template v-slot:msg = { user }>
    sometimes naive..
    {{ user.msg }}
</template>
// 后
<template #msg = { user }>
    sometimes naive..
    {{ user.msg }}
</template>
```
默认插槽可以这样：
```
// 原，隐藏了个名字 default
<title v-slot= { user }></title>
// 后
<title #default= { user }></title>
```



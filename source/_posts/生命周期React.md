---
title: React生命周期
tags: [React, LifeCycle, API]
category: React
date: 2018-07-09 22:22
---

# 生命周期 React

#### getDerivedStateFromPropes

1. state 需要从 props 初始化的时候使用
2. 尽量不要使用
3. 每次 render 都调用
4. 如：表单获取默认值

#### componentDidMount

1. UI 渲染完成后调用
2. 只执行一次
3. 如：请求

#### componentWillUnMount

1. 移除时调用
2. 如：释放资源

#### getSnapshitBeforeUpdate

1. 在页面 render 之前调用，state 已更新
2. 如：获取 render 签的 dom 状态

#### componentDidUpdate

1.  每次 UI 更新都被调用
2.  如：props 变化获取数据

#### shouldComponentUpdate

1. 决定虚拟 DOM 是否重绘
2. 一版可以有 PureComponent 自动实现
3. 如：性能优化

![Alt text](./1_cEWErpe-oY-_S1dOaT1NtA.jpeg)

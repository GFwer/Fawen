---
title: PureComponent
tags: [React, Perfomance]
category: React
date: 2018-07-09 22:23

---

# PureComponent

React 15.3 中新增的 PureComponent 类，可以减少不必要的 render 次数。
Pure（纯的）

当组件更新的时候，如果组件的 props 和 state 都没发生改变，那么就不会触发 render。其原理是对其做一层浅比较：

``` jsx
if (this._compositeType === CompositeTypes.PureClass) {
  shouldUpdate = !shallowEqual(prevProps, nextProps)
  || !shallowEqual(inst.state, nextState);
}
```

shallowEqual 判断`Object.keys(state | props)` 的长度是否一致，每一个 key 是否两者都有，并且是否是一个引用（只比较第一层的值）

- 易变数据不能使用一个引用
- 不变数据使用一个引用
- 空对象或空数组容错
- 复杂状态与简单状态不要共用一个组件
- 与`shouldComponentUpdate`共存（有的话直接使用其结果）

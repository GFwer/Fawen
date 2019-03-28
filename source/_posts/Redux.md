---
title: Redux
tags: [React, Redux, Plugin]
category: React
date: 2018-07-09 22:23
---

# Redux

Redux 负责管理全局的状态，并且让组件通信更加容易。

全局只有一个 State，那就是 Store，所有的组件的状态都可以放在外部的 Store 中，这样的话如果 Store 发生了变化，组件也会相应更新，页面上的部分也会相应的变化。

### Redux 特性

- 唯一状态来源

  > 所有组件的状态都从 Redux 获取

- 可预测性

  > state + action = new state
  > 要产生一个新的状态必定是由一个新的 action 引起的

- 纯函数更新 Store
  > 通过 action 触发，并且通过一个 reducer 函数产生一个新的 Store

### Store

``` jsx
//产生Store
const store = createStore(reducer)
//获取Store内容
store.getState();
//监听Store内容变化
store.subscribe(()=>console.log(store.getState()))
```

### action

描述一个行为事件的一个纯 JS 对象，在被 dispatch 之后，Store 才能接收到它

### reducer

reducer 是一个函数，接收两个参数（之前的 state 和 action），返回一个新的 Store

### cmbineReducers

它接收多个 reducer 作为参数，返回一个新的 reducer（合并多个 reducer）

``` jsx
//合并reducer
export default combineReducers({
		reducer1,
		reducer2
})
//创建store
const store = createStore(combineReducers);
```

最终形成一个封装过的函数，会把 action 散发到对应 reducer 中

### bindActionCreators

每次需要传递 action 给 Store 的时候都需要 dispatch

``` jsx
store.dispatch(action)
```

这样每次都需要先拿到 Store，如果使用 bindActionCreators，就可以直接调用 action 的函数就可以更新 Store：

``` jsx
//重新包装action
action = bindActionCreators(action,store.dispatch)
//调用action，自动dispatch更新Store
action()
```

![Alt text](./WX20180625-235749@2x.png)

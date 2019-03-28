---
title: 不可变数据（Immutable Data）(长期)
tags: [React, Immutable, Performance]
category: React
date: 2018-07-09 22:25
---

## 不可变数据(immutable data，长期)

**setState 总会触发 render()**
**setState 总会触发 render()**
**setState 总会触发 render()**

**[demo](https://codesandbox.io/s/my9j7n6k4x)**
[pure 引发的问题](https://codesandbox.io/s/lyr5zxjmpl)

不可以直接修改某一个对象的值，而是要通过复制它的值产生一个新对象来改变它，他是 Redux 运行的基础。

优点：

- 性能优化，每次改变都能产生一个新对象，就可以通过引用来判断是否有变化来决定组件是否需要重新渲染
- 易于调试，任何时刻都能拿到之前的状态和之后的状态
- 易于推测，在任何时刻都能知道 Store 状态如何引起的

### 操作不可变数据

1. 原生写法 ： {...} 和 Object.asign

``` jsx
const state = {filter:'completed',todos:['learn React']}
//...的延展操作符把原来的属性都复制过来
const newState = { ...state,todos:[
	...state.todos,
	'Learn redux'
]}
//assign写法，性能最高
const newState = Object.assign({},state,{todos:[
	...state.todos,
	'Learn Redux'
]})
```

2. [immutability-helper](https://github.com/kolodny/immutability-helper)（用于层次比较深的更新）

``` jsx
import update from 'immutability-helper'
const state = {filter:'completed',todos:['learn React']}
//增加新的
const newState = update(state,{todos:{$push:['Learn Redux']}})
```

3. [immer](https://github.com/mweststrate/immer)（性能稍低，可以用类似原生的方式来写）

``` jsx
import produce from 'immer';
const state = {filter:'completed',todos:['learn React']}
const newState = produce(state,draftState =>{
	draftState.todos.push('Learn Redux')
})
```

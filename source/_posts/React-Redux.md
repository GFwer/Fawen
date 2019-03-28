---
title: React-Redux
tags: [React, Redux, Plugin]
category: React
date: 2018-07-09 22:25
---

## React-Redux

###connect

``` jsx
import { connect } from 'react-redux'
```

connect 的作用就是把组件和 Store 相连接

connect 工作原理就是高阶组件，把 Store 作为 props 传入组件

``` jsx
import React from "react";
import { bindActionCreators, createStore } from "redux";
import { Provider, connect } from "react-redux";

// 初始化Store的默认值
const initialState = { count: 0 };

// reducer
const counter = (state = initialState, action) => {
  switch (action.type) {
    case "PLUS_ONE":
      return { count: state.count + 1 };
    case "MINUS_ONE":
      return { count: state.count - 1 };
    case "CUSTOM_COUNT":
      return { count: state.count + action.payload.count };
    default:
      break;
  }
  return state;
};

// 创建 Store
const store = createStore(counter);

// 创建action
function plusOne() {
  return { type: "PLUS_ONE" };
}

function minusOne() {
  return { type: "MINUS_ONE" };
}

export class Counter extends React.Component {
  render() {
  //从props获取
    const { count, plusOne, minusOne } = this.props;
    return (
      <div className="counter">
        <button onClick={minusOne}>-</button>
        <span style={{ display: "inline-block", margin: "0 10px" }}>
          {count}
        </span>
        <button onClick={plusOne}>+</button>
      </div>
    );
  }
}
//返回组件需要的对应值，越少对性能影响越小
function mapStateToProps(state) {
  return {
    count: state.count
  };
}
//创建dispatch后的action方法
function mapDispatchToProps(dispatch) {
  return bindActionCreators({ plusOne, minusOne }, dispatch);
}
//把对应State和方法通过props传给Counter组件
const ConnectedCounter = connect(mapStateToProps, mapDispatchToProps)(Counter);

export default class CounterSample extends React.Component {
  render() {
    return (
    //根节点使用Provider组件，其子元素都可以访问Store
      <Provider store={store}>
        <ConnectedCounter />
      </Provider>
    );
  }
}
```

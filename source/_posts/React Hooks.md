---
title: React Hooks
tags: [React, API]
category: React
date: 2019-02-23 22:37
---

# React Hooks

## 函数式组件和类式组件

### 函数式组件

其中没有`state`，只能接收`props`

``` jsx
const ABC = () => {
	return (
		<div>
			Function Component
		</div>
	);
};
```

### 类式组件

其中有`state`,但是相比函数式，代码较多，有`this`，但是有完整的生命周期

``` jsx
class ABC extends Component {
	constructor(props) {
		super(props);
		this.state = {

		}
	}

	render() {
		return (
			<div>
				Class Component
			</div>
		);
	}
}
```

可以得知，如果想要使用本地`state`，或在组件的某个生命周期做些什么事，那么一定要使用类式组件，但是有了`Hooks`之后，一切都变得不一样了...

## Hooks 是什么？

`Hooks` 其实一个`React`中一种比较特殊的函数，我们约定它以`use`作为开头。通过它我们可以再不编写类的情况下使用一些类才有的高级功能。

## 为什么要使用它？

### 组件之间复用有状态的逻辑比较困难

如果一个状态不放在`store`中，那么重用这个状态将变得非常的困难，要不就一层层传递（我选择死亡），要不就使用`render prop`和`HOC高阶组件`来解决问题，但是这个可能导致组件数层数变得非常多

### 拒绝`bind this`

在函数式组件中使用，木有`this`问题，摆脱"xxx is undefined"。

### 如何使用它？

#### 基本用法

可以用 Hooks 实现一个简单的一个[计数器](https://codesandbox.io/s/moj415mm2j)：

``` jsx
import { useState } from "react";

const Counter = () => {
  const [count, counter] = useState(0);
  return (
    <>
      {count}
      <p>
        <button onClick={() => counter(count + 1)}>+</button>
      </p>
      <button onClick={() => counter(count - 1)}>-</button>
    </>
  );
}
```

- `useState` 设置首次渲染的初始值
- 数组第一项相当于这个`state`，第二项相当于`setState`的方法（但是不能新旧状态合并）

#### 生命周期?

`useEffect`一统天下，再也木有`componentDidMount`、`componentDidUpdate`和`componentWillUnmount`了，它默认在每一次渲染后运行。

``` jsx
useEffect(() => {
    console.log(111);
    return () => {
      console.log(222);
    };
},[]);
```

`useEffect`将会在每一次渲染后执行，并且不会阻塞浏览器渲染页面（这里没太懂..不知是否是指`componentDidMount`中`setState`会合成一次渲染而`useEffect`不会的意思？），如果只想在`mount`和`unmout`阶段执行，那么给`useEffect`加一个`[]`参数，这会告诉`React`这个`effect`不依赖任何`props`或`state`的值或者它不需要重新运行，如果将这个参数改成`[props.source]`，表示 effect 将会在`source`改变的时候重新运行。

## 后记

`Hooks`还有提供很多组件，比如`useReducer`、`useRef`、`useMemo`等等，确实让人感觉这是一个好特性，包括`Hooks`正式版特性发布后，许多开发者用其重构了自己的组件，还有`react-use`之类的相关库也开始崭露头角，这里就不继续讲了（因为看着看着发现 1 点多了，黑眼圈脱发猝死啦，赶紧睡觉去了..）

## 参考

[React Hooks](https://reactjs.org/docs/hooks-intro.html)

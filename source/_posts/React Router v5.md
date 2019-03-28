---
title: React Router v5
tags: [React, React-Router]
category: React
date: 2019-03-27 23:13
---

# React Router v5

## 前言：
是在是措不及防，我在看 Reach Router 的时候说 React Router v5 马上就来，然后前两天 React Router 官方就发布了 v5 的正式版，其实它本来应该是 4.4，但是由于在`react-rouer-dom`的依赖的路由版本`4.3.1`被误写成了`^4.3.1`，然后官方就撤销了`4.4`版本，并把它改成`5.0`发布。（这么说是不是有点钦定的感觉？）


## 特性
- 完全兼容 v4 版本
- 更好的支持 React 16
- 消除所有在严格模式 StrictMode 下的警告
- 新的 Context API(使用[create-react-context](https://www.npmjs.com/package/create-react-context))



## 功能/API
好像还是增加了一点点功能的，加上上次没写过的功能，一起写了吧。

### 引入

这个...我怎么记得之前好像也从`react-router-dom`中引入的...我记忆错乱了？？
```jsx
// Instead of:
import Router from 'react-router/Router';
import Switch from 'react-router/Switch';

// do:
import { Router, Switch } from 'react-router';
```

### path array
现在 path 支持不仅支持字符串也支持数组了。
```jsx
// Instead of this:
<Switch>
  <Route path="/users/:id" component={User} />
  <Route path="/profile/:id" component={User} />
</Switch>

// you can now do this:
<Route path={["/users/:id", "/profile/:id"]} component={User} />
```

### `<Link innerRef>`
支持使用`React.createRef`来创建`Link`组件的`innerRef`.
```jsx
const linkRef = React.createRef()

<Link innerRef={this.linkRef} >link</Link>
```

### Route forwardRef
`<Route component>`支持`React.forwardRef`，`forwardRef` 作用是传递父子组件的`ref`。


## 总结
总之新的功能不是很多，但是现在已经足够稳定，我现在只希望能够把`withRouter`给好好改改，这玩意太复杂了..
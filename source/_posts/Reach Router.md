---
title: Reach Router
tags: [React, Router]
category: React
date: 2019-03-19 23:07
---

# ReachRouter

## 前言

特大喜讯，特大喜讯，React-Router 5.0 即将发布！React-Router 5.0 即将发布！所以我们今天来学习一下 Reach Router（滑稽

## 介绍

Reach Router 是 React Router 的其中一位作者写的库，那么现在有这两个库摆在我面前，既然左边已经是“成熟”的 React Router 了，我们为什么还要去用后来者 Reach Router？它有什么优势？让我们随着官方文档，走近科学...不对，让我们随着官方文档，探究这些问题。

## 用法

### 渲染

``` jsx
import { React } from "react"
import { render } from "react-dom"
import { Router, Link } from "@reach/router"

let Home = () => <div>Home</div>
let Dash = () => <div>Dash</div>

render(
  <Router>
    <Home path="/" />
    <Dash path="dashboard" />
  </Router>
)
```

Reach Router 也提供了 Router 与 Link 组件，和 React Router 不同，它将直接将组件对应的组件写在 Router 组件下，而不是`<Route path="/" component={Home}>`，这么做无疑大大简化了逻辑。

再看看参数传递：

``` jsx
<Home path='/user/:id'>

const Home = ({id})=>{
    return (
        <span>{id}</span>
    )
}
```

看到这里我都要沸腾了，之前在 React 是这么的：

``` jsx
const {match: {params: { id } } } = props
```

Reach Router 的参数接收方法就好像是直接给这个组件传递了参数的 Props 一样，React Router 那种方法我已经无力吐槽了...

### 路由匹配

Reach Router 有一个策略，一个路由下只会渲染一个最符合条件的路由。

``` jsx
render(
  <Router>
    <Home path="/" />
    <Invoice path=":invoiceId" />
    <InvoiceList path="invoices" />
  </Router>
)
```

`/123` 匹配`:invoiceId`，`/invoices`匹配`invoices`，虽然匹配上了`/`,但是并不会渲染（除非路径正好是`/`），如果在 React Router 中，这种行为怕是要加 `exact`的？

### 嵌套路由

``` jsx
render(
  <Router>
    <Home path="/" />
    <Dash path="dashboard">
      <Invoices path="invoices" />
      <Team path="team" />
    </Dash>
  </Router>
)
```

（`Dash`要写`children`的情况下）如果 URL 是`/dashboard/team`，那么就会把`Team`作为`Dash`的`chlidren`渲染，如果 URL 是`/dashboard`，那么只会渲染没有`children`的`Dash`。

在子路由中使用 `Link` 跳转的话跳转的是相对路径：

``` jsx
<Dash path='dashboard'>
    <Chart path='/'>
    <About path='about'>
</Dash>
//这时候在 About 组件中用<Link to='about'>就是跳转到 `/dashboard/about`
```

从上面也可以看出如果在几个同级的路由中，第一个路由是'/'，后面的路由都可以不在开头加`/`，这个`/`就是默认的路由

### 404 路由

``` jsx
const NotFound = () => (
  <div>Sorry, nothing here.</div>
)

render(
  <Router>
    <Home path="/" />
    <Dash path="dashboard">
      <DashboardGraphs path="/" />
      <InvoiceList path="invoices" />
    </Dash>
    <NotFound default />
  </Router>
)
```

如果在给组件一个 `default` 的 `Props` 的话代表没有的话跳转至此路由。

### 嵌入式

``` jsx
render(
  <Router>
    <Home path="/" />
    <Dash path="dashboard/*" />
  </Router>
)

const Dash = () => (
  <div>
    <p>A nested router</p>
    <Router>
      <DashboardGraphs path="/" />
      <InvoiceList path="invoices" />
    </Router>
  </div>
)
```

如果用`*`就代表里面可以嵌入子路由。

### 跳转

Reach Router 提供了`navigate`来帮助跳转：

``` jsx
import { navigate } from '@reach/router'

navigate(`/about`) //绝对路径跳转
```

## 总结

想 React Router 4 出来的时候，一片吐槽，至今我感觉 React Router 的很多 API 过于复杂，有点过于追求结构性反而忽略的使用性的意思，Reach Router 使用全新的方式大大简化了各种路由的 API，核心代码只有几百行，可以说是极简了，但是简约和全面不可兼得，简化了 API 的同时也注定不能讲功能做的如同 React Router 那么全面，Google 到的信息还相对较少。

在我看来，小型应用已经完全可以使用 Reach Router 了，大型应用由于可能会有各种复杂的情况，暂时还是先用 React Router 吧，React Router v5 好像是兼容 v4 版本的，那么注定应该没有什么 break change 了？先期待吧。

---
title: React-router
tags: [React, Router]
category: React
date: 2019-01-17 22:44
---

# React-router

## 分类

- BrowserRouter URL 路径
- HashRouter 哈希路由（带#号）
- MemoryRouter 内存路由（URL 不变化，通常 SSR）

## API

### Link

普通链接，相当于`<a>`，不会触发浏览器刷新

``` jsx
import { Link } from 'react-router-dom'

<Link to="/home">Home</Link>
```

### NavLink

类似 Link 但是可以添加当前选中状态的 class

``` jsx
<NavLink to="/ai" activeClassName="ai-select">ai</NavLink>
```

### Prompt

满足条件的时候提示用户是否离开当前页面

``` jsx
import { Prompt } from 'react-router'

<Prompt
    when={inputIsFill}
    message="尚未输入"
/>
```

### Redirect

重定向当前页面

``` jsx
{isLog?<Redirect to="/home"/>:<Login />}
```

### Route

URL 匹配的时候显示对应属性
如果没有`Switch`，每个匹配的 Route 都会显示。
exact 精确匹配

``` jsx
<Route path="/book/:id" component={Book}/>

```

通过高阶组件的方式，`<Book/>`能获得一个`match`值获取相应参数。

### Switch

只显示匹配的第一个路由

## 参数

this.props.match.params
页面的状态尽量通过 URL 参数来定义而不是存储在`state`中，例如查询字符串

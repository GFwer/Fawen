---
title: 异步Action
tags: [React, Redux]
category: React
date: 2018-07-09 22:23
---

## 异步 Action

在 View 触发 Action 之后会被中间件（Middlewares）截获，会去先处理（请求 API），然后把结果 Action dispatch 到 Store

### 中间件

- 截获 action
- 发出 action

实际上 action creator 返回一个函数（普通的 creator 直接返回一个 action 对象），函数执行时先 dispatch 一个 action 表示正在执行，然后数据返回后在 dispatch action 表示完成

``` jsx
const fetchPosts() => {
	return dispatch =>{
		//执行时先返回一个action表示正在执行
		  dispatch({type:"loading"});
		  return fetch(`/some/API/${postTitle}.json`)
		    .then(response => response.json())
		    .then(json => dispatch({type:"success",data:json));
  };
	}

};
```

![Alt text](./1530023215602.png)

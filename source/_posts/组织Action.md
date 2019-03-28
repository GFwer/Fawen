---
title: 组织Action
tags: [React, Redux]
category: React
date: 2018-07-09 22:25
---

## 组织 Action

在官方示例中，所有的 action 放一个文件，所有的 reducer 放一个文件，这样会有一些问题：

1. 所有的 action 放一个文件，会无限扩展
2. action,reducer 分开，实现业务逻辑需要来回切换
3. 系统中有哪些 action 不够直观

#### 新的方式：单个 action 和 reducer 放在同一个文件

有一个总的 actions 和 reducers 文件，从各个小文件中引入各个 action 和 reducer

创建一个 actions.js 和 reducers.js 分别引入不同文件中的 action 和 reducer 并 export 集中处理

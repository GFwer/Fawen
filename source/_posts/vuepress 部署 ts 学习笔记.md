---
title: vuepress 部署 ts 学习笔记
tags: [JavaScript]
category: JavaScript
date: 2020-4-17 22:32
---
# vuepress 部署 ts 学习笔记

## 前言
在这个 Vue 3 beta 的喜庆时刻，我准备学习 TS，正好想玩玩 vuepress，就想把 TS 学习的内容另存一个文档，用 vuepress + serverless framework 建站。

## 部署
vuepress 没有一个快速开始的模板，需要手动新建/clone 项目，我选的是自己手动新建，大概项目文件如下：
```
├── docs
│   ├── README.md
│   ├── config.md
│   ├── guide
│   │   ├── README.md
│   │   └── start.md
│   └── public
│       ├── favicon.ico
│       ├── fawen.png
│       └── typescript.png
├── package.json
├── serverless.yml
└── yarn.lock

3 directories, 10 files

# fawen @ Fawen-MacBook in ~/data/typescript [22:22:03]
$ tree -a
.
├── docs
│   ├── .vuepress
│   │   ├── components
│   │   ├── config.js
│   │   ├── public
│   │   └── styles
│   │       ├── index.styl
│   │       └── palette.styl
│   ├── README.md
│   ├── config.md
│   ├── guide
│   │   ├── README.md
│   │   └── start.md
├── package.json
├── serverless.yml
```
大概就是这么一个目录结构，vuepress 更崇尚约定式的目录结构，`docs`下每个目录的`README`默认为首页，然后通过`config.js`配置相关信息，其他都和官方的差不多，我这里主要是配置`sidebar`：
```javascript
sidebar: {
  '/guide/': ['', 'start']
}
```

## Dark Mode
我还想加上 Dark Mode 支持，于是根据官方的指导新建了`index.styl`文件，由于默认是`stylus`，我只好通过 CSS Variable 重新覆写了一遍（时间都花在找颜色了，找半天，感谢 Stack Overflow...）

## 部署
写完了之后就是 Serverless Framework 相关内容了，参见<a href="https://blog.gongfangwen.com/2020/04/11/%E9%80%9A%E8%BF%87%20Serverless%20Framework%20%E9%83%A8%E7%BD%B2%E5%8D%9A%E5%AE%A2/" target="_blank">通过 Serverless Framework 部署博客</a>

## 后记
<a href="https://typescript.gongfangwen.com" target="_blank">部署完成，点击访问！</a>TS 相关内容之后会更新在这上面，就不（可能）来回搬运了
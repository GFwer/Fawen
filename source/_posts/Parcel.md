---
title: Parcel
tags: [Parcel, Plugin]
category: Plugin
date: 2018-10-26 23:01
---

# Parcel

最近使用 WebPack 开发的时候，总感觉速度变慢，而且 Config 文件也是越来越长，感觉啥都想往上加的样子（捂脸），越来越感觉，这不是我想要的，我觉得工具的本质，应该以尽量少的工作完成尽量多的功能，Webpack 确实很强大，但是繁琐的配置感觉这不是我需要的。

这时候就想起来了当时在 CodeSandBox 上看到的 Parcel，当时看到之后有稍稍的了解过一点，这次趁着这个机会用了它一段时间，感觉还不错，写篇文章记录下。

## 安装

一笔带过。

``` Shell
yarn global add parcel-bundler
```

## 真香警告

和 Webpack 不同的是，Parcel 可以使用任何类型的文件来作为 entry，之前 Webpack 我使用 JS 文件，这次我直接使用 index.html 做为入口：

```
parcel src/index.html -p 8080 --open
```

直接开始！
这时候用 Parcel 算的地方就来了：除了一些最最基本的环境配置以外，Parcel 几乎不用任何配置就能够直接打包：热更新、Less，错误提示等我的项目比较基本的一应俱全，还带自动安装依赖，那一刻我只想说：真香！
还有的是，Parcel 官方也将其打包速度作为宣传点之一，实际体验下来，速度确实不错。

## 那代价是？

万物皆有利弊，零配置和自定义必不能兼得。告别了 xx-loader，明天又会迎来什么？

### alias

我项目中用的比较多的可能就是 `alias` 了，不用繁琐的写`../../..`，就一个名字，专治 `import`，Webpack 能够在 config 中的 resolve 里面配置，那么 Parcel 呢？官方告诉你：别慌，我们还有 babel 插件!`babel-plugin-module-resolver`登场~直接在 `package.json`添加就可以.

``` js
[
    "module-resolver",
    {
      "root": [
        "./src"
      ],
      "alias": {
        "@": "./src",
        "utils": "./src/utils",
        "img": "./src/static/img"
      }
    }
  ]
```

### CSS

对于 CSS 的各种处理，人群中突然钻出一个光头（PostCSS：我来——）。
我现在使用`autoprefixer` 和 `CSS Module`，看官方的文档直接 add 安装后配置 `.postcssrc`:

``` js
{
  "modules":true,
  "plugins": {
    "autoprefixer": true,
    "postcss-modules":{
      scopeBehaviour:"local"
    }
  }
}
```

话说这个文件和 `postcss.config` 什么关系？（都是姐妹）；
不过后面打包的时候有报过 `PostCSS` 版本的问题...

### 不爽之处

吹了这么久，再来说说用的比较不爽的地方：

- 我也不晓得是不是 BUG，比如我修改某个组件的样式文件，有些时候热更新之后样式会变得和没有那些 class 一样（也许是 CSS Module 的问题？），但是刷新之后又是好的...
- 我也不晓得是不是 BUG，今天突然打包的时候突然抛了一个 error，在 issue 中查了一下发现是多个文件作为入口的时候触发的，但是我一直是单个文件作为入口啊...最关键的是...它...它过一会儿就不报了，也许是这期间我换回了 WebPack 的玄学？
- 我也不晓得是不是 BUG...

也许这就是编程玄学?有时候偶尔遇到的问题还挺影响使用的，还找不到复现的规律...看了下 Parcel 的星星数量，已经 2.5w 了，做了个表情包，表达我现在想说的话：

![parce](https://static.gongfangwen.com/parcel.png)

顺便问句：Parcel 2 啥时候来？

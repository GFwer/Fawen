---
title: Docz
tags: [Docz, Plugin]
category: Plugin
date: 2019-03-15 00:44
---

# Docz

这两天在想如何在一个 React 项目内部生成项目相关组件文档，首先想到的就是`antd`所用的`bisheng`了，下载下来研究了一下，感觉默认的主题很简陋..而且上手还是有点难度的，就放弃使用这个了，接下来还调研了下其他相关的开源库，这里着重先提下`docsify`，这个好像是国人写的，打开文档一股`Vue`风扑面而来，很喜欢，内置的 API 也是非常丰富，看了文档官方支持写`Vue`,可以插件扩展`React`，其实我是先看了`docsify`的文档的，但是最后却没有使用它，是因为另外一个库吸引了我：`Docz`。

我承认是由于`Docz`的星星数才先上手使用它的，所以简单上手之后它的特性还是震惊到了我：

- 为 React 而生，专注写 React 组件文档；
- 零配置，并且你可以在任意目录下下你的文件；
- PropsTable，根据`PropsTypes`和`defaultProps`自动生成组件`Props`文档
- ...

当然，这只是看上去很美好，真正上手之后踩的坑我也震惊了..于是写下这篇文章记录我的踩坑之路...

## 安装

实践证明，安装和基础运行上社区里的作品从来没让我失望过，直到我遇到了它...
安装没问题，但是运行上我一直报错，无法运行，不过事实证明，你遇到的问题总有人遇到，Google 一下 [issues](https://github.com/pedronauck/docz/issues/228) 到手，但是里面的解决方法...看了大半并且经过重装依赖，差不多能确定是某一个或多个依赖导致冲突了。于是，我开始用了这个最愚蠢也最聪明的做法：“二分法”删`package.json`，删除一次我`yarn`一次，最后确定了这个是以为未使用的依赖：`bisheng`（没错，就是前面说的那个，试用完之后忘了删除了..），删除依赖，重跑一次`yarn`，进入正常！

## doczrc.js

Docz 构建和打包使用的是`webpack 4`，里面自然提供了一些配置项供我们修改`webpack`配置，这个就是`doczrc.js`了。这个也是我踩最多的坑的地方了，下面听我意义到来：

### alias

说道`import`，我首先想到的不是两开花，而是`alias`，前面说道，`docz.js`提供了一些配置可以修改`webpack`对应的配置，就有这么一个函数：

```
modifyBundlerConfig: config => config
```

这个就相当于`webpack`的配置了，直接添加！完事！

### antd

首先由于是单独打包，文档里面自然不能使用`antd`，在正常使用的时候，我们使用`babel-plugin-import`来按需加载`antd`，所以在这个配置里面也需要做一下处理，
`Docz`提供这么一个函数：

```
modifyBabelRc = config => {return config}
```

这个`config`相当于默认`.babelrc`文件，这里在此之上修改一下就行了。

### less

项目使用`less`进行样式编写，`less`插件编译成`css`才能使用，`Docz`提供了`docz-plugin-css`来进行样式处理，包括`Sass`,`Less`,`Stylus`,`PostCSS`等，相当于内置了各种`loader`，并且可以使用`loaderOpts`（相当于`loader`的`options`），配上`less`，注入变量之后，结果发现`antd.css`报错，之后尝试配置`exclude`忽略相关的目录，结果还是报错..然后我一脸懵逼...

嗯，最后不知道为什么鬼使神差的添加了一个`postcss`的`loader`，竟然正常了...

### themeConfig

`themeConfig`是可以通过`Docz`内置的`<ThemeProvider>`组件获取这些配置然后使用，我使用的是`Docz`的默认主题，所以就没必要编写组件，直接按照默认的配置改就行了，不过对于`color`这块来说，默认的文档中默认值似乎少了一个`primary`的颜色，导致我配色的时候一直被一个更高优先级的样式覆盖了不能生效，后面才在`introduction`里面发现这个配置..

### assets 加载

在`Docz`及其主题配置中，有很多东西可以自定义，比如说在`htmlContext`中可以定义`favicon`图标,`head`引入样式，`indexHtml`指定基础模板等，`themeConfig.logo`中也可以配置 Logo 相关信息，这时候就遇到了个问题：怎么写资源都加载不出来，这时候我写的是相对路径，后来翻了半天 issues 才发现，`Docz`需要指定一个`public`配置，打完包以后，`public`中的内容会被自动打进`dist/public`目录中，然后在配置中引入的是相对于`public`的路径，然后我配置上以后竟然还是不好使..截止到现在都处于无法加载的情况（神奇的是，`build`之后可以加载）。

### Wrapper

这时候我突然要用到`Redux`了，看了官方的意思，是写一个新的组件，然后在配置里面配置`wrapper`的相对路径，相对路径，相对路径，单纯的我就在开始时配成了前面的`public`..太惨了...

## 写`JSX`

终于到了正文，`Docz`提供`<Playground>`组件，把其中的`chldren`转化为`JSX`代码运行，不过在最外层似乎只能使用`import`的样子，无法定义和解构，所以我就得把这些东西提取到组件内部了..

## React.memo

写着写着，突然发现`PropsTable`没有渲染，查来查去发现这个组件用了`memo`，现在`Docz`暂时还不支持，重写了一个`PureComponent`替换解决。

## 总结

总结写文档字中遇到的问题，大部分解决方法都是在 issues 中发现的，感觉现在 `Docz` 的文档（包括主题文档）还是非常不完善，很多配置和 API 在文档里面都没有写出来，不过好在 `Docz` 的 1.0 release 就快来了，里面包含了一些重大更新，比如：

- 替换插件，比如 `styled-components`替换了 `emotion`,`reach-router`替换了`react-rouer`
- Hooks
- 文档改进
- 还有一些我看不懂的高级更新

总之随着版本的迭代，`Docz`肯定还会越来越好用的！

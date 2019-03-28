---
title: ESLint
tags: [ESLint, Plugin]
category: Plugin
date: 2019-01-15 13:23
---

# ESLint

## 前言

JavaScript 是一个在非常灵活同时在开发中比较容易出错的语言，由于没有编译程序，所以 JS 代码必须在运行的时候才能检测出其中的代码错误，这时候我们就可以使用一些代码检查工具，通过一些预制其中的规则能够在编辑过程中就检测出很多代码的错误。

![-w750](https://static.gongfangwen.com/15479120743126.jpg)

## 工具对比

### JSLint

最早的检查工具，开箱即用，缺点主要是不能配置，你必须强制使用其中的某些规则。

### ESLint

今天的主角，同样是开箱即用（？其实并不），支持 JSX，内置上百条规则可以自定义，同时也可以使用 Airbnb、Google 等规则，每个人/团队都可以根据自身的特点配置符合其自身需求的规则，由于其高度可定义性，很多人也把它当做一个统一代码风格的工具。

## 使用

### 安装

ESLint 基于 Node.js 编写。

``` Shell
yarn add eslint -g
//可以根据自身需求选择是否全局安装
```

### 创建 ESLint 配置

``` Shell
eslint --init
```

这时候出现了野生的选项：1.流行配置（Airbnb、标准 和 Google）2.自定义。
ESLint 会根据不同的选择安装不同的扩展，比如 React(eslint-plugin-react)、JSX(eslint-plugin-jsx-a11y)。
同时你也可以选择在 Github 上寻找其他团队/个人意见定好的配置（eslint-config-xxx）。
完成之后会当前目录会创建`.eslintrc.js`(如果选择输出 js)文件。

### 配置

ESLint 提供了[这么多多多多的规则](https://cn.eslint.org/docs/rules/)。
在`rules`中可以针对规则做自定义配置，规则可以设置为：

- off 关闭
- warn 开启，报出警告
- error 开启，报出错误

某些配置可以传入一些 Option,按照文档说明传入即可。

### 在 VSCode 中使用

VSCode 中已经有 ESLint 插件，直接安装就可以使用，在配置中也开启`Auto Fix On Save`，可以在保存时自动使用`--fix`命令。
这时候界面上就能够报出告警信息了。

![-w428](https://static.gongfangwen.com/15479162505814.jpg)

另外，格式化也可以使用`Prettier`来格式化代码，其中也可以使用 ESLint 配置来格式化代码。

#### React 中使用

安装时已经选择使用 React 即可使用，也可以自行安装`eslint-plugin-react`。

#### Vue 中使用

在`vue-cli`中已经自带了 ESLint，同时已经安装了`eslint-plugin-vue`并且配置好了规则，安装好`Vetur`也能够进行格式化了。

## 结尾

其实自己感觉单纯看这么多规则都能收获良多..另外 ESLint 没有推荐那种写法是好的哪种写法是坏的（比如`a["b"]`和`a.b`），更多的是维护代码的一致性或者可读性，如果是非团队的情况下，没有必要非要一定要完美遵循主流的配置，在兼顾自我的代码习惯和可读性情况下，择优而行。

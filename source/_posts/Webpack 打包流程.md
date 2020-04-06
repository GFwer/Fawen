---
title: webpack 打包流程
tags: [webpack]
category: webpack
date: 2020-3-29 17:20
---
# webpack 打包流程

## 前言
webpack 是当前前端一个非常重要的打包工具，它能够影响到前端的方方面面，不过说到底还是人写的，接下来来看看 webpack 的一些核心概念和它到底是怎么构建出一个工程的吧！

## 核心概念
webpack 本质上是一个静态模块打包工具，简单来说，它会从一个入口开始（ entry ）开始，一层层读取相关依赖，构建一个依赖图（ dependency graph ），这个依赖图包含了项目中所有需要用到的模块，webpack 会构建出这些模块并生成一个或多个包（ bundle ）。

它的核心概念主要有下面内容：
### 模块（ module ）
在 webpack 中，所有文件都是模块，一个模块对应一个文件。

### 入口（ entry ）
webpack 每次构建都是从入口开始的，入口标志着构建的起点，从起点开始找出起点的依赖，再找出起点依赖的依赖，再找出依赖的依赖的依赖...直到结束，构建出一个依赖图，在进行后续操作。

在 webpack 配置中，`entry`默认值为`./src/index.js`

### 输出（ output ）
输出定义了一个 webpack 构建完成之后输出文件的规则，包括了文件目录、文件命名等。

默认输出目录是`./dist`

### loaders
由于 webpack 只能理解 JavaScript 文件和 JSON 文件，所以需要通过 loader 来让 webpack 能够处理其他类型的文件，才能使这些文件被转换为有效地模块。

一般来说，使用 loader 需要有`test`和`use`属性，它告诉 webpack，遇到什么类型的文件使用哪个 loader。

### 插件（ plugin ）
loader 只用于处理一些文件转换成模块，而 plugin 可以能够完成更多的事情如打包优化、资源管理、注入变量等，plugin 的目的就在于解决 loader 无法实现的事。

### 模式（ mode ）
一般来说使用`production`和`development`两种模式，通过配置 mode，可以试 webpack 在不用模式下执行不同的优化内容。

### 兼容性
webpack 支持所有现代浏览器（注意现代），其中使用`import`和`require.ensure()`需要`Promise`，如果不支持就需要引入 polyfill。

## 模块系统
先来说说模块化相关吧，流行的模块化规范主要有 CommonJS、AMD、CMD 和 ES6。

### CommonJS
CommonJS 主要使用`require`加载模块，下面是相关用法：
```javascript
// 定义
var fawen = 'fawen'
function getAge() {
    return 18
}

// 导出模块，定义相关变量
module.exports = {
    fawen: fawen,
    getName: getName
}

// 引用模块
var person = require('./xx.js')
console.log(person.name); // 'fawen'
```

CommonJS 主要使用同步的方式加载模块，NodeJS 是其规范的主要实践者，浏览器端由于网络原因，更适合异步方案。

### AMD

AMD（ AMD YES! ）规范就采用了异步加载，它是 require.js 在推广过程中对模块定义的规范化产出，它提倡依赖前置，提前执行，即使用模块前需要提前声明需要用到的所有模块，它可以定义回调函数，在模块加载完成之后执行：
```javascript
// 需要先引入 require.js

// 在 add.js 中定义模块
define(function() {
    var add = function(x, y) {　
        return x + y;
    };
    return {　　　　　　
        add: add
    };
});

// 在同级目录中引入模块
require(['./add'], function(addModule) {
    console.log(addModule.add(1, 1))
});
```

### CMD
CMD 是 sea.js 在推广过程中的产物，它采用和 AMD 类似的方案，不同的是，它提倡依赖就近，延迟执行，即在模块使用的时候再声明相应模块：
```javascript
// AMD
define(['a','b','c','d'],function(a, b, c, d){
    // do something
})

// CMD
define(function(require, exports, module){
    var a = require('./a')
    // do something
    if(boolean){
        var b = require('./b')
        // do something
    }
})
```

### ES6
ES6 规范提供了非常简单的模块解决方案，`export`导出模块，`import`导入模块。
```javascript
// 定义
var name = 'fawen'

export { name }

// 引入
import { name } from './name.js';
```
如果要在现代浏览器中直接使用，需要加上`module`：
```html
<script src="name.js" type="module"></script>
```

#### UMD
UMD 是 AMD（浏览器端） 和 CommonJS（ Node 端） 的耦合，它会先判断是否支持 Node.js 模块，支持就以 CommonJS 方式加载，在判断是否支持 AMD，支持就以 AMD 方式加载。

#### ES6 和 CommonJS
ES6 和 CommonJS 主要有两个差异：

- ES6 模块输出的是一个引用，如果原始的值变了，那么输出的模块也会跟着变。CommonJS 输出的是值的拷贝，原始的值变化不影响已经输出的内容。
- ES6 模块是编译时加载，CommonJS 模块是运行时加载。

> CommonJS 模块就是对象，在输入时是先加载整个模块，生成一个对象，然后再从这个对象上面读取方法。
> ES6 模块不是对象，而是通过 export 命令显式指定输出的代码，import时采用静态命令的形式。即在import时可以指定加载某个输出值，而不是加载整个模块。

## 构建流程

完整的流程如下图：

<figure class="image-bubble"><div class="img-lightbox"><div class="overlay"></div><img src="https://static.gongfangwen.com/2020-03-29-15854826405394.jpg" alt="流程"></div><div class="image-caption">流程</div></figure>


根据《深入浅出 webpack》，webpack 从开始到结束大致分成几个流程：
1. 初始化参数，从配置文件或者控制台中获取配置，得到一个最终参数集合
2. 开始编译，使用参数集合初始化`Compiler`对象，加载配置的`plugin`，开始执行编译
3. 确定入口，根据参数`entry`找出所有入口文件
4. 编译模块，从入口模块出发，调用所有的 loader 对相应模块进行处理，在找出这些模块的依赖，不断循环直到所有依赖模块都经过了处理
5. 完成模块编译，经过 loader 转换完所有的模块之后，得到了相关内容和依赖关系
6. 输出资源，根据依赖关系，把模块组合成 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输入列表，这是可以修改输出内容的最后机会。
7. 输出完成，确定好输出内容后，根据输出配置按照相应的文件名和目录把文件内容写到文件系统。

整合起来就是初始化阶段、编译阶段、输出阶段，在各个阶段中，webpack 的 compiler 实例和 compilation 实例会广播相应的<a href="https://webpack.docschina.org/api/compiler/#%E4%BA%8B%E4%BB%B6%E9%92%A9%E5%AD%90" target="_blank">事件</a>供 Plugin 使用。

compiler 和 compilation 是 webpack 最常用到的两个对象，他们各自包含了不同信息和钩子：
> Compiler 对象包含了 Webpack 环境所有的的配置信息，包含 options，loaders，plugins 这些信息，这个对象在 Webpack 启动时候被实例化，它是全局唯一的，可以简单地把它理解为 Webpack 实例；
> Compilation 对象包含了当前的模块资源、编译生成资源、变化的文件等。当 Webpack 以开发模式运行时，每当检测到一个文件变化，一次新的 Compilation 将被创建。Compilation 对象也提供了很多事件回调供插件做扩展。通过 Compilation 也能读取到 Compiler 对象。

Compiler 在整个 webpack 生命周期中只有一个，从开始到结束，而每次编译都会创建一个新的 Compilation 对象，一个 Compilation 对象就代表了一次编译。

## 按需加载
webpack 内置了对`import(*)`语句的支持：
```javascript
// main.js
element.addEventListener('click',function(event){
    import(/* webpackChunkName: "show" */ './show').then((show) => {
        show('fawen');
  })
})

// show.js
module.exports = function (content) {
  console.log('Hello ' + content);
};
```

在识别到这样的代码（ import ）的时候，webpack 会以`./show.js`为入口文件新建一个 Chunk（name 属性叫`show`，文件名根据 `output` 生成），当代码执行到 import 的时候，才会去加载 Chunk 的代码。

如果是 React，可以使用`React.lazy()`和`Suspense`组件。


## Loader

loader 相关于转换器，来看看它的结构，下面是一个简单的 loader 源码
```javascript
import { getOptions } from 'loader-utils';
// 这里可以引入任何 Node 模块或自带 API

module.exports = function(source) {
  // webpack 提供的 utils 可以获取传入的配置
  const options = getOptions(this) || {};
  // source 为 compiler 传递给 Loader 的一个文件的原内容
  // 这里可以使用 Node 模块对 source 随意处理
  // 使用正则把 // @require '../style/index.css' 转换成 require('../style/index.css'); 
  return source.replace(/(\/\/ *@require) +(('|").+('|")).*/, 'require($2);')
};
```

## Plugin
在 webpack 运行的生命周期中会广播出各种事件，Plugin 就可以监听这些事件，在触发时通过 webpack 提供的 API 改变输出结果。
在插件中，可以拿到 Compile 和 Compilation 的引用对象，使用它们广播事件，这些事件可以被其他插件监听到，或者对他们做出一定修改，其他插件拿到的也是变化的对象。

Plugin 可以做的事情非常多，一个简单的 Plugin 如下面所示：
```javascript
// webpack.config.js
const FawenPlugin = require('./FawenPlugin.js');
module.export = {
  plugins:[
    new FawenPlugin(options),
  ]
}
```

```javascript
// FawenPlugin.js
class FawenPlugin {
  // 在构造函数中获取用户给该插件传入的配置
  constructor(options){
  }

  // Webpack 会调用配置中实例的 apply 方法给插件实例传入 compiler 对象
  apply(compiler){
    // 广播一个事件
    compiler.apply('fawen',params);
    // 监听一个事件
    compiler.plugin('fawen', function(params) {
        // 事件中可以使用 webpack 提供的 API 做各种才做
    });
    /** 每个事件根据不同事件，
    *   附带的参数不同，
    *   可能是 compiler、compilation、compilationParams 或者其他乱七八糟的
    **/
  }
}

// 导出 Plugin
module.exports = FawenPlugin;
```

## 参考
- 深入浅出 webpack
- [webpack 文档](http://webpack.docschina.org/)
- [ES6 系列之模块加载方案](https://github.com/mqyqingfeng/Blog/issues/108)


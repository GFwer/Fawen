---
title: AST in JavaScript
tags: [JavaScript]
category: JavaScript
date: 2020-04-05 23:10
---
# AST in JavaScript

## 前言
之前在<a href="https://blog.gongfangwen.com/2020/03/28/JavaScript%20%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B/" target="_blank">JavaScript 执行过程</a>中聊过 AST，其实 AST 在 JavaScript 中的应用不止如此，在日常使用中，它几乎无处不在：
- Babel
- 代码高亮
- UglifyJS
- ESLint
- webpack
- .....

## 初见
AST，抽象语法树，是把源代码转换成树形的结构表示。为什么需要他呢？在将代码转化为树状结构后，代码就分成了以块块区域，其中标明了这段代码各种属性，接着或分析或遍历，一些工具就能很好分析检测代码了。

## 结构
在<a href="https://astexplorer.net/" target="_blank">astexplorer.net</a>中能够直观的查看生成的语法树。

比方说，`add(1,2)`分析成了下面内容：
```
  {
    "type": "ExpressionStatement",
    "start": 38,
    "end": 46,
    "loc": {
      "start": {
        "line": 5,
        "column": 0
      },
      "end": {
        "line": 5,
        "column": 8
      }
    },
    "expression": {
      "type": "CallExpression",
      "start": 38,
      "end": 46,
      "loc": {
        "start": {
          "line": 5,
          "column": 0
        },
        "end": {
          "line": 5,
          "column": 8
        }
      },
      "callee": {
        "type": "Identifier",
        "start": 38,
        "end": 41,
        "loc": {
          "start": {
            "line": 5,
            "column": 0
          },
          "end": {
            "line": 5,
            "column": 3
          }
        },
        "name": "add"
      },
      "arguments": [
        {
          "type": "Literal",
          "start": 42,
          "end": 43,
          "loc": {
            "start": {
              "line": 5,
              "column": 4
            },
            "end": {
              "line": 5,
              "column": 5
            }
          },
          "value": 1,
          "rawValue": 1,
          "raw": "1"
        },
        {
          "type": "Literal",
          "start": 44,
          "end": 45,
          "loc": {
            "start": {
              "line": 5,
              "column": 6
            },
            "end": {
              "line": 5,
              "column": 7
            }
          },
          "value": 2,
          "rawValue": 2,
          "raw": "2"
        }
      ]
    }
  }
```

## 利用
AST 生成之后，工具们是怎么利用它的，来看一看：

### ESLint
在 ESLint 中，可以使用 AST 选择器来查询 AST，AST 选择器类似 CSS 选择器，每个 ESLint 规则都是通过选择器操作的
```javascript
module.exports = {
  create(context) {
    // ...

    return {

      // 选择 if 语句中的块
      "IfStatement > BlockStatement": function(blockStatementNode) {
        // 判断逻辑
      },

      // 选择多于三个参数的函数定义
      "FunctionDeclaration[params.length>3]": function(functionDeclarationNode) {
        // 判断逻辑
      }
    };
  }
};
```

### Babel
Babel 对于一段代码主要工作流程大概就是：
1. 输入代码
2. 词法分析，把代码分成 Token
3. 语法分析，把 Token 转换成 AST
4. 遍历 AST
5. 改变 AST ，增删改查
6. AST 转换为源代码

下面是一个对`**`的简单处理的例子：
```javascript
module.exports = function (babel) {
  return {
    visitor: {
      BinaryExpression(path) {
        const node = path.node;
        let result;
        // 判断表达式两边，是否都是数字
        if (t.isNumericLiteral(node.left) && t.isNumericLiteral(node.right)) {
          // 根据不同的操作符作运算
          switch (node.operator) {
            case "**":
              let i = node.right.value;
              while (--i) {
                result = result || node.left.value;
                result = result * node.left.value;
              }
              break;
            default:
          }
        }
      },
    },
  };
};
```
### webpack
webpack 主要通过遍历 AST 分析出模块的依赖、消除一些无用代码等。

## 后记
事实上几乎所有需要转换 JavaScript 代码的过程中都需要先转换成 AST（TS、JSX 等），转换工具也有很多（比如 babylon），所以想要深入这些其实都先需要对 AST 做一个大概的了解。

## 参考
- [AST 抽象语法树](http://jartto.wang/2018/11/17/about-ast/)
- [ESLint](https://cn.eslint.org/)
- [babel插件入门-AST](https://juejin.im/post/5ab9f2f3f265da239b4174f0#heading-6)
---
title: EditorConfig
tags: [EditorConfig,Plugin]
category: Plugin
date: 2019-01-17 16:40
---

# EditorConfig

最开始知道 `EditorConfig` 是在使用 `Vue-cli` 生成项目之后，工程目录中一个小小的 `.editorconfig` 成功引起了我的注意（霸道总裁状）。

![-w243](https://static.gongfangwen.com/15479960598011.jpg)

然后就去查阅了相关资料，才稍微了解了解。

## What and Why

实际使用中，不同的人可能使用不同的编辑器（`VSCode`、`Atom`、`SubLime`等），它们常常会有不同的代码风格配置（即使是同一种编辑器也可能出现），[`EditorConfig`](https://editorconfig.org/)就是一个帮助开发人员进行跨编辑器同一编码样式的工具。

## How

EditorConfig 的配置文件十分易读且容易配置。

### 创建配置文件

在你编辑某个文件的时候，`EditorConfig`插件会从当前目录向上查找`.editorconfig`文件，直到找寻至项目根目录或者在配置文件中发现`root=true`为止。

### 可配置的属性

并不是所有属性都能得到编辑器的支持，有些编辑器支持的配置数量有限。以下是从 [EditorConfig 官网](https://editorconfig.org/)和 [Wiki](https://github.com/editorconfig/editorconfig/wiki/EditorConfig-Properties) 中列出的属性：

| 属性                       | 含义                     | 值                                                 |
| -------------------------- | ------------------------ | -------------------------------------------------- |
| `indent_style`             | 缩进风格                 | `tab`,`space`                                      |
| `indent_size`              | 缩进大小                 | 一个整数,`tab`                                     |
| `tab_width`                | 制表符宽度               | 一个整数，默认为`indent_size`的值                  |
| `end_of_line`              | 行末结束格式             | `lf`(Unix),`crlf`(DOS),`cr`(Windows)               |
| `charset`                  | 文件编码                 | `utf-8`,`latin1`,`utf-16be`,`utf-16le`,`utf-8-bom` |
| `trim_trailing_whitespace` | 行末尾是否允许空格       | `true`,`false`                                     |
| `insert_final_newline`     | 文件是否应以换行符结尾   | `true`,`false`                                     |
| `max_line_length`          | 指定的字符数强制强制换行 | 一个整数，`off`                                    |

### 通配符

EditorConfig 路径匹配采用通配符：

| 通配符         | 作用                                           |
| -------------- | ---------------------------------------------- |
| `*`            | 匹配任意数量 string 类型的字符，'/' 除外       |
| `**`           | 匹配任意数量 string 类型的字符                 |
| `?`            | 匹配任意单个字符                               |
| `[a-z]`        | 匹配方括号规定范围内的任意单个字符             |
| `[!a-z]`       | 匹配不在方括号规定范围内的任意单个字符         |
| `{s1,s2,s3}`   | 匹配任意一个大括号内部的字符(','分隔)          |
| `{num1..num2}` | 匹配 num1 和 num2 之间的任意一个整数，正负均可 |

### DO

这是官网的示例配置：

``` Shell
# EditorConfig is awesome: https://EditorConfig.org

# top-most EditorConfig file
root = true

# Unix-style newlines with a newline ending every file
[*]
end_of_line = lf
insert_final_newline = true

# Matches multiple files with brace expansion notation
# Set default charset
[*.{js,py}]
charset = utf-8

# 4 space indentation
[*.py]
indent_style = space
indent_size = 4

# Tab indentation (no size specified)
[Makefile]
indent_style = tab

# Indentation override for all JS under lib directory
[lib/**.js]
indent_style = space
indent_size = 2

# Matches the exact files either package.json or .travis.yml
[{package.json,.travis.yml}]
indent_style = space
indent_size = 2
```

### VSCode

VSCode 使用直接安装 [`EditorConfig for VS Code`](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig) 即可。

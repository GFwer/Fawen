---
title: 细数那些我可能用到却不熟练的 Bash 命令
tags: [Bash]
category: Bash
date: 2019-6-7 23:53
---

# 细数那些我可能用到却不熟练的 Bash 命令

## 前言
开发不免要面对命令行，掌握基本的技巧/方法无疑会大大增强生产力，本文就是细数那些我可能会用到但是不太熟悉的 Bash 命令。

本文动力是[这个](https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md)。由于我使用的是`zsh + oh-my-zsh`，一些命令可能与 `Bash` 不太相同，所以这里以实际为准。

## 键入体验
### 移动光标
- ctrl + a 移到命令最开头
- ctrl + e 移到命令最末尾
- ctrl + w 删除最后一个单词
- ctrl + u 删除整行
- ctrl + l 清屏

### 历史命令
`history`可以查看所有历史键入的命令，同时用`!+ 序号`可以快捷键入该历史命令。

### 路径
`cd -`回到上一个路径。

### 端口
通过`lsof -i:8080`查看端口占用信息。

### 传输
通过`scp`或者`rsync`传输或者同步文件。

### 查看文件
`head -n [filename/*]`查看文件开头
`grep . *`查看当前目录所有文件
`tail -n [-f] filename`[实时]查看文件末尾
`less +F filename`实时查看文件末尾

### 查找
`find . [-name 'xxx']grep`查找目录下文件


待更新..
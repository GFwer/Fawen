---
title: console
tags: [Chrome]
category: Chrome
date: 2019-09-01 00:15
---



# console 大全

## 环境 Chrome
在 Chrome 中，`console`有二十四种写法
```js
keys(console)
["debug", "error", "info", "log", "warn", "dir", "dirxml", "table", "trace", "group", "groupCollapsed", "groupEnd", "clear", "count", "countReset", "assert", "profile", "profileEnd", "time", "timeLog", "timeEnd", "timeStamp", "context", "memory"]
```

- console.log
- console.info 提示信息
- console.warn 警告信息
- console.error 错误信息
- console.debug 调试信息(Verbose 可见)
![](https://static.gongfangwen.com/2019-09-01-15672653388586.jpg)
- console.dir 输出所有属性和方法
![](https://static.gongfangwen.com/2019-09-01-15672658402917.jpg)
- console.dirxml 输出节点 html 代码
![](https://static.gongfangwen.com/2019-09-01-15672658983237.jpg)
- console.table 输出以表格形式显示
![](https://static.gongfangwen.com/2019-09-01-15672659713637.jpg)
- console.trace 显示堆栈跟踪
- console.group、console.groupCollapsed 组开始或之中，collapsed 表示默认收起
- console.groupEnd 组结束
- console.clear 清空控制台
- console.count 输入结果及被调用的次数
- console.countReset 清空计数结果
![](https://static.gongfangwen.com/2019-09-01-15672665691752.jpg)
- console.assert 如果首个参数为`false`，那么输出第一个后的错误信息参数，为`true`大家当做无事发生
![](https://static.gongfangwen.com/2019-09-01-15672668093401.jpg)
- console.profile、console.profileEnd 记录性能信息，参数为名字
![](https://static.gongfangwen.com/2019-09-01-15672670904036.jpg)
- console.time、console.timeEnd 记录时间间隔
- console.timeLog 计时中输入计时信息
![](https://static.gongfangwen.com/2019-09-01-15672674108287.jpg)
- console.timeStamp 据说是像性能检查工具里面添加一个标记，但是半天我也没找到这个标记在哪。。
- console.context ？？？
- console.memory ？？？输出内存信息？

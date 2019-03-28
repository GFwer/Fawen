---
title: Number Format
tags: [JavaScript,API]
category: JavaScript
date: 2018-07-09 22:25
---

## Number Format

### Number.prototype.toLocaleString()

这个方法返回数字在特定语言下的表示字符串

> numObj.toLocaleString([locales [,options]])


其中locales为语言标记字符串
option一些配置如下：
`style`:格式化样式。decimal（数字，默认）、currency（货币）、percent（百分比）
`currency`:货币代码。USD（美元）、CHY（人民币），无默认值
`useGrouping`：是否使用分割符，true（默认），false

| 字段                  | 含义               | 范围   | 默认                                                                                                                  |
| --------------------- | ------------------ | ------ | --------------------------------------------------------------------------------------------------------------------- |
| minimumIntegerDigits  | 整数数字的最小数目 | [1,21] | 1                                                                                                                     |
| minimumFractionDigits | 小数位数的最小数目 | [0,20] | 数字和百分比为0，货币为 [国际标准化列表](http://www.currency-iso.org/en/home/tables/table-a1.html)提供(列表没有则为2) |
| maximumFractionDigits | 小数位数的最大数目 | [0,20] | 数字max(minimumfractiondigits,3) ,货币max(minimumfractiondigits,列表),百分比max(minimumfractiondigits,0)              |

如果下面格式中有任意一个值则忽略上表的值
| 字段                     | 含义                     | 范围   | 默认                     |
| ------------------------ | ------------------------ | ------ |
| minimumSignificantDigits | 使用的有效数字的最小数目 | [1,21] | 1                        |
| maximumSignificantDigits | 使用有效数字的最大数目   | [1,21] | minimumsignificantdigits |

``` js
var number = 123456.789
console.log(number.toLocaleString('en-IN', { maximumSignificantDigits: 3 }));
//1,23,000
```

### 性能

大量数字格式化时，最好建立一个 `NumberFormat` 对象并且使用它提供的 `NumberFormat.format` 方法。


### NumberFormat

> `Intl.NumberFormat`是对语言敏感的格式化数字类的构造器类
用法
>new Intl.NumberFormat([locales[, options]])

参数和`tolocaleString`类似

``` js
var x = 1234567;
var format = new Intl.NumberFormat();
format.format(x)//1,234,567
```
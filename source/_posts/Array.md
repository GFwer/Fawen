---
title: Array 方法
tags: [JavaScript, Array]
category: JavaScript
date: 2018-12-11 15:12
---

# 前言

今天发现还有几个不太熟悉的`Array`方法，平常也有几个也没怎么用到，特此记录一下，加强记忆，顺便试试 iOS 上的 Mweb 手感如何～

# 改变自身的方法

#### array.fill(value[,start=0,end=this.length])

将数组指定区间的值都替换成对应值，start 和 value 都允许为负值（倒数）

#### array.shift()、array.pop()

shift 是删除数组中的第一个元素，pop 是删除最后一个元素，他们都会返回被删除的元素

#### array.unshift(element1,element2)、array.push(element1,element2)

unshift 是向数组开头添加元素，push 是向数组末尾添加元素，他们都会返回新数组的长度

#### array.reverse()

颠倒数组，返回新数组

#### array.sort([function(a,b)])

对数组内的元素进行排序，并返回这个数组，默认暗中 ha 哦字符串的 unicode 码进行排序；
a 与 b 是数组内的两个元素，如果

- 函数返回值小于零，那么 a 会排在 b 之前
- 函数返回值等于零，那么 a 和 b 的位置不变
- 函数返回值大于零，那么 b 会排在 a 之前

#### array.splice(start,deleteCount[,element1,element2])

删除数组中从 start 开始的 deleteCount 个元素，并在其中添加若干个元素，返回被删除的元素组成的数组

# 不改变自身的方法

#### array.concat(value1,value2)

将传入的值与原来的数组合并成一个新数组然后返回，如果其中有对象则是浅拷贝。

#### array.includes(searchElement[,fromIndex])

判断某个值是否包含在数组内，是 true 否 false。

#### array.join([separator=‘,’])

将数组中所有元素用分隔符连接成字符串。

#### array.slice(begin=0,[,end=this.length - 1])

将数组中的一部分浅拷贝存入一个新的数组中并返回。

#### array.toString()

返回一个由每个元素调用 toString()后在调用 join()方法由逗号分隔连接成的字符串

#### array.toLocaleString()

与 toString 类似，只不过调用的是 toLocaleString()方法，分隔符也可能因语言环境的不同而不同。

#### array.indexOf(searchElement[,fromIndex=0])

在数组中从前往后找到第一个 searchElement 并返回其索引，找不到则返回-1，fromIndex 可以为负值，表示（从前往后）查询范围从 0 至倒数第 n 个。匹配 searchElement 使用 ‘===’判断。

#### array.lastIndexOf(searchElement[,fromIndex=arr.length - 1])

从后往前查找元素并返回索引，找不到则返回-1.

# 遍历方法

#### array.forEach((element,index,array)=>{})

数组中的每一项都执行指定函数，forEach 范围在第一次调用函数时就会被确定，调用后往数组内添加元素并不会被访问，如果元素在调用中被改变，则传递给函数的是当 forEach 遍历到那个元素时的值。

#### array.entries()

返回一个迭代对象，这个对象包含了该数组的每一个索引及值。

``` js
let x= [“fang”,”wen”,”gong”]
let entries = x.entries()
console.log(entries.next().value) //[0,”fang”]
console.log(entries.next().value) //[1,”wen”]
```

#### array.every((element,index,array)=>{})

every 对数组中做判断，返回对所有函数返回值做 “&” 操作的布尔值，即数组中所有元素都满足条件。

#### array.some((element,index,array)=>{})

对数组中做判断，和 every 不同的是，它对返回值做 “||” 操作，表示数组中有元素满足条件

#### array.filter((element,index,array)=>{})

对每个元素做筛选，如果函数返回 true 则该元素满足条件，最后返回一个所有满足条件的元素组成的一个新数组，不会改变原数组。

#### array.find((element,index,array)=>{})

返回满足条件的第一个元素，如果没有满足条件的则返回 undefined

#### array.findIndex((element,index,array)=>{})

返回满足第一个满足条件元素的索引，如果没有则返回-1

#### array.keys()

返回一个数组索引的迭代器，类似于 entries，不过 keys 值包含索引值。

``` js
var a = [“a”,,”c”]
var key1 = Objecy.keys(a)
var key2 = [...a.keys()]
console.log(key1)//[“0”,”2”]
console.log(key2)//[0,1,2]
```

#### array.map((element,index,array)=>{})

数组中每一个元素调用函数后由每个函数的返回值组成的新数组，不改变原数组

#### array.reduce((accumulator,currentValue,currenIndex,array)=>{})

第一个参数为一个累加器，它的值为上一次返回的结果。

``` js
const a = [1,2,3,4,5]
console.log(a.reduce((acc,elem)=>acc+ elem)) //15
```

#### array.reduceRight()

和 reduce 相同，只不过方向相反。

# 其他

#### array.from(arrayLike[,mapFn])

将伪数组（拥有 length 属性和索引的任意对象）和可迭代对象（Map 和 Set 等）。
相当于 array.from(obj).map(mapFn, thisArg)。
array.from().length == 1

#### array.of(element0[,element1...])

使用参数创建一个新数组。

``` js
Array.of(5);       // [5]
Array.of(1, 2, 3); // [1, 2, 3]

Array(5);          // [ , , , , , ]
Array(1, 2, 3);    // [1, 2, 3]
```

#### array.isArray(obj)

返回一个布尔值，判断参数是否为 Array

# 参考

- [简书](https://www.jianshu.com/p/ec79c4e47370)
- MDN

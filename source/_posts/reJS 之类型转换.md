---
title: reJS 之类型转换
tags: [Javascript]
category: JavaScript
date: 2020-04-01 22:19
---
# reJS 之类型转换

> reJS 系列开坑，搞懂那些看起来简单实际上复杂的问题，努力克服眼高手低的毛病。

## 类型
在 JavaScript 中这样偏向动态类型的语言来说，类型转换通常是非常头疼的问题，几乎所有用到 JavaScript 的地方都可能存在这强制类型转换。

在 JavaScript 中，有一下几种基本类型
- null
- undefined
- object
- boolean
- string
- number
- symbol

我们通常可以用`typeof`运算符检测类型：
```javascript
typeof '1'          === 'string'
typeof 1            === 'number'
typeof undefined    === 'undefined'
typeof true         === 'boolean'
typeof {}           === 'object'
typeof Symbol()     === 'symbol'
// typeof 返回一个字符串
typeof typeof 1     === 'string'
/////例外情况
typeof null         === 'object'
```

大部分情况下`typeof`都能正常工作，除了`null`，其根源是在存储时有三位二进制数来表示类型，`object`是`000`，而`null`正好全部都是零。

值得一提的是，在 ES6 之后，由于暂时性死区，typeof 已经不是一个完全完全的操作：
```javascript
typeof a
let a
```

## 类型转换
在 JavaScript 中，几乎处处都进行这类型转换，比如下面一个例子：
```javascript
var str = 'a string';

console.log(a.length); // 'a string'

var num = 13.123;

console.log(num.toFixed(2)) // 13.12
```
对于上面这个例子来说，声明的`str`只是一个字面量，它不具有`length`属性，在执行的时候，引擎会自动把`str`转换成一个`String`对象，这才能读取其属性值，之后的`num`同理也会先被转换成`Number`对象，这种情况被称为（隐式）强制类型转换。

还有一种显式强制类型转换，比如：
```javascript
var n = 123;

var s = a + '';
```
对于变量`a`来说，有一个操作数是字符串，所以`a`会被强制转换成字符串。

### 抽象操作
ES 规范中定义了一些抽象操作，如 ToString、ToNumber、ToBoolean 等。

#### ToString
ToString 负责处理费字符串到字符串的强制转换。
基本类型的 ToString 规则如下基本不变，如果对象有自己的`toString`方法，那么就会调用：
```javascript
String(1)           === '1'
String(null)        === 'null'
String(undefined)   === 'undefined'
String(true)        === 'true'
```

##### JSON.stringify
JSON 的序列化时也使用了 ToString，大体和之前相同，不过在遇到`function`、`null`，`symbol`类型的时候会自动忽略，如果是数组中则会返回`null`。
```javascript
console.log(JSON.stringify(undefined)); // undefined
console.log(JSON.stringify(null)); // null
console.log(JSON.stringify()); // undefined
console.log(JSON.stringify(function(){})); // undefined
console.log(JSON.stringify({a:'1',b:function(){}})); // {"a":"1"}
console.log(JSON.stringify([1,undefined,function(){}])); // [1,null,null]
```


#### ToNumber

在转换之前，会先检查该值是否有`valueOf()`方法，如果有，就返回其内容，没有再执行转换。
ToNumber 遵循的规则与 ToString 相差不大，`true`时为`1`，`false`为`0`，`undefined`为`NaN`，`null`为`0`，其他处理失败的情况为`NaN`：
```javascript
var a = {
    valueOf:()=>41
}

console.log(Number(a)); // 41
console.log(Number({1:2})); // NaN
console.log(Number("")); // 0
console.log(Number([1,2,3])); // NaN
console.log(Number([])); // 0
console.log(Number(new Date())); // 1585743625540
```

#### ToBoolean
ToBoolean 大致上会将真值转换为`true`，假值转换为`false`，不过还有一些例外情况：
```javascript
console.log(Boolean(1)); // true
console.log(Boolean("false")); // true
console.log(Boolean("true")); // true
console.log(Boolean(new Boolean(false))); // true
console.log(Boolean(new String(''))); // true
console.log(Boolean(new Number(0))); // true
console.log(Boolean(function() {})); // true
```
对于“封装了真值的对象”和“封装了假值的对象”，他们，都为`true`

#### ToPrimitive
对于对象转换到基本类型（数字/字符串）都是使用了 ToPrimitive，内部逻辑使用了`toString`和`valueOf`来做对象的转换。

ToPrimitive 用法如下：
```javascript
ToPrimitive(input[, PreferredType]);
// 参数分别为需要转换的对象和期望转换的类型
// PreferredType 为空时，除非 input 为 Date 类型，否则都默认为 Number
```

ToPrimitive 的执行流程如下：
- 如果`PreferredType`为`String`，那么优先调用`toString`方法，如果返回一个基本类型，则返回结果，否则再调用`valueOf`方法。
- 如果`PreferredType`为`Number`，那么优先调用`valueOf`方法，如果返回一个基本类型，则返回结果，否则再调用`toString`方法。


例如：
```javascript
var obj = {
  valueOf: () => 123,
  toString: () => "321",
};
console.log(+obj); // 123
console.log(obj + ""); // '123'
console.log(String(obj)); // '321'

```
这个例子中，只有`String(obj)`显式指定了需要转换成 String 类型。

### 显式强制类型转换
大部分显式转换都符合逻辑，下面介绍几种比较特殊的函数/转换。

#### parseInt/parseFloat

`parseInt`和`parseFloat`都能将参数显式转换至数字，下面以`parseInt`为例：
```javascript
console.log(parseInt('123')); // 123
console.log(parseInt('123asd')); // 123
console.log(parseInt('asd123')); // NaN
console.log(parseInt('123asd1')); // 123
```
在以上处理流程中，如果参数不是字符串，`parseInt`的参数会先会转换成字符串（上例子已经都是了），然后从左到右解析，遇到非数字就停下。

### 隐式强制类型转换
相对于显式强制类型转换，隐式强制类型转换更不易察觉，不规范的写法甚至可能导致问题，所以应该尽量使用显式强制类型转换。

### + 运算符
`+`运算符常常会用到类型转换，比如：
```javascript
var a = '1';
var b = '2';
var c = 3;
var d = 4;

console.log(a + b); // '12'
console.log(a + c); // '13'
console.log(c + a); // '31'
console.log(c + d); // 7
console.log([1,2] + [3]); // '1,23'
```

从上面个可以得知，如果一个操作数是字符串，那么会将其他操作数都转化为字符串加以拼接。

### || 和 &&

`||`和`&&`是非常常用的选择操作符，他们返回的是两个操作数其中的一个：
```javascript
console.log(1 || 2); // 1
console.log(null || 2); // 2
console.log(1 && 2); // 2
console.log(null && 2); // null
```

两个运算符都会先将第一个操作数转化为`boolean`
- 对于`||`来说，如果第一个操作数为`false`就取第二个，否则取第一个
- 对于`&&`来说，如果第一个操作数为`false`，就取第一个数，否则就取第二个。

大致等同于：
```javascript
a || b = a ? a : b;
a && b = a ? b : a;
```

### == 运算符
JavaScript 还有一个特别迷惑的是`==`运算符，自从`===`出现后，`==`尽量还是少使用吧。

`==`，即宽松相等，在两个值的时候如果类型不一样，会先进行隐式类型转换。

```javascript
console.log('42' == 42); // true

```
#### 非布尔和布尔比较
在大部分时候，`==`的比较还是符合逻辑的，但是还是会有一些坑，比如：
```javascript
console.log('42' == true) // false

```

`'42'`明明是一个真值，为什么不会相等呢？这就涉及到规范问题了：
> - 如果 Type(x) 是布尔类型，那么返回 ToNumber(x) == y 的结果
> - 如果 Type(y) 是布尔类型，那么返回 y == ToNumber(x) 的结果

所以在这个例子中，不是把`'42'`转换为布尔类型比较，而是把两者都转为数字类型，`'42'`变成了`42`，`true`变成了`1`，`42 == 1`为`false`。（其实反过来也一样，`'42' == false`也为`false`）

#### undefined 和 null
在`==`中，`undefined`和`null`相当于一回事：
```true
console.log(null == undefined); // true

```

#### 对象和非对象
对象和非对象怎么比较？规范又来了，它告诉我们如果比较对象是字符串或数字，那么就把用抽象操作对象转换成数字或字符串：
```javascript
console.log('123' == Object('123')); // true
```

#### 其他特殊情况
再来看看其他那些特殊情况：
```javascript
console.log(false == 0); // true
console.log(false == ''); // true
console.log(false == []); // true
console.log(false == {}); // true
console.log([] == ![]); // ？？？true 
console.log(2 == [2]); // ？？？true
console.log("" == [null]); // ？？？ true
```

从打问号的地方开始吧，第一个，实际上`!`运算符先把操作数转换为布尔值，`![]`等于`false`，再判定`[] == false`即可。

第二个，`[2]`会先转换为字符串`[2]`变成了`2`。

第三个，`[null].toString()`结果为`""`。

尴尬了，所以在使用`==`的时候可能会有很多奇奇怪怪的坑，实际用到的时候我们应该使用全等号加以规避。

### 再来比较一下
除了等号，还有大于小于号呢？下面是相关规则：
先调用抽象关系进行转化，
- 结果有非字符串，那么双方都转换为数字进行对比
- 如果都是字符串，那么进行字符串比较

例子来了：
```javascript
console.log(['42'] < [44]); // true，转化为数字比较
console.log(['42'] < ['044']); // false，转化为字符串比较，从第一个 4 > 0 可得知 true
console.log({a : 1} < {a : 2}); // false，两个都是"[object object]"
console.log({a : 1} <= {a : 2}); // true，好像符合上面的规则
console.log({a : 1} >= {a : 2}); // true，好像符合上面的规则
console.log({a : 1} == {a : 2}); // ？？？false 
```

最后又出现了奇怪的输出，实时上，在 JavaScript 中，`<=`代表不大于，在计算`a <= b`时，相当于计算`!(a > b)`，`a > b`结果为`false`，反转其值，结果为`true`。行吧，看完我表示”奇怪的知识增加了！“

## 后记
类型转换真的坑太多了，记好`ToPrimitive`方法吧，对象转换先用它！

## 参考
- 你不知道的 JS
- [JavaScript 深入之头疼的类型转换(上) ](https://github.com/mqyqingfeng/Blog/issues/159)
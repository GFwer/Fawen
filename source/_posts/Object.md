---
title: Object
tags: [JavaScript, Object]
category: JavaScript
date: 2018-12-11 15:12
---
# Object

#### Object.assin()
浅拷贝一个对象，对于多个相同属性，以最后一个为准

#### Object.create()
创建以一个原型对象创建一个新对象，并返回该对象
```  js
const person = {
  isHuman: false,
  printIntroduction: function () {
    console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
  }
};

const me = Object.create(person);

me.name = "Matthew"; // "name" is a property set on "me", but not on "person"
me.isHuman = true; // inherited properties can be overwritten

me.printIntroduction();
```

#### Object.defineProperties(obj,props)
在一个对象上定义/修改属性并返回。
``` js
var obj = {};
Object.defineProperties(obj,{
    'prop':{
        value:'nihao',
        writable:true
    }
})
```

#### Object.defineProperty(key,prop,descriptor)
在对象上定义/修改属性并返回。
`descriptor`为属性描述符，其中有以下可选key值：
- `configurable`：是否可以修改或者删除这个属性，如果为`false`就不可修改/删除，在此方法下默认为`false`
- `enumerable`：是否可枚举，如果为`true`，则改属性可以被`for-in`遍历出来，在此方法下默认为`false`
- `writable`：是否可以修改，如果为`false`，则任何对于此属性的修改都会失效，在此方法下默认为`false`
- `value`：该属性的值，可以是任何类型，默认为`undefined`
- `get`：访问该属性时调用的方法，默认为`undefined`
- `set`：修改该属性时调用的方法，默认为`undeifned`

对于`configurable`、`enumerable`、`writable`来说，如果是直接在对象上定义的属性，这些都默认设置为`true`,而在调用`Object.defineProperty()`方法时，这些默认为`false`
``` js
var obj = {}
obj.name = "test"
Object.getOwnPropertyDescriptor(obj,'name') //{value: "test", writable: true, enumerable: true, configurable: true}

var obj2 = {}
Object.defineProperty(obj2,'name',{
    value:'test'
})
Object.getOwnPropertyDescriptor(obj2,'name')//{value: "test", writable: false, enumerable: false, configurable: false}
```
其**同时存在关系**如下(如果不能同时存在的被同时定义会抛出异常)：

|            | configurable | enumerable | value | writable | get | set |
| ---------- | ------------ | ---------- | ----- | -------- | --- | --- |
| 数据描述符 | ✔️           | ✔️         | ✔️    | ✔️       | ❌   | ❌   |
| 存取描述符 | ✔️           | ✔️         | ❌     | ❌        | ✔️  | ✔️  |

#### Object.entries(obj)
返回一个键值对数组，其顺序和`for-in`遍历顺序相同。
``` js
var obj = {name:'test1',age:'18'}
console.log(Object.entries(obj)) //[["name", "test1"] ["age", "18"]]
```

#### Object.freeze(obj)
冻结一个对象，不能修改/删除/添加属性，不能修改属性的属性描述符，返回被冻结的对象。

#### Object.fromEntries(iterable)
与`Object.entries`相反，该函数将可枚举型数组或者Map转化成Object。
注：Chrome 71、Safari 12均未有此函数，Firefox 63 有。

#### Object.getOwnPropertyDescriptor(obj, prop)
返回对象上某一个属性的属性描述符。

#### Object.getOwnPropertyDescriptors(obj)
返回对象上所有自身属性的属性描述符。
``` js
var obj = {name:'test1',age:'18'}
console.log(Object.getOwnPropertyDescriptors(obj)) //{
  "name": {
    "value": "test1",
    "writable": true,
    "enumerable": true,
    "configurable": true
  },
  "age": {
    "value": "18",
    "writable": true,
    "enumerable": true,
    "configurable": true
  }
}
```

#### Object.getOwnPropertyNames(obj)
返回一个数组，这个数组有`obj`自身拥有的枚举或不可枚举属性名称字符串组成，不会获取到原型链上的属性，可枚举顺序与`for...in`顺序相同，不可枚举顺序未定义
``` js
var arr = ["1", "2", "3"];
console.log(Object.getOwnPropertyNames(arr).sort()); // ["0", "1", "2", "length"]

var obj = { 0: "a", 1: "b", 2: "c"};
console.log(Object.getOwnPropertyNames(obj).sort()); // ["0", "1", "2"]
```

#### Object.getOwnPropertySymbols(obj)
与上面类似，返回的是所有`Symbol`属性

#### Object.getPrototypeOf(obj)
返回指定对象的原型。
``` js
var proto = {};
var obj = Object.create(proto);
Object.getPrototypeOf(obj) === proto; // true
```

#### Object.is(value1, value2)
判断 value1 与 value2 是否相同
``` js
Object.is('1',1)    // false,不做类型转换
Object.is(0, -0)    // false '=='与'==='均为true
Object.is(-0, -0)   // true  '=='与'==='均为true
Object.is(NaN, 0/0) // true
window.NaN === NaN  // false
Object.is(NaN,window.NaN) // true
```

#### Object.preventExtensions(obj)
让一个对象变的不可扩展（无法添加新属性），但是可以添加到原型。

#### Object.isExtensible(obj)
判断一个对象是否是可扩展（可添加新属性）。

#### Object.isFrozen(obj)
判断一个对象是否已经被冻结。

#### Object.seal()
封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要可写就可以改变。

#### Object.isSealed(obj)
判断一个对象是否已经被密封。

#### Object.keys(obj)
返回所有可枚举属性值组成的数组，顺序与`for...in`一致。

#### obj.hasOwnProperty(prop)
判断目标对象是否有指定属性（忽略继承的）

#### prototypeObj.isPrototypeOf(object)
检查对象是否在目标`object`的原型链上。

#### obj.propertyIsEnumerable(prop)
判断指定属性是否可以枚举。

#### Object.values()
返回可枚举属性的值。
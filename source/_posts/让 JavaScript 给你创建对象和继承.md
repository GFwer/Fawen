---
title: 让 JavaScript 给你创建对象和继承
tags: [JavaScript]
category: JavaScript
date: 2020-3-26 23:18
---
# 让 JavaScript 给你创建对象和继承
## 前言
本来这篇文章不是写对象相关的，而是写构造函数和 Class 相关的，写到一半看书，默默把写到一半的文字删了，默默改了标题，这篇博客，就决定让 JavaScript 来给我创建一个对象了！

> 本篇文章不包含 Class 的内容，旨在理解各种模式，Class 一来，各种模式没法玩了...

## ING
### 工厂模式

这是一种比较早期的创建对象的方法：
```javascript
function createPerson(name, age) {
    var o = {}
    o.name = name
    o.age = age
    o.sayName = function(){
        console.log(this.name)
    }
}

var person = createPerson('fawen', 18)
```
但是他有一个缺点就是当有很多相似的函数的时候，无法标识对象的属于什么类型。

### 构造函数模式
通过哦构造函数方法也可以较为方便地创造对象：
```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.sayName = function () {
    console.log(name);
  };
}

var person1 = new Person("fawen", 18);
var person2 = new Person("fawen2", 17);
```
首先，为了和普通函数区别开来，使用构造函数有一个需要注意的点就是**使用大写字母开头**。
接着，构造函数使用`new`操作符创建一个新对象，同时可以根据构造方法`constructor`标识一个对象：
```javascript
console.log(person1.constructor === Person); // true
console.log(person2.constructor === Person); // true

// 或者使用 instanceof 操作符
console.log(person1 instanceof Person); // true
console.log(person2 instanceof Person); // true
// 同时继承了 Object，是 Object 的实例
console.log(person1 instanceof Object); // true
```
构造函数的问题在于，每次创建新对象都要把每个方法重新创建一遍，如以上例子中创建了两次`sayName`函数，同时他们的作用都是相同的。

### 原型链模式
构造函数的问题可以通过原型链模式来解决：
```javascript
function Person() {}
Person.prototype = {
  constructor: Person,
  name: "fawen",
  age: "18",
  sayName: function () {
    console.log(this.name);
  },
};

var person1 = new Person();
var person2 = new Person();
console.log(person1.sayName()); // 'fawen'
```
通过将属性直接添加到原型上使得实例能够共享方法，这时候，所有的对象访问的都是同一个`sayName`函数了，但是这样做的问题在于，对于方法可以共享，对于属性共享就比较麻烦了，如果是引用类型的属性，可能会修改其他实例：
```javascript
function Person() {}
Person.prototype = {
  constructor: Person,
  name: "fawen",
  age: "18",
  feature: ["handsome"],
  sayName: function () {
    console.log(this.name);
  },
};

var person1 = new Person();
var person2 = new Person();
person1.feature.push("strong");
console.log(person2.feature); // ["handsome", "strong"]
```

### 结合原型的构造函数模式
既然两种都有各自的问题，我们可以把它结合起来：
```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.feature = ["handsome"];
}
Person.prototype = {
  constructor: Person,
  sayName: function () {
    console.log(this.name);
  },
};

var person1 = new Person("fawen1", 18);
var person2 = new Person("fawen2", 17);
person1.sayName(); // 'fawen1'
person1.feature.push("strong");
console.log(person2.feature); // ['handsome']
console.log(person1.sayName === person2.sayName); // true
```

可以说这种方法是目前最常用的一种创建对象的方法了，不过往下看，还有其他的方法：）

### 动态原型模式
动态原型模式和之前的区别就是在构造函数中初始化原型，同时初始化原型有一定条件：
```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.feature = ["handsome"];
  if (typeof this.sayName != "function") {
    Person.prototype.sayName = function () {
      console.log(this.name);
    };
  }
}
var person1 = new Person("fawen1", 18);
person1.sayName(); // 'fawen1'
```
这段代码只在初次调用构造函数时才会调用，同时不需要检查每个方法。
> 创建对象实例之后不能覆写整个构造函数的原型，不然会切断现有实例和原型的联系（原型指向了新的对象）

### 寄生构造函数模式
结合工厂模式和构造函数模式起来，可以这么创建对象：
```javascript
function Person(name, age){
    var o = {}
    o.name = name;
    o.age = age
    o.sayName = function(){
        console.log(this.name)
    }
    return o
}
```
这种方法其实存在挺多问题的，对象没有标识、返回对象和构造函数及其原型都没关系，同时多次创建了方法。

### 稳妥构造函数模式
```javascript
function Person(name, age){
    var o = {}
    o.sayName = function(){
        console.log(name)
    }
    return o
}
```
在这个模式之中，除了`sayName`没有其他方法可以访问`name`，因此相对安全，不过同样有之前所述的那些缺点。


## 继承
现在有对象了，该讨论下一代的事情了，谈谈继承吧！（斜眼笑
### 原型链继承
通过原型链可以比较直观地实现继承：
```javascript
function Parent() {
  this.name = "fawen";
}
Parent.prototype.sayName = function () {
  console.log(this.name);
};
function Child() {}
Child.prototype = new Parent();
var fawen = new Child();
console.log(fawen.sayName()); // 'fawen'
```
这种方法有一个问题，就是之前提到的引用类型，修改一个实例的引用类型会影响其他所有实例。

### 构造函数继承
为了解决引用类型，可以借用在子类型内部调用构造函数来实现继承：
```javascript
function Parent() {
  this.feature = ["handsome"];
}
function Child() {
  Parent.call(this);
}
var child1 = new Child();
var child2 = new Child();

child1.feature.push("strong");
console.log(child2.feature); // ["handsome", "strong"]
console.log(child2.feature); // ['handsome']
```

这种借用构造函数的方法也不是完美的，方法已经定义了，每个子类型被创建都需要调用一次。

### 组合继承
组合继承指将上面两种弄方法组合到一起实现继承，核心是方法通过原型定义，属性通过构造函数定义：
```javascript
function Person(name) {
  this.name = name;
}
Person.prototype.sayName = function () {
  console.log(this.name);
};
function Child(name, age) {
  // 这里继承属性
  Person.call(this, name);
  this.age = age;
}
// 这里继承方法
Child.prototype = new Parent();
Child.prototype.constructor = Child;
Child.prototype.sayAge = function () {
  console.log(this.age);
}
var child1 = new Child('fawen', 18);
child1.sayAge()
```

原型模式很好的结合了原型链和构造函数的优点，而避免了他们的缺点。

### 原型式继承
原型式继承指的是创建一个临时的构造函数，把对象作为原型：
```
function obj(o) {
  // 创建一个新函数，把其原型指向对象来实现继承
  function F() {}
  F.prototype = o;
  return new F();
}

var person = {
  name: "fawen",
  age: 18,
};

var person = obj(person);
console.log(person.name); // 'fawen'
```

其实这个函数就是`Object.create()`的模拟实现，不过通过这种方法同样存在引用类型的问题。

### 寄生式继承

通过创建一个封装继承过程的函数来实现继承：
```javascript
function create(o) {
  var clone = Object(o);
  clone.sayName = function () {
    console.log(this.name);
  };
  return clone;
}
```
这种方法同样的，每次创建实例都都需要创建函数。

## 寄生组合继承
回顾组合继承，每次新建一个实例都需要调用两次父类型的构造函数，所以就有了”寄生组合继承“，其本质就是在用寄生继承使 `Child.prototype`继承`Parent.prototype`，组合继承继承属性：
```javascript
function inherit(child, parent) {
  // 创建父类型原型副本，手动把其构造方法指向子类型
  var prototype = Object(parent.prototype);
  prototype.constructor = child;
  child.prototype = prototype;
}

function Parent(name) {
  this.name = name;
}
Parent.prototype.sayName = function () {
  console.log(this.name);
};

function Child(name, age) {
  Parent.call(this, name);
  this.age = age;
}
// 继承父类型方法
inherit(Child, Parent);

// 子类型增加方法
Child.prototype.sayAge = function () {
  console.log(this.age);
};

var child = new Child("fawen", 18);
child.sayName(); // 'fawen'
child.sayAge(); // 18
```

通过`inherit`方法，就可以减少一次构造函数，同时保持了高效率，是理想的继承方式。

## 后记
其实边看边写（chao）这些内容挺枯燥的，从各种模式入手肯定如此，不过能够更好的理解继承方式和 Class 等内容，现在看，ES6 真是伟大的一步！

## 参考
- JavaScript 高级程序设计
- [JavaScript深入之继承的多种方式和优缺点](https://github.com/mqyqingfeng/Blog/issues/16)
---
title: JavaScript 之设计模式
tags: [设计模式]
category: 设计模式
date: 2020-04-11 22:20
---
# JavaScript 之设计模式

## 前言
前端变化太快，总让人目不暇接，不过总有那么一块东西是永远不会过时的东西，设计模式就是其中一部分。

设计模式，是对一些普遍存在的场景被开发人员广泛采用的最佳实践。

## 工厂模式
工厂模式实际上是对对象创建过程的封装，用于创建有着共同属性的对象。
### 简单工厂
简单工厂比较无脑，比如班级里面每个人都有名字，年龄，性别等等。

```javascript
function Student(name, age, gender){
    this.name = name;
    this.age = age;
    this.gender = gender;
}

var fawen = new Student('fawen', 18, 'male')
```

这时候班级里面各种委员，学习委员、体育委员等等，每个委员要负责不同的事情，那该怎么创建学生对象呢？
可以建立一个“工厂”，判断学生类型分配工作就行了.
```javascript
function Student(name, age, gender, cadre, work) {
    this.name = name;
    this.age = age;
    this.gender = gender;
    this.cadre = cadre;
    this.work = work;
}

function Factory(name, age, gender, cadre) {
    let work
    switch (cadre) {
        case 'learning':
            work = ['收作业', '带早读']
            break;
        case 'life':
            work = ['管理班费', '购买必须品']
            break;
        // more ...
        default:
        	work = []
    }
    return new Student(name, age, gender, cadre, work)
}

// var fawen =
```
这样，一个工厂就完成了，每次创建对象只需要考虑学生担任的干部类型，不用考虑具体做什么工作。

### 抽象工厂
简单工厂很好理解，但是同时也带来了问题：每次增加一个类型就要修改工厂，类型越多，工厂越庞大，同时每次修改都要重新对整个工厂做回归测试，这太难了！

这时候，就要谈谈开放封闭原则了，所谓开放封闭，就是**对扩展开放，对修改封闭**，这样才能保持工厂的可维护性。

那什么是抽象工厂呢？抽象工厂的“抽象”值得就是先定义一些抽象的东西来约束，具体的实现应该由“具体工厂”来做，打个比方：
```javascript
class StudentFactory {
    eat() {
        throw new Error('我就是个约束')
    }
    job() {
        throw new Error('我就是个约束')
    }
}

class Student extends Person{
    eat() {
        return new 边吃边学的能力()
    }
    job() {
        return new 五年高考三年模拟()
    } 
}
```
这里的 Person 类，就是所谓的“抽象工厂”，而 Student 就是“具体工厂”，Person 不定义具体的东西，只是抽象 Student 基本要有的东西，实际上定义 Student 的时候，将方法重写就行了，这样，我们创建小学生、初中生、高中生的时候就只需要对学生工厂进行扩展，同时不用对抽象工厂做任何的修改。

### 总结
工厂模式实际上在于分离某个东西的变和不变的部分，不变的封装，变的留接口。

## 单例模式
单例单例，顾名思义，一个实例。

就像地球只有一个，单例模式下，一个类只能有一个实例，比如创建一个地球：
```javascript
class Earth {
    自转(){}
    公转(){}
    四季更替(){}
}

const planet1 = new Earth();
const planet2 = new Earth();
console.log(planet1 === planet2); // false
```
这种情况就有俩地球了，怎么办？这时候就需要对类进行处理，使得**每次创建实例都返回第一次创建的对象**。

### 解法一
解法一通过定义类里面方法：
```javascript
class Earth {
    自转(){}
    公转(){}
    四季更替(){}
    static 获取地球实例(){
        if(!Earth.instance) Earth.instance = new Earth()
        return Earth.instance
    }
}

const planet1 = Earth.获取地球实例();
const planet2 = Earth.获取地球实例();
console.log(planet1 === planet2); // true
```

### 解法二
解法二是通过闭包：
```javascript
Earth.getInstance = (function(){
    let instance = null
    return function() {
        if(!instance) instance = new Earth()
        return Earth
    }
})()
```

### 总结
单例模式应用比较广泛，如果需要创建全局唯一的对象就需要用到单例模式了，比方说建立一个全局唯一个 Modal：
```javascript
const Modal = (function () {
  let instance = null;
  return function () {
    if (!instance) {
      modal = document.createElement("div");
      modal.style.display = "none";
      document.body.appendChild(modal);
    }
    return modal;
  };
})();

const closeModal = () => {
  const modal = new Modal();
  modal.style.display = "none";
};

const createModal = () => {
  const modal = new Modal();
  modal.style.display = "block";
};
```

## 原型模式
原型模式体现就是**原型链**，这个之前有提过，见<a href="https://blog.gongfangwen.com/2020/04/03/reJS%20%E4%B9%8B%E5%8E%9F%E5%9E%8B%E9%93%BE/" target="_blank">reJS 之原型链</a>

## 装饰器模式
装饰器，听名字就可以知道，装饰器模式就是在**不改变对象基础功能的情况下，通过对其进行包装拓展，使得它能实现用户预期新的功能**。

比方说，iPod Touch 是苹果推出的一款智能设备，但是它没有蜂窝通讯功能，这时候，我大华强北推出了一款叫“苹果皮”的“手机壳”，套上之后能够使 iPod 能够打电话，这就是所谓的装饰器。
![苹果皮和 iPod](https://static.gongfangwen.com/2020-04-11-15866103292682.jpg)

装饰器模式有什么用呢？
比方说，某天，产品经理找到你，希望在圣诞节那天给特定的按钮组添加~~雪覆盖的效果~~点击后有下雪动画和圣诞快乐的字样，这时候可能就需要改组件，但是改组件可能之后又要改回来，同时对基础组件代码做修改影响范围太大，这时候改怎么办呢？添加一个装饰器：
```javascript
class Button {
  onClick() {}
}

class Decorator {
  constructor(button) {
    this.button = button;
  }
  onClick() {
    this.button.onClick();
    this.happyChristmas();
  }
  // 下雪
  snow() {}
  // 祝福
  blessing() {}
  // 合并到一起
  happyChristmas() {
    this.snow();
    this.blessing();
  }
}

const button = new Button();
const decorator = new Decorator(button);

document.querySelector("button").addEventListener("click", () => {
  decorator.onClick();
});
```

### 总结
实际上 ES7 就添加了装饰器（@decorator）的提案，`mbox`也可以使用，证明装饰器还是很有用的

## 适配器模式
适配器，打个比方，MacBook Pro 使用了超高性能的雷电 3 接口，通过它能够高速地进行各种传输，这时候，各种移动硬盘党就蒙了，你改了接口我们怎么用？苹果：你别慌，我们提供了**转换器**，通过转换器中转，老接口也能轻松连，只需要 399 就可以带回家！众人：...不亏配件大厂...

所谓适配器就是这个意思，当两种数据/函数虽然实现同样功能，但是参数、调用不兼容，这时候就可以再某一方使用适配器**抹平差异**。

`axios`，大名鼎鼎的请求神器，在底层就使用了适配器，快让我康康源码：
```javascript
var adapter = config.adapter || defaults.adapter;
```
这里定义了可以使用默认适配器和用户自定义的适配器，康康默认的怎么说：
```javascript
function getDefaultAdapter() {
  var adapter;
  if (typeof XMLHttpRequest !== 'undefined') {
    // 浏览器环境就用 xhr
    adapter = require('./adapters/xhr');
  } else if (typeof process !== 'undefined' && Object.prototype.toString.call(process) === '[object process]') {
    // node 环境就用 http
    adapter = require('./adapters/http');
  }
  return adapter;
}
```

看到这里就明白了，`axios`通过定义不同的适配器，消除了平台的差异，使参数统一直观，开发者一看就懂。
### 总结
适配器模式是为了抹平差异而存在的，在中间层定义适配器做转换工作可以有效避免差异的产生。

## 代理模式
代理，这个词特别的而熟，这不就是不能上网的时候通过其他的机器那啥么？

所谓代理模式，就是**访问某一个对象必须先经过某一个代理才能到达**

ES6 中定义了 Proxy 代理，可以很好地通过代理来拦截 getter/setter 等：
```javascript
var obj = {
  movie: {},
};

var proxy = new Proxy(obj, {
  get: function (target, key) {
    if (isVIP) return target[key];
    return new Promise((resolve) => {
      console.info("请开通百度网盘 VIP 享受极速下载");
      setTimeout(() => {
        resolve(target[key]);
      }, 999999);
    });
  },
  set: function (target, key, value) {
    if (isVIP) target[key] = value;
    else throw new Error("您的空间不足，请开通百度网盘 VIP");
  },
});

proxy.movie; // ？？
```
这就是代理模式的一个简单的例子，实际上代理模式还有很多的应用，比如：
- 函数缓存，代理函数，发现参数执行过就返回缓存结果，否则执行函数
- 事件委托，通过冒泡，将列表每一行的点击放在父节点上减少消耗
- ....

## 策略模式
举个栗子，还是百度网盘，产品经理提了个需求，普通用户给 5% 的速度，VIP 给 50% 的速度，VVIP 给 80% 的速度，代码这么写：
```javascript
function getSpeed(level) {
  if (level === "normal") {
    return 5;
  } else if (level === "vip") {
    return 50;
  } else if (level === "vvip") {
    return 80;
  }
}
```
乍一看都是 if-else ，明天产品又加了个“超级VIP”、"超级VVIP"、“免费试用加速特权”，没完没了了，说好的单一职责呢？
看看这么改：
```javascript
const speed = {
  normal: () => 5,
  vip: () => 50,
  vvip: () => 80,
};

const getSpeed = (type) => speed[type]();
```
这样就避免了`if-else`问题，同时维护了数据结构，如果以后需要再加新的类型，只需要：
```javascript
speed[type] = () => 45;
```
完全不用去动原来的对象，保证了原来对象的稳定。

### 总结
策略模式，就是88定义不同的算法，把它们封装起来，使得其可相互替换**。


## 观察者模式
详情请见<a href="https://blog.gongfangwen.com/2020/04/04/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B9%8B%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/" target="_blank">设计模式之观察者模式</a>



## 参考
- [JavaScript 设计模式核⼼原理与应⽤实践](https://juejin.im/book/5c70fc83518825428d7f9dfb/)
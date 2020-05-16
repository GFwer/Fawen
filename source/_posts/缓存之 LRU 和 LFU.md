---
title: 缓存之 LRU 和 LFU
tags: [Algorithm]
category: Algorithm
date: 2020-5-2 18:15
---
# 缓存之 LRU 和 LFU

## 前言
缓存是一种常见的方法，对于重复的计算内容缓存其结果，下次计算就不需要耗费时间，直接从缓存中取结果就行了，但是空间始终是有限的，在缓存空间到达设定值的极限的时候，意味着就要抛弃一部分内容，对于抛弃哪部分内容，不同的算法有着不同的考量，LRU 和 LFU 就是两种常见的缓存机制。


## LRU
LRU（Least Recently Used），最近最少使用，具体淘汰方法是：把缓存想象成一个有序的列表，当有一个缓存被读取的时候，这个缓存就移动到列表的头部，淘汰的时候删除最末尾的一个。

生活中常见的手机的任务列表就是一个 LRU 列表，每次访问的应用程序都会提升到列表最前面，假如要用 LRU 淘汰一个，那么就是淘汰列表最后的一项。
![任务列表](media/%E4%BB%BB%E5%8A%A1%E5%88%97%E8%A1%A8.jpeg)

要实现一个 LRU 的缓存机制，首先要通过构造函数接收一个 capacity 表示缓存列表的大小，通过构造函数新建一个对象后，对象拥有`get`和`put`方法模拟缓存的读写。

可以通过 Map 来构建缓存的列表，通过映射的方式可以很好地获取结构，由于 Map 结构部署了 Iterator 接口，通过获取 Map 所有 key 值列表可以得知 key 插入的顺序：
```javascript
// 假如未找到缓存，那么返回 -1
var LRUCache = function (capacity) {
  this.cache = new Map();
  this.maxSize = capacity;
};

LRUCache.prototype.get = function (key) {
  let res = -1;
  if (this.cache.has(key)) {
    res = this.cache.get(key);
    this.cache.delete(key);
    this.cache.set(key, res);
  }
  return res;
};

LRUCache.prototype.put = function (key, value) {
  if (this.cache.has(key)) {
    this.cache.delete(key);
  }
  this.cache.set(key, value);
  if (this.cache.size > this.maxSize) {
    // keys 返回一个有序的 Iterator 结构，包含了所有 key 值
    this.cache.delete(this.cache.keys().next().value);
  }
};
// 测试
const cache = new LRUCache(2);

cache.put(1, 1);
cache.put(2, 2);
cache.get(1); // 返回  1
cache.put(3, 3); // 该操作会使得密钥 2 作废
cache.get(2); // 返回 -1 (未找到)
cache.put(4, 4); // 该操作会使得密钥 1 作废
cache.get(1); // 返回 -1 (未找到)
cache.get(3); // 返回  3
cache.get(4); // 返回  4
```

## LFU
LFU（Least Frequently Used），即最不经常使用，根据使用的频次来淘汰相应的缓存，具体淘汰方法是：给每个数据都设定相应的频次，每次使用数据，对应频次就加一，淘汰的时候淘汰频次少的那一组数据。

同样的 LFU 也可以使用 Map 来实现，不过不同的是，由于 LFU 需要记录频次，所以增加一个 Map：
```javascript
var LFUCache = function (capacity) {
  this.cache = new Map();
  this.use = new Map();
  this.maxSize = capacity;
};

LFUCache.prototype.get = function (key) {
  let res = -1;
  if (this.cache.has(key)) {
    let count = this.use.get(key);
    res = this.cache.get(key);
    this.use.set(key, count + 1);
    // 相同频次的情况下先删除最久未使用的
    this.cache.delete(key);
    this.cache.set(key, res);
  }
  return res;
};

LFUCache.prototype.put = function (key, value) {
  if (!this.maxSize) return;
  // 先取最小频率
  const min = Math.min(...this.use.values());
  if (this.cache.has(key)) {
    this.cache.delete(key);
  }
  this.cache.set(key, value);
  if (!this.use.has(key)) {
    this.use.set(key, 1);
  } else {
    let count = this.use.get(key);
    this.use.set(key, count + 1);
  }

  if (this.cache.size > this.maxSize) {
    let list = this.cache.keys();
    let node = list.next();
    while (!node.done) {
      // 删除第一个频率相等的项
      if (this.use.get(node.value) === min) {
        this.use.delete(node.value);
        this.cache.delete(node.value);
        break;
      }
      node = list.next();
    }
  }
};
```

## 参考
- [LRU](https://leetcode-cn.com/problems/lru-cache/)
- [LFU](https://leetcode-cn.com/problems/lfu-cache/)
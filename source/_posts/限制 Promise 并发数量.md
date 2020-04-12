---
title: 限制 Promise 并发数量
tags: [JavaScript]
category: JavaScript
date: 2020-04-12 16:22
---
# 限制 Promise 并发数量

## 前言
实际场景中，我们常常会对请求并发做一些限制，比如微信小程序中`wx.request`的最大并发限制就是 10 个，那如何实现一个并发的限制呢？

## 实现
首先考虑下实现最大并发的流程：
- 请求需要先被拦截器拦截，判断是否等于限制数量
- 如果大于限制是数量，就把请求生成一个 Promise 先放进队列中
- 每个请求结束的时候都要判断队列是否为空

假设请求的 API 返回一个 Promise（当然不是的话也可以转换）：
```javascript
class Plimit {
  constructor(limit) {
    // 并发限制
    this.limit = limit;
    // 缓存队列
    this.queue = [];
    // 当前执行
    this.active = 0;
  }
  // 进入队列
  enqueue(fn) {
    return new Promise((resolve, reject) => {
      this.queue.push({ fn, resolve, reject });
    });
  }
  // 执行队列中最前面的
  dequeue() {
    if (this.limit > this.active && this.queue.length) {
      const { fn, resolve, reject } = this.queue.shift();
      this.run(fn).then(resolve).catch(reject);
    }
  }
  // 通用执行函数
  async run(fn) {
    this.active++;
    const value = await fn();
    this.active--;
    this.dequeue();
    return value;
  }

  // 拦截器判断是否超过并发限制
  interceptor(fn) {
    if (this.active < this.limit) {
      return this.run(fn);
    } else {
      return this.enqueue(fn);
    }
  }
}

// 测试时间~
Promise.all(
  [1, 2, 3, 4, 5].map((key) =>
    limit.build(
      () =>
        new Promise((resolve) => {
          setTimeout(() => {
            console.log(key);
            resolve(key);
          }, 3000);
        })
    )
  )
).then(console.log);
// 1
// 2
// 三秒后
// 3
// 4
// 大约三秒后
// 5
// [1, 2, 3, 4, 5]
```

当然，业界已经有成熟的库可以使用了，比如[p-limit](https://www.npmjs.com/package/p-limit)，思想十分类似，看看它是如何处理的：
```javascript
'use strict';
const pTry = require('p-try');

const pLimit = concurrency => {
	if (!((Number.isInteger(concurrency) || concurrency === Infinity) && concurrency > 0)) {
		return Promise.reject(new TypeError('Expected `concurrency` to be a number from 1 and up'));
	}

	const queue = [];
	let activeCount = 0;

	const next = () => {
		activeCount--;

		if (queue.length > 0) {
			queue.shift()();
		}
	};

	const run = (fn, resolve, ...args) => {
		activeCount++;

		const result = pTry(fn, ...args);

		resolve(result);
      // 完成查看队列
		result.then(next, next);
	};

	const enqueue = (fn, resolve, ...args) => {
		if (activeCount < concurrency) {
			run(fn, resolve, ...args);
		} else {
			queue.push(run.bind(null, fn, resolve, ...args));
		}
	};
   // 这里生成 promise
	const generator = (fn, ...args) => new Promise(resolve => enqueue(fn, resolve, ...args));
	// 额外方法
	Object.defineProperties(generator, {
		activeCount: {
			get: () => activeCount
		},
		pendingCount: {
			get: () => queue.length
		},
		clearQueue: {
			value: () => {
				queue.length = 0;
			}
		}
	});

	return generator;
};

module.exports = pLimit;
module.exports.default = pLimit;
```


## 参考
- [RequestDecorator](https://juejin.im/post/5b99ceb25188255c5e66ceea#comment)
- [如何对 Promise 限流：实现一个 Promise.map](https://juejin.im/post/5d4a1d30f265da03f564cbf1)
- [p-limit](https://github.com/featurist/promise-limit/blob/master/index.js)
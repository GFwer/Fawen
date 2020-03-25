---
title: 从一个 CDN 问题到前端缓存机制
tags: [Browser, Cache]
category: Other
date: 2020-3-23 21:36
---
# 从一个 CDN 问题到前端缓存机制
## 前言
上篇文章提到过，对缓存机制了解的不够透彻，加上博客没有设置哈希后缀，所以很容易就被缓存了，加上有 CDN，简直混乱了，之前出现好几次更改了样式文件结果实际访问没更新的情况，所以今天来探究一下各种缓存。

## CDN
CDN 这个还是比较熟悉的，原理不再赘述，这里拉埃讲一下缓存刷新的问题，我前几次就是被这个给坑了..
首先静态文件在 CDN 上都有缓存时间，这个时间一般是 30 天（当然也可以自己配置），在需要的时候可以自行进行缓存刷新配置，一般可以刷新 URL 和目录，这里我之前就理解错了，之前以为每次我刷新一个首页的 URL 就行了，结果刷新的是资源的 URL，导致实际上根本没刷新，这点倒是值得注意。

## 浏览器缓存
### 从缓存位置开始
浏览器拿到的缓存可能存在不同的位置，并且有各自不同的优先级，把优先级从高到低分成这些情况：
- Service Worker
- Memory Cache
- Disk Cache
- Push Cache

浏览器从上到下开始查找，找到就返回相应数据，如果都没找到则发送请求，下面一个个来看一下：

#### Service Worker
Service Worker 是一个独立于浏览器主线程的一个脚本，它能够给用户提供完全离线的体验，使用它首先必须先使用 HTTPS。
Service Worker 在注册以后，就可以自由的控制缓存，包括缓存文件，更新，删除等，如下面是来自[Google Developer](https://developers.google.com/web/fundamentals/primers/service-workers)的缓存的示例代码：
```javascript
var CACHE_NAME = 'my-site-cache-v1';
var urlsToCache = [
  '/',
  '/styles/main.css',
  '/script/main.js'
];

self.addEventListener('install', function(event) {
  // Perform install steps
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(function(cache) {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});
```
如果所有文件都缓存成功，那么 Service Worker 则可以成功安装，在安装以后可以定义一个 fetch 事件来使用这些缓存：
```javascript
self.addEventListener('fetch', function(event) {
  event.respondWith(
    caches.match(event.request)
      .then(function(response) {
        // 命中缓存，返回结果
        if (response) {
          return response;
        }
        // 没命中，请求数据，并且可以再请求以后缓存结果
        return fetch(event.request);
      }
    )
  );
});
```
> 通过 fetch 访问的资源，即使没有命中缓存，在浏览器 NetWork 中也会标注`from ServiceWorker`

#### Memory Cache
Memory Cache 即内存缓存，内存的读书速度比硬盘快上许多，所以优先级自然要高。
一般来说，资源肯定是优先存在 Memory Cache 中的，但是内存容量会小上很多，所以并不是什么东西都会往内存中存的，而且当关闭标签页之后，内存中的缓存被释放，下次请求自然不会在内存中找到了。

#### Disk Cache
Disk Cache 即硬盘你缓存，和内存缓存不同，它是持久存储的，不会随着标签页被关闭而清理。

#### Memory Cache or Disk Cache?
这时候问题就来了，那到底什么时候会用内存缓存，什么时候会用硬盘缓存？首先如果是新页面，如果有缓存，那么肯定是用到硬盘缓存的，但是如果刷新页面呢？查找了相关资料，说法各不相同，大概总结了一下，同时自己试了一下（内存小限制了我学习..），手动清理内存之后，连续刷新页面，大部分缓存都是内存缓存，做出这几种猜测：
- 如果文件较大，那么使用硬盘缓存；
- 如果内存占用较高，那么使用硬盘缓存；
- 图片优先使用内存缓存；
- 其他情况优先使用内存缓存；

同时我还发现，对于一个页面上的某个资源来说，某一次使用了内存缓存，我退出了这个标签页，新开一个标签页，这时候这个资源又是从硬盘缓存中加载了，是关闭标签的时候浏览器自动把资源放进磁盘了？还是一开始存就是存两份？又是一个值的思考的问题。

#### Push Cache
Push Cache 即推送缓存，这是 HTTP 2 中服务端主动推送的内容，这点查到的资料比较少，暂时也没办法测试，只能如下引用了：
> 它只在会话（Session）中存在，一旦会话结束就被释放，并且缓存时间也很短暂，在 Chrome 浏览器中只有 5 分钟左右，同时它也并非严格执行 HTTP 头中的缓存指令。


如果以上的缓存都没有命中，那么浏览器需要发送请求获取资源。

## HTTP 头相关
Service Worker 比较新、Memory Cache 不受 Cache-Control 控制，这里 http 头相关内容主要还是关于 Disk Cache。

### 强缓存
强缓存指的是对于某个资源不向服务器发送请求，直接从缓存中读取该资源，与强制缓存相关的字段是`Expires`和`Cache-control`。

#### Expires
Expires 表示过期时间，来自 HTTP 1.0 ，它是一个绝对时间，格式如下：
```
expires: Tue, 24 Mar 2020 06:55:29 GMT
```
expires 在响应头中，告诉浏览器，“在这个时间之前，别再找我要它了”，它的值可以如下得到：
```
expires = max-age + 请求时间
```
expires 的问题很明显，请求时间来自本地，如果修改了本地时间，那么 expires 就不符合本来指定的时间了。

#### Cache-Control
HTTP 1.1 中增加了 Cache-Control，用来控制浏览器和其他中间缓存如何缓存各个响应以及缓存多久，以下列举了几个比较常见的值：
 - `public`：该响应可以被任何对象缓存；
 - `private`：该响应只能被单个用户缓存，比如代理服务器、CDN 不能缓存；
 - `no-cache`：可以缓存，但是使用之前需要先把缓存提交给服务器对比；
 - `no-store`：不要缓存，比如用户数据，每次使用都需要像服务端请求；
 - `max-age`：表示从请求时间开始`max-age`秒之内，资源可以被重用；

使用时逗号分割：
```
cache-control: public, max-age=31536000
```
不过使用这些也是有优先级的：

<figure class="image-bubble"><div class="img-lightbox"><div class="overlay"></div><img src="https://static.gongfangwen.com/2020-03-23-1321334.png" alt="字段优先级——来自 Google Developer"></div><div class="image-caption">字段优先级——来自 Google Developer</div></figure>


一般来说，为了比较好的兼容性，Expires 和 Cache-Control 都会同时使用，Cache-Control 的优先级较高，在不支持 HTTP 1.0 的浏览器上（真的还存在吗），Expires 就会起作用。

### 协商缓存
当强缓存失效之后，浏览器携带标识像服务器发起请求，由服务器决定缓存是否可以使用，这就是协商缓存。
当强缓存失效的时候，浏览器发起请求，可能有如下结果：
- 服务器发现这些缓存可以使用，返回`304`告诉浏览器，你继续用这个缓存吧；
- 服务器发现这些缓存失效了，返回`200`和相应数据；

协商缓存可以通过`Last-Modified`和`If-Modified-Since`实现。

#### Last-Modified & If-Modified-Since
浏览器在请求资源时，服务端在响应头之中加上`Last-Modified`字段，告诉浏览器这个资源的最后修改时间：
```
last-modified: Fri, 20 Mar 2020 14:13:11 GMT
```
浏览器下一次请求这个资源的时候，会加上`If-Modified-Since`字段，值就是`Last-Modified`的值：
```
if-modified-since: Fri, 20 Mar 2020 14:13:11 GMT
```
服务器收到这个时间，和自己手上的资源最后修改时间对比，如果变了就返回新的资源和`200`，如果没变就返回`304`，浏览器直接从缓存中读取数据；

但是还是有一些缺陷：
- 由于最小时间是秒，服务器在某一秒内修改了两次文件，两次的间隔中客户端请求资源，之后客户端再请求资源，由于两次修改时间在数据上是一样的，所以依然会使用缓存。

#### Etag & If-None-Match
为了解决这个问题，又出现了`Etag`和`If-None-Match`，相对于时间，`Etag`是相对于一个资源版本的特定`hash`，从而避免了之前的问题。
客户端请求资源，服务端在响应头加上（`W/`表示使用弱验证器）：
```
etag: W/"5e608307-44c3d"
```
浏览器下次请求的时候，会在请求头加上：
```
if-none-match: W/"5e608307-44c3d"
```
之后就和`Last-Modified`一样了，当然，`Etag`还可以配合`If-Match`来防止共同编辑的防撞车问题。

优先级上，`Etag`要更高一些。


## 参考
- [深入理解浏览器的缓存机制](https://www.infoq.cn/article/8VU-VCrhoxducaFPrNOL)
- [一文读懂前端缓存](https://zhuanlan.zhihu.com/p/44789005)
- [HTTP 缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching?hl=zh-cn)
- [Cache-Control](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control)
- [ETag](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag)
- [Google Developer——Service Worker](https://developers.google.com/web/fundamentals/primers/service-workers)
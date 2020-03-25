---
title: 聊聊 Cookie 的 SameSite 属性
tags: [Browser,Cookie]
category: Browser
date: 2020-3-24 22:09
---
# 聊聊 Cookie 的 SameSite 属性

## 前言
2 月份的 Chrome 80 默认开始了 Cookie 限制，很多第三方由于页面的 Cookie 丢失产生了许多问题，今天来仔细聊聊 SameSite 这个属性和其周边吧。

## 什么是 SameSite
### Cookie
SameSite 是 Cookie 的一个属性，所谓 Cookie，就是服务端发送给浏览器的一段数据，在浏览器收到这段数据之后每次再像相同的服务端（域名或域）请求数据时会带上这段数据，服务端就会知道这个请求和之前的请求来自于统一个浏览器，Cookie 经常被用于记录用户的登录信息和自定义设置等。
> Cookie 使基于无状态的 HTTP 协议记录稳定的状态信息成为了可能。

服务端通过`Set-Cookie`响应头来向浏览器发送 Cookie：
```
Set-Cookie: <cookie名>=<cookie值>
```
浏览器以`Cookie`请求头来向服务端发送 Cookie：
```
cookie: django_language=zh-CN; dwf_sg_task_completion=False
```

### Cookie 的属性
![Cookie](https://static.gongfangwen.com/2020-03-24-15850552881937.jpg)
如上图，Cookie 是以`Name=Value;`的字符串形式存在浏览器的。
#### Expires 和 Max-age
`Expires`和`Max-age`指定了 Cookie 的生命周期。和之前提到的强缓存一样，Cookie 也有类似的属性，这里不再赘述，Expires 缺省为 Session，即 Cookie 只在当前会话中存在。

#### Domain 和 Path
`Domain`和`Path`指定了 Cookie 的作用域，它告诉浏览器，访问什么地址的时候该像服务端发送 Cookie。
Domain 设定为域名，它标识了哪些主机接受 Cookie，这个设定会**对子域名也有效**，但是如果不设定，默认为当前主机名，对**子域名不生效**。
以下两种设定均可对子级域名生效：
```
Cookie: Domain=mozilla.org
Cookie: Domain=.mozilla.org
```

Path 指定了主机下的哪些路径接受 Cookie。如果设定 Path 如下：
```
Cookie: Path=/abc
```
那么一下几种情况都会发送 Cookie：
- /abc
- /abc/eg
- /abc/eg/hj

以下几种不会发送 Cookie：
- /abcd
- /ab

#### SameSite
`SameSite`标识了一些值，它会限制在跨站的某些时候 Cookie 不会发送，可以阻止跨站请求（CSRF）。

什么是 CSRF？举个例子，你登录了淘宝之后，一些 Cookie 已经被存在了浏览器端，这时候你访问某个论坛，有用户在其中添加了如下代码：
```html
<img src="www.taobao.com/account?xxx=xxx" />
```
你打开含有这个页面的图片时，这个链接就会自动被访问。

`SameSite`有一些值可以限制 Cookie 的发送：
- `None`，不做限制；
- `Strict`，浏览器将只发送相同站点的 Cookie，即如果当前网页 URL 和请求 URL 相同时才发送；
- `Lax`，允许部分请求跨站，这个选项在新版本浏览器中为默认值，而之前的默认值为`None`。

再说说什么是跨站，和同源策略（同协议、同主机、同端口）不同，
- 不同源肯定跨站；
- 如果在[公共域名列表](https://publicsuffix.org/list/public_suffix_list.dat)中，那么与其子域名都属于跨站，如`github.io`，它在公共域名列表，所以`a.github.io`和`b.github.io`属于跨站。
- 如果不在公共域名列表中，那对子级域名没有限制，如`a.taobao.com`、`www.b.taobao.com`、`b.taobaoo.com`都属于`taobao.com`，他们依然不属于跨站。

下面还是不同的属性对于不同的请求限制：


| 请求类型    | 示例                                   | `None` | `Strict` | `Lax` |
|---------|--------------------------------------|--------|----------|-------|
| 链接      | `<a href="..."></a>`                 | 发送     | 不发送      | 发送    |
| 预加载     | `<link rel="prerender" href="..."/>` | 发送     | 不发送      | 发送    |
| Get 表单  | `<form method="GET" action="...">`   | 发送     | 不发送      | 发送    |
| Post 表单 | `<form method="POST" action="...">`  | 发送     | 不发送      | 不发送   |
| iframe  | `<iframe src="..."></iframe>`        | 发送     | 不发送      | 不发送   |
| AJAX    | `$.get("...")`                       | 发送     | 不发送      | 不发送   |
| Img     | `<img src="...">`                    | 发送     | 不发送      | 不发送   |



## 参考
- [HTTP cookies](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)
- [Cookie 的 SameSite 属性](https://juejin.im/post/5e718ecc6fb9a07cda098c2d#heading-22)
- [如何区分两个地址是同站（Same site）还是跨站（Cross site）？](https://juejin.im/post/5e74a11a6fb9a07c846b9391)
- [Cookie 的 SameSite 属性](https://www.ruanyifeng.com/blog/2019/09/cookie-samesite.html)
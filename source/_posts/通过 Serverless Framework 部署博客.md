---
title: 通过 Serverless Framework 部署博客
tags: [Serverless]
category: Serverless
date: 2020-04-11 16:43
---
# 通过 Serverless Framework 部署博客

## 什么是 Serverless

Serverless，直译就是无服务器，不过这个无服务器不是真正的无服务器，指的是开发人员不再关心底层服务器资源的事情，对于这个，不同人有不同的理解，我的理解是，原来使用服务器需要各种负载均衡、弹性扩缩容、底层资源维护，也就是不用在关心底层的建设即可快速搭建应用。

综合来说，Serverless 主要可以分成 Baas 和 Faas，其中我认为 Faas 是真正需要关心的，通过撰写函数的方式来触发一些操作，比如写数据库、请求对象存储、调用 API 等等，通过 Fass 和 Baas 层实现交互。

## Serverless Framework
Serverless Framework 实际上是一个 NPM 模块，通过命令行调用提供好的 Serverless 应用，能够触发复杂的操作，简化 Serverless 开发。

目前 <a href="https://github.com/serverless/serverless" target="_blank">Serverless Framework</a>已经狂砍 3.5 w star。

可以直接通过`npm`或者`yarn`来安装`Serverless Framework`。

## 部署
由于域名在腾讯云备案，而且好像阿里云没和 Serverless Framework 合作，所以只能测试腾讯云部署了...
我的博客是在本地由 MWeb 编写，hexo 生成的，正好腾讯云已经提供了部署静态网站的模块`@serverless/tencent-website`，所以只要编写配置文件就可以直接用了。

> 如果需要 https 需要提前申请证书
配置文件`serverless.yaml`：
```yaml
myWebsite:
  myWebsite:
  component: '@serverless/tencent-website'
  inputs:
    code:
      src: ./public
      index: index.html
      error: index.html
    region: ap-guangzhou
    bucketName: my-hexo-bucket
    protocol: https
    #  CDN 配置
    hosts:
      - host: serverless.gongfangwen.com # 希望配置的自定义域名
        https:
          certId: 124112 # SSL 证书 ID
          http2: off
          httpsType: 4
          forceSwitch: -2
```

写完直接执行`sls`，然后等待一会部署就成功了！真的太简单了，堪称一键部署，来看看它帮我们做了什么：
- 新建 CDN 域名和配置
- 新建存储桶并上传文件
- 对象存储配置 SSL、CDN 等

不过由于 dns 配置不一定在哪家服务商，所以 CDN 还是需要手动 CNAME 的。

回顾整个流程，我真正提供的，也就只有域名而已，对象存储、CDN 都有免费额度，对于博客来说完全够用了。


好了，部署完成！<a href="https://serverless.gongfangwen.com" target="_blank">点击~~访问~~套娃</a>

决定就是你了，更新命令！
```sh
hexo g -d && sls
```


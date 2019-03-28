---
title: 配置HTTPS
tags: [HTTPS,Tips]
category: Tips
date: 2018-06-26 11:20

---

叕部署好了...第N遍的部署，这次使用来Hexo部署，用了之后才发现，markdown写作还真是挺方便。

这次就先记录配一下https的过程吧，我的SSL证书是在腾讯云上申请的，大概用了十分钟证书就下发了..

然后下载证书文件，证书文件里面包含了Apache、Tomcat、Nginx等好几个文件，由于用的Nginx作为服务器就直接把这个目录底下的两个文件传到服务器删了

把这两个文件mv到`/etc/nginx/`目录下，然后编辑我的conf文件：

``` Shell
server {
        listen 443;
        server_name blog.gongfangwen.com; #域名
        ssl on;
        ssl_certificate 1_blog.gongfangwen.com_bundle.crt;
        ssl_certificate_key 2_blog.gongfangwen.com.key;
        ssl_session_timeout 5m;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #协议
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#套件
        ssl_prefer_server_ciphers on;
        location / {
            root   *******;
            index  index.html：
        }
    }
```

这是对应配置文件的说明：
| 名称                | 值  | 注释                              |
| ------------------- | --- | --------------------------------- |
| listen              | 443 | SSL访问端口号为443                |
| ssl                 | on  | 启用SSL功能                       |
| ssl_certificate     |     | 证书文件                          |
| ssl_certificate_key |     | 私钥文件                          |
| ssl_protocols       |     | 使用的协议                        |
| ssl_ciphers         |     | 配置加密套件，写法遵循openssl标准 |

配置文件配置完成后先`nginx -t`看看配置文件有没有写错，如果没有就可以`reload`了，这时候已经可以通过https:// + 域名访问了！

不过一般都会访问`http`自动重定向到`https`,这个过程可以在前端判断，也可以Nginx来做：


``` shell
# 在http 下的server加上重定向：
rewrite ^(.*) https://$host$1 permanent;
```

这样访问http也会自动重定向到`https`了!!

....

等等还没完！由于我用了腾讯云的CDN，所以还得在CDN上做一点小配置，

在CDN的控制台打开对应的域名，在域名的高级设置可以看到`https`相关的配置项，如果使用了腾讯云的SSL证书的话直接选择就完成了！

（然后我发现这里有一个强制跳转`https`的开关....不知道他俩啥区别？）

（效果还是不太好看..后续还得搞主题美化下界面..）

后续...

开启CDN之后会出现无限302的问题导致浏览器重定向过多，根据工作人员的提示把CDN`https`证书里的回源方式改成协议跟随就行啦

过了一会儿就能正常访问了，重新部署清除缓存美滋滋

后续的后续...

发现突然浏览器的小绿锁没了，一番查找才发现那个绿锁是需要所有的资源都要通过`https`访问才有的，而我的图片是从`http`域名上获取的，在存储上改成`https`的域名就行了~
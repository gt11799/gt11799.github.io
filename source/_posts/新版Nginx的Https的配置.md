title: 新版Nginx的HTTPS的配置
date: 2018-06-24 23:00:56
tags: 运维
---

最近迁移了一台服务器，Nginx配置好了HTTPS的证书之后，浏览器一直报`ERR_SSL_PROTOCOL_ERROR`的问题
<!--more-->

从nginx的一个低版本迁移到了1.10.3版本，证书是使用的阿里云的免费证书。配置好了之后用浏览器访问一直报`ERR_SSL_PROTOCOL_ERROR`的错误，在error日志里看，则显示`recv() not ready (11: Resource temporarily unavailable`

而且我基本是从我的老服务器上给抄过来的，所以按照道理来说不存在配置失败，或者证书无效的事情。

小宇帮我找到了答案。原链接在[这里](https://www.viwav.com/topic/7)

>补充说明，在配置多个https网站的时候，会报错：a duplicate default server for 0.0.0.0:443 in...
nginx版本换成1.13.8之后配置多个https网站没有问题了，不过仍然有一个站点需要在443端口后面加default，其他站点则不需要。（我是对nginx不熟，高手看到了的话可以解释一下为什么）

当有多个HTTPS网站的时候，必须在其中的一个上加default。于是我把配置改成了

```
listen 443 default ssl http2;
```

一切正常了。原链接里有说会看到 `a duplicate default server` 的错误，但是我却没有看到。难道说版本过低的原因？

另外，这个版本的nginx还有一个我没见过的坑，如果没有设置默认的server name的解析，那么如果一个server name没有配置，则会匹配到第一个server上。

比如我配置了 a.0x01.site，而 b.0x01.site 也是解析到这台服务器，即使我没有配置后者，依然会匹配到 a.0x01.site 上。查了一下，加了默认配置。

```
server {
    listen 80;
    server_name _;
    return 404;
}

server {
    listen 443;
    server_name _;
    return 404;
}
```

突然就感觉自己有点落伍了呢。

title: Python抓取网站报ssl证书验证失败的问题
date: 2019-03-31 10:44:51
tags: Python
---

最近在测试爬虫的时候，发现ZF的网站会报`certificate verify failed`的错误 <!--more-->

这是因为我国ZF的网站的证书不少都没有经过国际认证，浏览器访问没有问题，应该大家都针对性的优化了吧。

具体的报错是

>HTTPSConnectionPool(host='www.somesite.cn', port=443): Max retries exceeded with url: / (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),))

虽然走了不少弯路，但是解决起来也非常容易

```python
requests.get('https://www.somesite.cn', verify=False)
```

不验证就好了。

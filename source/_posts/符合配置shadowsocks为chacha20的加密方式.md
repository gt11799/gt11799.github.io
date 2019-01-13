title: 符合配置shadowsocks为chacha20的加密方式
date: 2019-01-13 23:10:24
tags: 运维
---

配置shadowsocks很简单，但是据说chacha20-ietf-poly1305的加密协议更快更安全一些，现在客户端基本也都支持了。

但是大龙试了没有成功，我之前换服务器的时候，也没有成功。今天用一台白纸服务器尝试，非常顺利的成功了，趁着有记忆，赶紧写下来<!--more-->

服务器是AWS的lightsail，Ubuntu 16.04，据GitHub上的讨论，14.04比较麻烦

上来首先更新下包

```
sudo apt-get update
```

然后安装pip

```
sudo apt-get install python-pip
```

然后装最新版的shadowsocks的包，当时[这个帖子](https://github.com/shadowsocks/shadowsocks/issues/1046#issuecomment-356886760)讨论的时候，shadowsocks依然不支持上述协议

```
pip install --upgrade git+https://github.com/shadowsocks/shadowsocks.git@master
```

然后需要安装系统依赖， 参考的是[这个教程](https://github.com/shadowsocks/shadowsocks/issues/1046#issuecomment-355224571), 不过我不太喜欢pip加-U命令

```
sudo apt install libsodium-dev
```

然后创建一个shadowsocks配置文件，放在/etc/shadowsocks.json, 参考的是[官方文档](https://github.com/shadowsocks/shadowsocks/wiki/Configuration-via-Config-File)

```
{
    "server":"0.0.0.0",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"chacha20-ietf-poly1305",
    "fast_open": false
}
```

然后先跑起来，然后客户端连接测试一下

```
ssserver -c /etc/shadowsocks.json
```

看看日志是否正常，如果正常，则说明配置ok，我中间遇到了`Decrypt failed`的错误，事实证明，不要相信自己。(是因为客户端密码输错了)

然后选择自己喜欢的方式来让进程以daemon运行就好，最简单的是

```
ssserver -d -c /etc/shadowsocks.json start
```

不过我没用这个，我用的是supervisor来托管的进程，可以做到进程退出自动自动，服务器启动也能自动启动。

首先安装supervisor

```
pip install supervisor
```

然后是配置supervisor

```
echo_supervisord_conf > supervisord.conf
sudo mv supervisor.conf /etc/
```

编辑supervisor文件，在最后加上以下配置(如果有别的业务要用到，最好还是分文件)

```
[program:shadowsocks]
command=ssserver -c /etc/shadowsocks.json
stdout_logfile = /tmp/shadowsocks.log
stderr_logfile = /tmp/shadowsocks_err.log
```

当然我不止做了这些，我通常会改掉supervisor.sock, supervisor.pid的位置，放到/tmp目录不会自动重启，另外，supervisord的日志文件比较重要，我通常也会改走日志路径

然后启动supervisord, 这里不要加sudo，否则日志文件容易是root权限

```
supervisord -c /etc/supervisord.conf
```

之后如果要管理进程，可以用下面的命令

```
supervisorctl status
supervisorctl restart shadowsocks
```

title: Python环境安装合集
date: 2019-07-21 23:09:58
tags: python
---

## 无法安装mysql-python的解决办法

公司的电脑按键坏了（有人要买2017款的macbook pro的记得要避坑），把自己的电脑安装公司需要的环境。

本来pip install -r requirements一行命令就能解决的事情，遇到了mysql-python。这个库是有名的难装，之前帮同事在windows下解决这个问题的时候也卡了好久。<!--more-->

执行如下命令

```
pip install mysql-python
```

会出现如下错误

```
building '_mysql' extension
creating build/temp.macosx-10.13-x86_64-2.7
clang -fno-strict-aliasing -fno-common -dynamic -g -O2 -DNDEBUG -g -fwrapv -O3 -Wall -Wstrict-prototypes -Dversion_info=(1,2,3,'final',0) -D__version__=1.2.3 -I/usr/local/Cellar/mysql/8.0.11/include/mysql -I/usr/local/Cellar/python@2/2.7.15_1/Frameworks/Python.framework/Versions/2.7/include/python2.7 -c _mysql.c -o build/temp.macosx-10.13-x86_64-2.7/_mysql.o
_mysql.c:36:10: fatal error: 'my_config.h' file not found
#include "my_config.h"
         ^~~~~~~~~~~~~
1 error generated.
error: command 'clang' failed with exit status 1
```

网上有各种各样的解决办法，最好用的是[这个办法](https://github.com/PyMySQL/mysqlclient-python/issues/131#issuecomment-338635251)

```
LDFLAGS=-L/usr/local/opt/openssl/lib pip install mysqlclient
LDFLAGS=-L/usr/local/opt/openssl/lib pip install mysql-python
```

不管装什么mysql相关的库，都可以试一下上面的这个命令

不过，最好的是直接切换到pymysql上去。

## 无法安装m2crypto

报错如下

```
SWIG/_m2crypto_wrap.c:2868:10: note: initialize the variable 'res' to silence this warning
  int res;
         ^
          = 0
SWIG/_m2crypto_wrap.c:3561:10: fatal error: 'openssl/err.h' file not found
#include <openssl/err.h>
         ^~~~~~~~~~~~~~~
1 warning and 1 error generated.
error: command 'cc' failed with exit status 1
```

[解决方案](https://stackoverflow.com/questions/33005354/trouble-installing-m2crypto-with-pip-on-os-x-macos)如下

```
brew install openssl
brew install swig
```

然后是

```
env LDFLAGS="-L$(brew --prefix openssl)/lib" \
CFLAGS="-I$(brew --prefix openssl)/include" \
SWIG_FEATURES="-cpperraswarn -includeall -I$(brew --prefix openssl)/include" \
pip install m2crypto
```

但是又报了如下错误

```
AttributeError: 'module' object has no attribute 'X509_up_ref'
WARNING: Retrying (Retry(total=1, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLError("bad handshake: Error([('SSL routines', 'ssl3_get_server_certificate', 'certificate verify failed')],)",),)': /simple/m2crypto/
```

应该是openssl的版本太低导致的

```
pip uninstall pyOpenSSL
pip install pyOpenSSL
```

然后再执行上面的命令解决了。

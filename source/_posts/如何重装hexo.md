title: 如何重装hexo
date: 2018-12-26 23:29:56
tags: 运维
---

电脑的npm给删了，于是想要重装Hexo，走了不少弯路，后来发现了方法，记下来留着以后迁移用 <!--more-->

npm重装了之后，hexo没有安装，需要安装，如果以前有过，但是现在报错，也建议重装。[原链接](https://github.com/yarnpkg/yarn/issues/1915#issuecomment-295803820)

```
sudo npm uninstall hexo-cli -g
sudo npm uninstall dtrace-provider -g
sudo npm install hexo-cli -g --no-optional
```

发现依然不可以，于是删掉生成的目录，重新生成来自[Github](https://github.com/hexojs/hexo/issues/2076#issuecomment-273472704)

```
rm -rf node_modules/ && npm install
```

如果报`Local hexo not found in`的错误，用上述方法也能解决

然后执行命令清除hexo的文件重新生成

```
hexo clean
hexo server
```

我因为引用了两个插件，会报下面的错误

```
ERROR Plugin load failed: hexo-generator-feed
Error: ENOENT: no such file or directory, open '/Users/gongting/py-develop/github/myblog/node_modules/hexo-generator-feed'
ERROR Plugin load failed: hexo-generator-sitemap
Error: ENOENT: no such file or directory, open '/Users/gongting/py-develop/github/myblog/node_modules/hexo-generator-sitemap'
```

在`_config.yml`里，注释掉这两个插件即可

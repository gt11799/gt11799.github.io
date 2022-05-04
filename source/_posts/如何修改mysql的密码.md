title: 如何修改MySQL的密码
date: 2019-03-30 23:56:12
tags: 运维
---

说来惭愧，曾经帮高老师装MySQL，以前的时候都会弹出密码输入框，但是，竟然没有弹。今天在树莓派上安装，也是没有任何提示。
<!--more-->

查了一下，使用下面的命令可以无密码登入

```bash
sudo mysql --defaults-file=/etc/mysql/debian.cnf
```

文件的位置需要自己去找。我尝试下面的命令去重置root，发现并没有用

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password'
```

提示我语法错误，懒得去整了。使用下面的命令去重置密码，发现依然无法登陆

```sql
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('new_password');
```

干脆新增了一个账户

```sql
CREATE USER 'pig'@'%' IDENTIFIED BY '123456';
GRANT ALL ON *.* TO 'pig'@'%';
FLUSH PRIVILEGES;
```

搞定。

值得一提的是，虽然每次安装同样大版本的MySQL，每一次的配置文件之类的细节，都不太一样

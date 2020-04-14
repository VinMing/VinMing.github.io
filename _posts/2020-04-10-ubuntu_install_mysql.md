---
layout:     post
title:      Ubuntu install mysql
subtitle:    "\"Ubuntu install mysql\""
date:       2020-04-10
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - ubuntu
    - mysql

---
## 环境
### 系统环境
```text
Distributor ID:	Ubuntu

Description:	Ubuntu 18.04.4 LTS

Release:	18.04

Codename:	bionic

Linux version :       5.3.0-46-generic ( buildd@lcy01-amd64-013 ) 

Gcc version:         7.5.0  ( Ubuntu 7.5.0-3ubuntu1~18.04 )
```

### 软件信息
```text
version : 	mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper
```
## 前言
这里是Ubuntu 18.04的mysql安装教程，ubuntu低版本或其他非Debian的Linux发行版可能不适用。

## 正文
### 安装mysql
```sh
sudo apt-get install mysql-server
sudo apt-get install mysql-client
sudo apt-get install libmysqlclient-dev
```

这里我安装完了没有提示设置密码或其他配置项的步骤，所以有需要的话可以看下一步更改默认密码。

### 更改默认密码
#### 查看默认配置文件
```sh
sudo cat /etc/mysql/debian.cnf
```
结果如下：
```sh
[client]
host     = localhost
user     = debian-sys-maint
password = EZuif××××××××××××
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = EZuif××××××××××××
socket   = /var/run/mysqld/mysqld.sock

```

上面有‘user=debian-sys-maint’，即为自动配置的默认用户；‘password=EZuif××××××××××××’，即为自动配置的密码。

#### 以默认配置登陆mysql
```sh
mysql -u debian-sys-maint -p        // 用户名以自己的配置文件为准
```
提示输入密码，这里要输入的就是上一步的‘password=EZuif××××××××××××’（密码以自己的配置文件为准）。

#### 更改密码
```sh
use mysql;
// 下一行，密码改为了yourpassword，可以设置成其他的
update mysql.user set authentication_string=password('yourpassword') where user='root' and Host ='localhost';
update user set plugin="mysql_native_password"; 
flush privileges;
quit;
```
#### 重启mysql
```sh
sudo service mysql restart
mysql -u root -p
```
输入新密码：yourpassword

#### OK



## 后记

@[TOC](这里写自定义目录标题)



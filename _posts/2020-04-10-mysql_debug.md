---
layout:     post
title:      Mysql debug
subtitle:    "\"MySQL debug\""
date:       2020-04-10
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Mysql
---

## 前言
收集mysql的错误信息来解决

## 环境
#### 系统环境
```text
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.4 LTS
Release:	18.04
Codename:	bionic
Linux version :       5.3.0-46-generic ( buildd@lcy01-amd64-013 ) 
Gcc version:         7.5.0  ( Ubuntu 7.5.0-3ubuntu1~18.04 )
```

#### 软件信息
```text
version : 	
     mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper
```
### debug 收集
**1** 

```sh
mysql -u root -p 
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
```

**2**

```sh
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (111)
```

**3**

```sh
$ mysql -h 127.0.0.1 -u root
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
```

## 正文
对应的问题的分析及其解决方案



### 1、mysqld.sock错误
#### 问题描述

```sh
mysql -u root -p 
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
```

#### 问题分析
这是liux套接字网络的特性，win平台不会有这个问题
#### 解决方案
在/etc/mysql/my.cnf文件追加 "[mysql] protocol=tcp"
```cnf
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
# 追加内容
[mysql]
protocol=tcp
```

#### 效果

```sh
$ mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.29-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```
### 2、ERROR 2003  Can't connect to MySQL server

#### 问题描述

```sh
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (111)
```

#### 问题分析

这是mysql服务没有正确启动（mysqld）

#### 解决方案

启动mysqld服务

```sh
$ systemctl start mysqld
```

#### 效果

```sh
$ mysql -u root -p
Enter password: 
ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (111)
$ systemctl start mysqld
$ mysql -u root -p
Enter password: 
```

  

### 3、ERROR 1045 (28000): Access denied

tobecontinue

#### 问题描述

```sh
$ mysql -h 127.0.0.1 -u root
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)
```

#### 问题分析

这个问题是密码出了问题，我们需要跳过密码验证进入mysql进行修改密码

#### 解决方案

1. 跳过密码验证进入mysql
2. 保存后重启mysql
3. 输入你安装mysql时的密码
4. 更新authentication_string密码
5. 退出并删除跳过密码验证设置
6. 重启并验证

#### 效果

## 后记

@[TOC](这里写自定义目录标题)




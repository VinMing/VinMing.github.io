---
layout:     post
title:      mysql debug
subtitle:    "\"MySQL debug\""
date:       2020-04-10
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - mysql

---
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
## 前言

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
ERROR 1698 (28000): Access denied for user 'root'@'localhost'
```

## 正文
对应的问题的分析及其解决方案
- [x] 1
### 问题分析
这是liux套接字网络的特性，win平台不会有这个问题
### 解决方案
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

### 效果

```sh
mysql -u root -p
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
- [ ] 2
- [ ] 3

## 后记

@[TOC](这里写自定义目录标题)




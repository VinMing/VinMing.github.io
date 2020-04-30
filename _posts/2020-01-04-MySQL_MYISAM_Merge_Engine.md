---
layout:     post
title:      MySQL MYISAM Merge Engine
subtitle:    "\"MySQL MYISAM Merge Engine\""
date:       2020-01-04
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - MySQL
    - MYISAM
    - Merge_Engine

---
## 前言
当需要存储的记录达到１亿级别的条数时，通常都是采用分表的结构。那怎么统计所有的分表呢？

将多个分表合并成一个总表，是利用MyISAM存储引擎基础上的Ｍerge存储引擎。

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

## 正文
这里先创建三张表，分别为test_1,test_2,test_3
```sql
CREATE TABLE `test_1` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`test_name` varchar(18) NULL ,
`test_sex` varchar(18) NULL ,
`test_birth_date` timestamp NULL ,
PRIMARY KEY (`id`)
)ENGINE = MYISAM DEFAULT CHARSET=utf8mb4;

 

CREATE TABLE `test_2` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`test_name` varchar(18) NULL ,
`test_sex` varchar(18) NULL ,
`test_birth_date` timestamp NULL ,
PRIMARY KEY (`id`)
)ENGINE = MYISAM DEFAULT CHARSET=utf8mb4;

 

CREATE TABLE `test_3` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`test_name` varchar(18) NULL ,
`test_sex` varchar(18) NULL ,
`test_birth_date` timestamp NULL ,
PRIMARY KEY (`id`)
)ENGINE = MYISAM DEFAULT CHARSET=utf8mb4;
```

插入虚拟表数据
```sql
INSERT INTO test_1 (test_name,test_sex,test_birth_date) VALUES ('曹one', '男', now());
INSERT INTO test_1 (test_name,test_sex,test_birth_date) VALUES ('曹two', '男', now());
INSERT INTO test_1 (test_name,test_sex,test_birth_date) VALUES ('曹three', '男', now());

INSERT INTO test_2 (test_name,test_sex,test_birth_date) VALUES ('王one', '女', now());
INSERT INTO test_2 (test_name,test_sex,test_birth_date) VALUES ('王two', '女', now());
INSERT INTO test_2 (test_name,test_sex,test_birth_date) VALUES ('王three', '女', now());

INSERT INTO test_3 (test_name,test_sex,test_birth_date) VALUES ('张1', '女', now());
INSERT INTO test_3 (test_name,test_sex,test_birth_date) VALUES ('张2', '女', now());
INSERT INTO test_3 (test_name,test_sex,test_birth_date) VALUES ('张3', '女', now());
```
创建　test_merge　表
```
CREATE TABLE `test_merge` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`test_name` varchar(18) NULL ,
`test_sex` varchar(18) NULL ,
`test_birth_date` timestamp NULL ,
PRIMARY KEY (`id`)
)ENGINE = MERGE UNION = (test_1,test_2,test_3);
```
查询　test_merge　表
``` sql
mysql> select * from test_merge;
+----+-----------+----------+---------------------+
| id | test_name | test_sex | test_birth_date     |
+----+-----------+----------+---------------------+
|  1 | 曹one     | 男       | 2020-04-30 14:10:38 |
|  2 | 曹two     | 男       | 2020-04-30 14:10:38 |
|  3 | 曹three   | 男       | 2020-04-30 14:10:38 |
|  1 | 王one     | 女       | 2020-04-30 14:10:38 |
|  2 | 王two     | 女       | 2020-04-30 14:10:38 |
|  3 | 王three   | 女       | 2020-04-30 14:10:38 |
|  1 | 张1       | 女       | 2020-04-30 14:10:38 |
|  2 | 张2       | 女       | 2020-04-30 14:10:38 |
|  3 | 张3       | 女       | 2020-04-30 14:10:38 |
+----+-----------+----------+---------------------+
9 rows in set (0.00 sec)
```
再添加一张　test_4　表
```sql
CREATE TABLE `test_4` (
`id` int(11) NOT NULL AUTO_INCREMENT ,
`test_name` varchar(18) NULL ,
`test_sex` varchar(18) NULL ,
`test_birth_date` timestamp NULL ,
PRIMARY KEY (`id`)
)ENGINE = MYISAM DEFAULT CHARSET=utf8mb4;

INSERT INTO test_4 (test_name,test_sex,test_birth_date) VALUES ('叶one', '女', now());
INSERT INTO test_4 (test_name,test_sex,test_birth_date) VALUES ('叶two', '女', now());
INSERT INTO test_4 (test_name,test_sex,test_birth_date) VALUES ('叶three', '女', now());

ALTER TABLE test_merge UNION = (test_1,test_2,test_3,test_4);
```
再查询　test_merge　表
``` sql
mysql> select * from test_merge;
+----+-----------+----------+---------------------+
| id | test_name | test_sex | test_birth_date     |
+----+-----------+----------+---------------------+
|  1 | 曹one     | 男       | 2020-04-30 14:10:38 |
|  2 | 曹two     | 男       | 2020-04-30 14:10:38 |
|  3 | 曹three   | 男       | 2020-04-30 14:10:38 |
|  1 | 王one     | 女       | 2020-04-30 14:10:38 |
|  2 | 王two     | 女       | 2020-04-30 14:10:38 |
|  3 | 王three   | 女       | 2020-04-30 14:10:38 |
|  1 | 张1       | 女       | 2020-04-30 14:10:38 |
|  2 | 张2       | 女       | 2020-04-30 14:10:38 |
|  3 | 张3       | 女       | 2020-04-30 14:10:38 |
|  1 | 叶one     | 女       | 2020-04-30 14:12:35 |
|  2 | 叶two     | 女       | 2020-04-30 14:12:35 |
|  3 | 叶three   | 女       | 2020-04-30 14:12:35 |
+----+-----------+----------+---------------------+
12 rows in set (0.00 sec)
```
两次前后查询　test_merge　表对比可得，已经将四个表合并到一张表中

然后我们只需查询test_merge表，即可得到我们想要的结果

## 后记
- MERGE存储引擎只能和MYISAM配合使用，也就是test_1,test_2,test_3,test_4必须指定ENGINE = MYISAM，否则查询test_merge会报错
- test_merge表和分表字段必须完全一致




---
layout:     post
title:      MySQL ON DUPLICATE KEY UPDATE with PK or UNIQUE
subtitle:    "\"MySQL ON DUPLICATE KEY UPDATE with PK or UNIQUE\""
date:       2020-04-30
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - MySQL
    - Sim-MERGE

---
## 前言
mysql 并没有merge into的DML(数据操纵语言)的关键字来合并两张表，但是有个on duplicate key update语法（不是标准的sql语法，是mysql 特有的语法）或者replace into可以实现merge into语法。

两种的方法都需要原始数据表具有唯一性索引或者主键，不然插入合并插入新表所有行。

如果你觉得merge有点眼熟，好像merge是mysql存储引擎，那你就请移步到另外一篇文章，[MySQL MERGE 存储引擎](https://www.cnblogs.com/hjw-zq/p/9804001.html)

首先声明：ON DUPLICATE KEY UPDATE 为MySQL特有语法；

语句的作用：当insert已经存在的记录时，执行update

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
创建表结构和数据
```sql
-- 创建测试表
-- drop table test_dup_old
create table test_dup_old(
id varchar(16),
name varchar(16),
operatime TIMESTAMP,
PRIMARY KEY (`id`) USING BTREE
)


-- 插入 test_dup_old 模拟数据
INSERT INTO test_dup_old values(1,"wujian",now()),(2,"xiay",now()),(3,"wangj",now());
```

#### 实验一： 单独更新一行数据
test_dup_old 表中有三条数据如下：
```sql
mysql> SELECT * FROM test_dup_old;
+----+--------+---------------------+
| id | name   | operatime           |
+----+--------+---------------------+
| 1  | wujian | 2020-04-29 09:46:52 |
| 2  | xiay   | 2020-04-29 09:46:52 |
| 3  | wangj  | 2020-04-29 09:46:52 |
+----+--------+---------------------+
3 rows in set (0.00 sec)
```

表中的主键为ID，现要插入一条数据，ID为3，name为修改test，operatime为now，结果为：
```mysql
mysql> INSERT INTO test_dup_old values(3,"test",now());
ERROR 1062 (23000): Duplicate entry '3' for key 'PRIMARY'
```
MySQL告诉我们，我们的主键冲突了，看到这里我们是不是可以改变一下思路，当插入已存在主键的记录时，将插入操作变为修改：
在原SQL后面增加 ON DUPLICATE KEY UPDATE 和动态的传入要修改的值（VALUES 后面条件）
```mysql
mysql> INSERT INTO test_dup_old values(3,"test",now()) ON DUPLICATE KEY UPDATE name=VALUES(name), operatime=VALUES(operatime);
Query OK, 2 rows affected (0.00 sec)
```
成功的修改了两行记录，刷新一下表.(我只是更新一条记录，这里提示为什么是两条呢？)
```mysql
mysql> SELECT * FROM test_dup_old;
+----+--------+---------------------+
| id | name   | operatime           |
+----+--------+---------------------+
| 1  | wujian | 2020-04-29 09:46:52 |
| 2  | xiay   | 2020-04-29 09:46:52 |
| 3  | test   | 2020-04-29 10:19:17 |
+----+--------+---------------------+
3 rows in set (0.00 sec)
```
**但是，这是需要注意的是：**

**选择写入的字段必须包含所有not null的字段，即非空的字段，如果少写了非空字段，则会报错！！**

**比如，只想修改name字段，但是operatime字段也必须写上，如果不想修改operatime字段，可以写入原来的值。**

```mysql 
mysql> INSERT INTO test_dup_old VALUES(3,'no operatime') on duplicate key update name=values(name);
ERROR 1136 (21S01): Column count doesn't match value count at row 1
```
虽然我们想要实现一个批量修改的操作，但是实则**首先执行的是插入语句，当插入语句(INSERT INTO)执行过程中发现已有重复的主键时才会执行更新(UPDATE)语句，所以非空字段(NOT NULL)一定要写全！**



#### 实验二： 合并两张表，更新所有字段
新创一张一样数据表结构不同表名并插入数据
```mysql
-- 复制 test_dup 的表结构到 test_dup_new
CREATE TABLE test_dup_new LIKE test_dup_old
-- 插入 test_dup_new 模拟数据
INSERT INTO test_dup_new values(1," xyr",now()),(2,"sy",now()),(5,"wsj",now());
```
查看两张表的数据情况
```
mysql> select * from test_dup_new;
+----+------+---------------------+
| id | name | operatime           |
+----+------+---------------------+
| 1  |  xyr | 2020-04-28 19:57:24 |
| 2  | sy   | 2020-04-28 19:57:24 |
| 5  | wsj  | 2020-04-28 19:57:24 |
+----+------+---------------------+
3 rows in set (0.00 sec)

mysql> select * from test_dup_old;
+----+--------+---------------------+
| id | name   | operatime           |
+----+--------+---------------------+
| 1  | wujian | 2020-04-29 09:46:52 |
| 2  | xiay   | 2020-04-29 09:46:52 |
| 3  | test   | 2020-04-29 10:22:07 |
+----+--------+---------------------+
3 rows in set (0.00 sec)

```
将表 test_dup_new 更新到表 test_dup_old 中去， 根据id存在就更新，不存在就插入
```mysql
mysql> INSERT INTO test_dup_old(id,name, operatime) SELECT * FROM test_dup_new ON DUPLICATE KEY UPDATE id=values(id);
Query OK, 1 row affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM test_dup_old;
+----+--------+---------------------+
| id | name   | operatime           |
+----+--------+---------------------+
| 1  | wujian | 2020-04-29 09:46:52 |
| 2  | xiay   | 2020-04-29 09:46:52 |
| 3  | test   | 2020-04-29 10:22:07 |
| 5  | wsj    | 2020-04-28 19:57:24 |
+----+--------+---------------------+
4 rows in set (0.00 sec)
```

## 后记
注意：id字段是主键或UNIQUE索引，不然只会插入dupnew表所有行数据
```sql
mysql> ALTER TABLE test_dup_old DROP PRIMARY KEY;
Query OK, 4 rows affected (0.08 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM test_dup_old;
+----+--------+---------------------+
| id | name   | operatime           |
+----+--------+---------------------+
| 1  | wujian | 2020-04-29 09:46:52 |
| 2  | xiay   | 2020-04-29 09:46:52 |
| 3  | test   | 2020-04-29 10:22:07 |
| 5  | wsj    | 2020-04-28 19:57:24 |
+----+--------+---------------------+
4 rows in set (0.00 sec)

mysql> SELECT * FROM test_dup_new;
+----+------+---------------------+
| id | name | operatime           |
+----+------+---------------------+
| 1  |  xyr | 2020-04-28 19:57:24 |
| 2  | sy   | 2020-04-28 19:57:24 |
| 5  | wsj  | 2020-04-28 19:57:24 |
+----+------+---------------------+
3 rows in set (0.01 sec)

mysql> INSERT INTO test_dup_old(id,name, operatime) SELECT * FROM test_dup_new ON DUPLICATE KEY UPDATE id=values(id);
Query OK, 3 rows affected (0.01 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM test_dup_old;    

+----+--------+---------------------+
| id | name   | operatime           |
+----+--------+---------------------+
| 1  | wujian | 2020-04-29 09:46:52 |
| 2  | xiay   | 2020-04-29 09:46:52 |
| 3  | test   | 2020-04-29 10:22:07 |
| 5  | wsj    | 2020-04-28 19:57:24 |
| 1  |  xyr   | 2020-04-28 19:57:24 |
| 2  | sy     | 2020-04-28 19:57:24 |
| 5  | wsj    | 2020-04-28 19:57:24 |
+----+--------+---------------------+
7 rows in set (0.00 sec)
```

好奇的我提取了一个问题，那没有主键又不会插入全部行的写法吗？

有！！！
[MySQL merge with no PK or UNIQUE](https://vinming.github.io/2020/04/30/MySQL_merge_with_no_PK_UNIQUE/)



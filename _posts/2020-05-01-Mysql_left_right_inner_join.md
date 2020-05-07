---
layout:     post
title:      MySQL left join,right join,inner join diff
subtitle:    "\"MySQL left join,right join和inner join的区别\""
date:       2020-05-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    -MySQL
    -left join
    -right join
    -inner join
---
## 前言

之前被问到mysql中的三种join的区别，一时蒙了。

```sql
left join  - 左连接
right join - 右连接
inner join - 内连接
```

经过以下文档说明，简单的概况成一句话：使用左右连接的两张表可以理解成其中一张表的信息明显比另外一张表信息重要得多，使用内连接的两张表可以理解成重要程度区别不大的两张表。

注意：“重要”的标准要对于查询结果的相对而言

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
这里先创建两张表，分表为A和B
```sql
CREATE TABLE `A` (
`id` int(11),
`name` varchar(18) NULL
);

CREATE TABLE `B` (
`id` int(11),
`count` int(10)
);
```
插入数据
```sql
INSERT INTO A VALUES (01, 'abc');
INSERT INTO A VALUES (02, 'ab');

INSERT INTO B VALUES (01, 7);
INSERT INTO B VALUES (03, 9);
```
查询两个表结果
```sql
mysql> SELECT * FROM A;
+------+-------+
| id   | name  |
+------+-------+
|    1 | abc   |
|    2 | ab    |
|    4 | abccc |
+------+-------+
3 rows in set (0.00 sec)

mysql> SELECT * FROM B;
+------+-------+
| id   | count |
+------+-------+
|    1 |     7 |
|    3 |     9 |
+------+-------+
2 rows in set (0.00 sec)
```
### 左右连接
#### left join（左连接）
返回包括左表的所有记录和右表中连接字段(即两表的id字段)相等的记录
```sql
mysql> SELECT A.id,A.name, B.count FROM A LEFT JOIN B on A.id = B.id;
+------+-------+-------+
| id   | name  | count |
+------+-------+-------+
|    1 | abc   |     7 |
|    2 | ab    |  NULL |
|    4 | abccc |  NULL |
+------+-------+-------+
3 rows in set (0.00 sec)
```
关注点在A表的id和name
#### right join（右连接）
返回包括右表中的所有记录和左表中连接字段相等的记录
```sql
mysql> SELECT A.id,A.name, B.count FROM A RIGHT JOIN B on A.id = B.id;
+------+------+-------+
| id   | name | count |
+------+------+-------+
|    1 | abc  |     7 |
| NULL | NULL |     9 |
+------+------+-------+
2 rows in set (0.00 sec)
```
关注点在B表的count
#### 左右连接适用场景
如：员工表中有个字段是详细地址信息表的主键id，这两个表相关联时，就可以用左连接或右连接，因为在详细地址信息表中找不到某员工的地址信息也要将员工这条记录显示出来，相应的详细地址信息字段为空即可，而不能因为地址没有存在数据库里，这个员工就没了（简单理解成不重要的信息不影响整条记录的显示）

#### inner join(等值连接)
只返回两个表中连接字段相等的记录
```sql
mysql> SELECT A.id,A.name, B.count FROM A inner join B on A.id = B.id;
+------+------+-------+
| id   | name | count |
+------+------+-------+
|    1 | abc  |     7 |
+------+------+-------+
1 row in set (0.00 sec)
```
等价写法
```sql
SELECT A.id,A.name, B.count 
FROM A,B 
on A.id = B.id
```
#### 内连接适用场景
相连接的两个表中必须在某个字段上有相等的值才可以将整条记录显示出来，如一条服务单记录在了两个表中，A表中记录了该服务单的服务时间、坐席名称和录音地址等基本信息，B表中记录了该服务单的业务详情，如保险单号，车牌号，保单日期等，当显示该服务单时，要将A表与B表做内连接，因为少这两表任何一个表，该服务单都不算完整，缺失的信息会使业务上没法继续。
### 总结
经过以上文档说明，简单的概况成一句话：**使用左右连接的两张表可以理解成其中一张表的信息明显比另外一张表信息重要得多**，**使用内连接的两张表可以理解成重要程度区别不大的两张表**。
## 后记
弱弱补一下[Mysql的查询语句执行顺序]()
## 参考
[SQL LEFT JOIN 关键字](https://www.w3school.com.cn/sql/sql_join_left.asp)
[SQL RIGHT JOIN 关键字](https://www.w3school.com.cn/sql/sql_join_right.asp)
[SQL INNER JOIN 关键字](https://www.w3school.com.cn/sql/sql_join_inner.asp)

@[TOC](这里写自定义目录标题)



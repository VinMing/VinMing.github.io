---
layout:     post
title:     MySQL Merge With No Primary Key or unique
subtitle:    "\"MySQL Merge With No Primary Key or unique\""
date:       2020-04-30
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - MySQL
    - MySQL function

---
## 前言

1.replace into和insert into on duplicate key update，都需要原始数据表具有唯一性索引。

2.合并两张表，最简便还是使用如上语句（要有唯一性索引），如果不想创建唯一性索引，则可以通过存储过程实现。

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

```mysql

-- 创建测试表
-- drop table test_a;
create  table test_a(
id VARCHAR (16),
name VARCHAR (16),
Operatime datetime
)
-- drop table test_b;
create table test_b(
id VARCHAR (16),
name VARCHAR (16),
Operatime datetime
)
 
-- 插入模拟数据
INSERT into test_b values(1,"1",now()),(2,"2",now());
INSERT into test_a values(1,"1",now()),(3,"3",now());
 
-- 查询数据
SELECT * FROM test_b;
SELECT * FROM test_a
```
设计 merge_a_to_b 函数
```mysql
CREATE DEFINER=`root`@`localhost` PROCEDURE `merge_a_to_b`()
BEGIN
-- 定义需要插入从a表插入b表的过程变量
DECLARE _ID VARCHAR (16);
DECLARE _NAME VARCHAR (16);
-- 游标遍历数据结束标志 
DECLARE done INT DEFAULT FALSE;
-- 游标指向a表结果集第一条-1位置
DECLARE cur_account CURSOR FOR SELECT ID, NAME FROM test_a;
-- 游标指向a表结果集最后一条加1位置 设置结束标志
DECLARE CONTINUE HANDLER FOR NOT FOUND  SET done = TRUE;
-- 打开游标
OPEN cur_account;
-- 遍历游标
read_loop :
LOOP
--  取值a表当前位置数据到临时变量
	FETCH NEXT FROM cur_account INTO _ID,_NAME;
 
-- 如果取值结束 跳出循环
IF done THEN LEAVE read_loop; 
END IF;
 
-- 当前数据做 对比 如果b表存在则更新时间 不存在则插入
IF NOT EXISTS ( SELECT 1 FROM test_b WHERE ID = _ID AND NAME=_NAME ) 
	THEN
		INSERT INTO test_b (ID, NAME,operatime) VALUES (_ID,_NAME,now());
	ELSE 
	UPDATE test_b  set operatime = now() WHERE ID = _ID AND NAME=_NAME;
END IF;
 
END LOOP;
CLOSE cur_account;
 
END
```

### 验证

原始数据情况

```mysql
mysql> SELECT * FROM test_b;
+------+------+---------------------+
| id   | name | Operatime           |
+------+------+---------------------+
| 1    | 1    | 2020-04-29 11:42:12 |
| 2    | 2    | 2020-04-29 11:42:12 |
+------+------+---------------------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM test_a;
+------+------+---------------------+
| id   | name | Operatime           |
+------+------+---------------------+
| 1    | 1    | 2020-04-29 11:42:12 |
| 3    | 3    | 2020-04-29 11:42:12 |
+------+------+---------------------+
2 rows in set (0.00 sec)
```

call merge_a_to_b 函数： 合并表a 和表b
```

mysql> call merge_a_to_b();
Query OK, 0 rows affected (0.02 sec)

mysql> SELECT * FROM test_b;
+------+------+---------------------+
| id   | name | Operatime           |
+------+------+---------------------+
| 1    | 1    | 2020-04-29 13:39:59 |
| 2    | 2    | 2020-04-29 11:42:12 |
| 3    | 3    | 2020-04-29 13:39:59 |
+------+------+---------------------+
3 rows in set (0.00 sec)

```




## 后记

@[TOC](这里写自定义目录标题)



---
layout:     post
title:      MySQL explain
subtitle:    "\"MySQL explain\""
date:       2020-04-30
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - MySQL
    - explain

---
## 前言
explain关键字可以模拟MySQL优化器执行SQL语句，可以很好的分析SQL语句或表结构的性能瓶颈。

使用过explain关键字优化过2,576,177行，1.02 GB (1,093,664,768)数据长度的数据，找到查询的痛点，改动表的字段属性，数据库表索引，表中内容（｜－,）查询时间从原来的15.290s到改进之后的2.64s

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
     
```

## 正文
#### 创建SQL语句和虚拟数据
```sql
-- 学科表
DROP TABLE IF EXISTS `subject`;
create table subject(
id int(10) auto_increment,
name varchar(20),
teacher_id int(10),
primary key (id),
index idx_teacher_id (teacher_id));

-- 教师表
DROP TABLE IF EXISTS `teacher`;
create table teacher(
id int(10) auto_increment,
name varchar(20),
teacher_no varchar(20),
primary key (id),
unique index unx_teacher_no (teacher_no(20)));

-- 学生表
DROP TABLE IF EXISTS `student`;
create table student(
id int(10) auto_increment,
name varchar(20),
student_no varchar(20),
primary key (id),
 unique index unx_student_no (student_no(20)));

-- 学生成绩表
DROP TABLE IF EXISTS `student_score`;
create table student_score(
id int(10) auto_increment,
student_id int(10),
subject_id int(10),
score int(10),
primary key (id),
index idx_student_id (student_id),
index idx_subject_id (subject_id));

-- 教师表增加名字普通索引
 alter table teacher add index idx_name(name(20));

-- 数据填充：
 insert into student(name,student_no) values ('zhangsan','20200001'),('lisi','20200002'),('yan','20200003'),('dede','20200004');
 
 insert into teacher(name,teacher_no) values('wangsi','T2010001'),('sunsi','T2010002'),('jiangsi','T2010003'),('zhousi','T2010004');
 
 insert into subject(name,teacher_id) values('math',1),('Chinese',2),('English',3),('history',4);
 
insert into student_score(student_id,subject_id,score) values(1,1,90),(1,2,60),(1,3,80),(1,4,100),(2,4,60),(2,3,50),(2,2,80),(2,1,90),(3,1,90),(3,4,100),(4,1,40),(4,2,80),(4,3,80),(4,5,100);
```
#### explain的执行效果　12个字段
```sql
mysql> explain select * from subject where id = 1 \G
-- 注 \G 将打横表格变成打竖表格
*************************** 1. row ***************************
           id: 1       -- 表示查询中执行select子句或操作表的顺序，3种情况
  select_type: SIMPLE  -- 查询类型，６种情况
        table: subject -- 访问哪个表
   partitions: NULL    -- 匹配分区
         type: const   -- 访问类型　一共13种，常见10种
possible_keys: PRIMARY -- 可能应用在这张表中的索引
          key: PRIMARY -- 实际使用到的索引
      key_len: 4       -- 索引中使用的字节数，在不损失精确度的情况下，长度越短越好
          ref: const　 -- 索引的哪一列被使用
         rows: 1       -- 行数
     filtered: 100.00  -- 查询的表行占表的百分比
        Extra: NULL    -- 包含不适合在其它列中显示但十分重要的额外信息，8种情况
1 row in set, 1 warning (0.00 sec)
```
#### 单独分析含有多种组合的４个字段
##### id字段（３种情况）
1. id相同

   执行顺序从上至下。

   ```sql
   mysql> select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
   +----+---------+------------+
   | id | name    | teacher_id |
   +----+---------+------------+
   |  1 | math    |          1 |
   |  1 | math    |          1 |
   |  1 | math    |          1 |
   |  1 | math    |          1 |
   |  2 | Chinese |          2 |
   |  2 | Chinese |          2 |
   |  2 | Chinese |          2 |
   |  2 | Chinese |          2 |
   |  3 | English |          3 |
   |  3 | English |          3 |
   |  4 | history |          4 |
   |  4 | history |          4 |
   |  4 | history |          4 |
   |  4 | history |          4 |
   +----+---------+------------+
   14 rows in set (0.02 sec)
   
   mysql> explain select subject.* from subject,student_score,teacher where subject.id = student_id and subject.teacher_id = teacher.id;
   +----+-------------+---------------+------------+-------+------------------------+----------------+---------+-------------------------+------+----------+----------------------------------------------------+
   | id | select_type | table         | partitions | type  | possible_keys          | key            | key_len | ref                     | rows | filtered | Extra                                              |
   +----+-------------+---------------+------------+-------+------------------------+----------------+---------+-------------------------+------+----------+----------------------------------------------------+
   |  1 | SIMPLE      | teacher       | NULL       | index | PRIMARY                | unx_teacher_no | 83      | NULL                    |    4 |   100.00 | Using index                                        |
   |  1 | SIMPLE      | subject       | NULL       | ALL   | PRIMARY,idx_teacher_id | NULL           | NULL    | NULL                    |    4 |    25.00 | Using where; Using join buffer (Block Nested Loop) |
   |  1 | SIMPLE      | student_score | NULL       | ref   | idx_student_id         | idx_student_id | 5       | spider_tmall.subject.id |    3 |   100.00 | Using index                                        |
   +----+-------------+---------------+------------+-------+------------------------+----------------+---------+-------------------------+------+----------+----------------------------------------------------+
   3 rows in set, 1 warning (0.00 sec)
   
   ```

2. id不同

   子查询，id 的序号递增，id的值越大优先级越高，优先被执行。

   ```sql
   
   ```

3. id相同又不同

   id如果相同，可以认为是一组，从上往下顺序执行，在所有组中，id值越大，优先级越高，越先执行。

   ```
   
   ```

##### select_tpye字段(６种情况)
1. SIMPLE

   简单查询，不包含子查询或者Union查询。

   ```sql
   
   ```

   

2. PRIMARY

   查询中包含任何复杂的子部分,最外层查询则被标记为主查询。

3. SUBQUERY

   在select 或者 where 中包含子查询

4. DERIVED

   在FROM列表中包含的子查询被标记为DERIVED(衍生),MySQL会递归执行这些子查询，把结果放到临时表中。
   备注：MySQL 5.7 + 进行了优化，增加了derived_merge（派生合并）,默认开启，可加快查询效率。

5. UNION

   若第二个select 出现在union之后，则被标记为union

6. UNION RESULT

   从UNION表获取结果的select

   

##### type字段

```
NULL>system>const>eq_ref>ref>fulltext>ref_or_null>index_merge>unique_subquery>index_subquery>range>index>ALL //最好到最差
备注：掌握以下10种常见的即可
NULL>system>const>eq_ref>ref>ref_or_null>index_merge>range>index>ALL
```

1. NULL

   MySQL能够在优化阶段分解查询语句，在执行阶段用不着再访问表或索引
   ```sql
   
   ```

2. system

　　表只有一行记录（等于系统表），这是const类型的特列，平时不大会出现，可以忽略
3. const
　　表示通过索引一次就找到了，const用于比较primary key或uique索引，因为只匹配一行数据，所以很快，如主键置于where列表中，MySQL就能将该查询转换为一个常量
　　```
　　```
4. eq_ref

   唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常见于主键或唯一索引扫描
   ```
   
   ```
5. ref

6. ref_or_null

7. index_merge

8. range

9. index

10. all

##### Extra字段
1. Using filesort
2. Using temporary
3. Using index
4. Using where
5. Using join buffer
6. impossible where
7. distinct
8. select tables optimized away



### explain的用途



## 后记

@[TOC](这里写自定义目录标题)



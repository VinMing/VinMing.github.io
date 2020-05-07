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
1. **id相同**
   
   ```sql
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
   id相同，执行顺序从上至下
   
   读取顺序: teacher > subject > subject
   
   体现在该sql语句的where的从右到左。
   
2. **id不同**

   子查询，id 的序号递增，id的值越大优先级越高，优先被执行。

   ```sql
mysql> explain select student_score.* from student_score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));
+----+-------------+---------------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
| id | select_type | table         | partitions | type  | possible_keys  | key            | key_len | ref   | rows | filtered | Extra                    |
+----+-------------+---------------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
|  1 | PRIMARY     | student_score | NULL       | ref   | idx_subject_id | idx_subject_id | 5       | const |    3 |   100.00 | Using where              |
|  2 | SUBQUERY    | subject       | NULL       | ref   | idx_teacher_id | idx_teacher_id | 5       | const |    1 |   100.00 | Using where; Using index |
|  3 | SUBQUERY    | teacher       | NULL       | const | PRIMARY        | PRIMARY        | 4       | const |    1 |   100.00 | Using index              |
+----+-------------+---------------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
3 rows in set, 1 warning (0.00 sec)
   ```
   读取顺序是teacher > subject > student_score,
   
   体现在该sql语句中是先里后外
   
3. **id相同又不同**

	id如果相同，可以认为是一组，从上往下顺序执行，在所有组中，id值越大，优先级越高，越先执行。
	```sql
mysql> explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id union select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
+----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
| id | select_type  | table      | partitions | type   | possible_keys  | key            | key_len | ref                             | rows | filtered | Extra                                              |
+----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
|  1 | PRIMARY      | subject    | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            |    4 |   100.00 | NULL                                               |
|  1 | PRIMARY      | teacher    | NULL       | eq_ref | PRIMARY        | PRIMARY        | 4       | spider_tmall.subject.teacher_id |    1 |   100.00 | Using index                                        |
|  2 | UNION        | teacher    | NULL       | index  | NULL           | unx_teacher_no | 83      | NULL                            |    4 |   100.00 | Using index                                        |
|  2 | UNION        | subject    | NULL       | ALL    | idx_teacher_id | NULL           | NULL    | NULL                            |    4 |   100.00 | Using where; Using join buffer (Block Nested Loop) |
| NULL | UNION RESULT | <union1,2> | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            | NULL |     NULL | Using temporary                                    |
	+----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
5 rows in set, 1 warning (0.00 sec)
	```

　　读取顺序：2.teacher > 2.subject > 1.subject > 1.teacher

　　体现在该sql语句的where的从右到左。


##### select_tpye字段(６种情况)
1. SIMPLE

   简单查询，不包含子查询或者Union查询。

   ```sql
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

2. PRIMARY

   查询中包含任何复杂的子部分,最外层查询则被标记为主查询。

   ```
   mysql> explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));
   +----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
   | id | select_type | table   | partitions | type  | possible_keys  | key            | key_len | ref   | rows | filtered | Extra                    |
   +----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
   |  1 | PRIMARY     | score   | NULL       | ref   | idx_subject_id | idx_subject_id | 5       | const |    3 |   100.00 | Using where              |
   |  2 | SUBQUERY    | subject | NULL       | ref   | idx_teacher_id | idx_teacher_id | 5       | const |    1 |   100.00 | Using where; Using index |
   |  3 | SUBQUERY    | teacher | NULL       | const | PRIMARY        | PRIMARY        | 4       | const |    1 |   100.00 | Using index              |
   +----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
   3 rows in set, 1 warning (0.00 sec)
   ```

   

4. SUBQUERY

   在select 或者 where 中包含子查询

   ```sql
   mysql> explain select score.* from student_score as score where subject_id = (select id from subject where teacher_id = (select id from teacher where id = 2));
   +----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
   | id | select_type | table   | partitions | type  | possible_keys  | key            | key_len | ref   | rows | filtered | Extra                    |
   +----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
   |  1 | PRIMARY     | score   | NULL       | ref   | idx_subject_id | idx_subject_id | 5       | const |    3 |   100.00 | Using where              |
   |  2 | SUBQUERY    | subject | NULL       | ref   | idx_teacher_id | idx_teacher_id | 5       | const |    1 |   100.00 | Using where; Using index |
   |  3 | SUBQUERY    | teacher | NULL       | const | PRIMARY        | PRIMARY        | 4       | const |    1 |   100.00 | Using index              |
   +----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+--------------------------+
   3 rows in set, 1 warning (0.00 sec)
   ```

5. DERIVED

   在**FROM**列表中包含的子查询被标记为**DERIVED(衍生)**,MySQL会递归执行这些子查询，把结果放到临时表中。
   备注：MySQL 5.7 + 进行了优化，增加了derived_merge（派生合并）,默认开启，可加快查询效率。

5. UNION

   若第二个select 出现在union之后，则被标记为union

   ```sql
   mysql> explain
       -> select subject.* from subject left join teacher on subject.teacher_id = teacher.id
       -> union
       -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   | id | select_type  | table      | partitions | type   | possible_keys  | key            | key_len | ref                             | rows | filtered | Extra                                              |
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   |  1 | PRIMARY      | subject    | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            |    4 |   100.00 | NULL                                               |
   |  1 | PRIMARY      | teacher    | NULL       | eq_ref | PRIMARY        | PRIMARY        | 4       | spider_tmall.subject.teacher_id |    1 |   100.00 | Using index                                        |
   |  2 | UNION        | teacher    | NULL       | index  | NULL           | unx_teacher_no | 83      | NULL                            |    4 |   100.00 | Using index                                        |
   |  2 | UNION        | subject    | NULL       | ALL    | idx_teacher_id | NULL           | NULL    | NULL                            |    4 |   100.00 | Using where; Using join buffer (Block Nested Loop) |
   | NULL | UNION RESULT | <union1,2> | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            | NULL |     NULL | Using temporary                                    |
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   5 rows in set, 1 warning (0.01 sec)
   ```

   

7. UNION RESULT

   从UNION表获取结果的select
   
      ```sql
   mysql> explain
       -> select subject.* from subject left join teacher on subject.teacher_id = teacher.id
       -> union
       -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   | id | select_type  | table      | partitions | type   | possible_keys  | key            | key_len | ref                             | rows | filtered | Extra                                              |
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   |  1 | PRIMARY      | subject    | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            |    4 |   100.00 | NULL                                               |
   |  1 | PRIMARY      | teacher    | NULL       | eq_ref | PRIMARY        | PRIMARY        | 4       | spider_tmall.subject.teacher_id |    1 |   100.00 | Using index                                        |
   |  2 | UNION        | teacher    | NULL       | index  | NULL           | unx_teacher_no | 83      | NULL                            |    4 |   100.00 | Using index                                        |
   |  2 | UNION        | subject    | NULL       | ALL    | idx_teacher_id | NULL           | NULL    | NULL                            |    4 |   100.00 | Using where; Using join buffer (Block Nested Loop) |
   | NULL | UNION RESULT | <union1,2> | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            | NULL |     NULL | Using temporary                                    |
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   5 rows in set, 1 warning (0.01 sec)
      ```


##### type字段

```
NULL>system>const>eq_ref>ref>fulltext>ref_or_null>index_merge>unique_subquery>index_subquery>range>index>ALL //最好到最差
备注：掌握以下10种常见的即可
NULL>system>const>eq_ref>ref>ref_or_null>index_merge>range>index>ALL
```

1. NULL

   MySQL能够在优化阶段分解查询语句，在执行阶段用不着再访问表或索引
   ```sql
   mysql> explain select min(id) from subject;
   +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------------------+
   | id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                        |
   +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------------------+
   |  1 | SIMPLE      | NULL  | NULL       | NULL | NULL          | NULL | NULL    | NULL | NULL |     NULL | Select tables optimized away |
   +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------------------+
   1 row in set, 1 warning (0.00 sec)
   ```

2. system

　　表只有一行记录（等于系统表），这是const类型的特列，平时不大会出现，可以忽略
3. const
　　表示通过索引一次就找到了，const用于比较primary key或uique索引，因为只匹配一行数据，所以很快，如主键置于where列表中，MySQL就能将该查询转换为一个常量

	```sql
　　mysql> explain select * from teacher where teacher_no = 'T2010001';
　　+----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+-------+
　　| id | select_type | table   | partitions | type  | possible_keys  | key            | key_len | ref   | rows | filtered | Extra |
　　+----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+-------+
　　|  1 | SIMPLE      | teacher | NULL       | const | unx_teacher_no | unx_teacher_no | 83      | const |    1 |   100.00 | NULL  |
　　+----+-------------+---------+------------+-------+----------------+----------------+---------+-------+------+----------+-------+
　　1 row in set, 1 warning (0.00 sec)
	```

4. eq_ref

   唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常见于主键或唯一索引扫描
   ```sql
   mysql> explain select subject.* from subject left join teacher on subject.teacher_id = teacher.id;
   +----+-------------+---------+------------+--------+---------------+---------+---------+---------------------------------+------+----------+-------------+
   | id | select_type | table   | partitions | type   | possible_keys | key     | key_len | ref                             | rows | filtered | Extra       |
   +----+-------------+---------+------------+--------+---------------+---------+---------+---------------------------------+------+----------+-------------+
   |  1 | SIMPLE      | subject | NULL       | ALL    | NULL          | NULL    | NULL    | NULL                            |    4 |   100.00 | NULL        |
   |  1 | SIMPLE      | teacher | NULL       | eq_ref | PRIMARY       | PRIMARY | 4       | spider_tmall.subject.teacher_id |    1 |   100.00 | Using index |
   +----+-------------+---------+------------+--------+---------------+---------+---------+---------------------------------+------+----------+-------------+
   2 rows in set, 1 warning (0.00 sec)
   ```

5. ref

     非唯一性索引扫描，返回匹配某个单独值的所有行
     本质上也是一种索引访问，返回所有匹配某个单独值的行
     然而可能会找到多个符合条件的行，应该属于查找和扫描的混合体
      ```sql
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
     
6. ref_or_null

     类似ref，但是可以搜索值为NULL的行

     None

7. index_merge

     表示使用了索引合并的优化方法
     
     None

8. range

     只检索给定范围的行，使用一个索引来选择行，key列显示使用了哪个索引,一般就是在你的where语句中出现between、<>、in等的查询。

     ```sql
     mysql> explain select * from subject where id between 1 and 3;
     +----+-------------+---------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
     | id | select_type | table   | partitions | type  | possible_keys | key     | key_len | ref  | rows | filtered | Extra       |
     +----+-------------+---------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
     |  1 | SIMPLE      | subject | NULL       | range | PRIMARY       | PRIMARY | 4       | NULL |    3 |   100.00 | Using where |
     +----+-------------+---------+------------+-------+---------------+---------+---------+------+------+----------+-------------+
     1 row in set, 1 warning (0.00 sec)
     ```

     

9. index
     Full index Scan 遍历索引树
     
     ```
     mysql> explain select id from subject;
     +----+-------------+---------+------------+-------+---------------+----------------+---------+------+------+----------+-------------+
     | id | select_type | table   | partitions | type  | possible_keys | key            | key_len | ref  | rows | filtered | Extra       |
     +----+-------------+---------+------------+-------+---------------+----------------+---------+------+------+----------+-------------+
     |  1 | SIMPLE      | subject | NULL       | index | NULL          | idx_teacher_id | 5       | NULL |    4 |   100.00 | Using index |
     +----+-------------+---------+------------+-------+---------------+----------------+---------+------+------+----------+-------------+
     1 row in set, 1 warning (0.00 sec)
     ```


10. all
	Full Table Scan，将遍历全表以找到匹配行
	```
	mysql> explain select * from subject;
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
| id | select_type | table   | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
|  1 | SIMPLE      | subject | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |   100.00 | NULL  |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
	```

##### Extra字段

包含不适合在其它列中显示但十分重要的额外信息

1. Using filesort


   说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取

   MySQL中无法利用索引完成的排序操作称为“文件排序”
   ```
   mysql> explain select * from subject order by name;
   +----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+----------------+
   | id | select_type | table   | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra          |
   +----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+----------------+
   |  1 | SIMPLE      | subject | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |   100.00 | Using filesort |
   +----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+----------------+
   1 row in set, 1 warning (0.00 sec)
   ```

4. Using temporary
   使用了临时表保存中间结果，MySQL在对结果排序时使用临时表，常见于排序order by 和分组查询group by
   ```
   mysql> explain
       -> select subject.* from subject left join teacher on subject.teacher_id = teacher.id
       -> union
       -> select subject.* from subject right join teacher on subject.teacher_id = teacher.id;
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   | id | select_type  | table      | partitions | type   | possible_keys  | key            | key_len | ref                             | rows | filtered | Extra                                              |
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   |  1 | PRIMARY      | subject    | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            |    4 |   100.00 | NULL                                               |
   |  1 | PRIMARY      | teacher    | NULL       | eq_ref | PRIMARY        | PRIMARY        | 4       | spider_tmall.subject.teacher_id |    1 |   100.00 | Using index                                        |
   |  2 | UNION        | teacher    | NULL       | index  | NULL           | unx_teacher_no | 83      | NULL                            |    4 |   100.00 | Using index                                        |
   |  2 | UNION        | subject    | NULL       | ALL    | idx_teacher_id | NULL           | NULL    | NULL                            |    4 |   100.00 | Using where; Using join buffer (Block Nested Loop) |
   | NULL | UNION RESULT | <union1,2> | NULL       | ALL    | NULL           | NULL           | NULL    | NULL                            | NULL |     NULL | Using temporary                                    |
   +----+--------------+------------+------------+--------+----------------+----------------+---------+---------------------------------+------+----------+----------------------------------------------------+
   5 rows in set, 1 warning (0.01 sec)
   ```

3. Using index

   ```
   表示相应的select操作中使用了覆盖索引（Covering Index）,避免访问了表的数据行，效率不错！
   如果同时出现using where，表明索引被用来执行索引键值的查找
   如果没有同时出现using where，表明索引用来读取数据而非执行查找动作
   备注：
   覆盖索引：select的数据列只用从索引中就能够取得，不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件，即查询列要被所建的索引覆盖
   ```
   ```
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

4. Using where

	使用了where条件
	同上
5. Using join buffer
	使用了连接缓存
	```
	mysql> explain select student.*,teacher.*,subject.* from student,teacher,subject;
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+---------------------------------------+
| id | select_type | table   | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                                 |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+---------------------------------------+
|  1 | SIMPLE      | student | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |   100.00 | NULL                                  |
|  1 | SIMPLE      | teacher | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |   100.00 | Using join buffer (Block Nested Loop) |
|  1 | SIMPLE      | subject | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    4 |   100.00 | Using join buffer (Block Nested Loop) |
+----+-------------+---------+------------+------+---------------+------+---------+------+------+----------+---------------------------------------+
3 rows in set, 1 warning (0.00 sec)
	```

6. impossible where
	where子句的值总是false，不能用来获取任何元组
	```
	mysql> explain select * from teacher where name = 'wangsi' and name = 'lisi';
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
| id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra            |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
|  1 | SIMPLE      | NULL  | NULL       | NULL | NULL          | NULL | NULL    | NULL | NULL |     NULL | Impossible WHERE |
+----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------+
1 row in set, 1 warning (0.00 sec)
	```
7. distinct
	一旦mysql找到了与行相联合匹配的行，就不再搜索了
	```
	mysql> explain select distinct teacher.name from teacher left join subject on teacher.id = subject.teacher_id;
+----+-------------+---------+------------+-------+----------------+----------------+---------+-------------------------+------+----------+------------------------------+
| id | select_type | table   | partitions | type  | possible_keys  | key            | key_len | ref                     | rows | filtered | Extra                        |
+----+-------------+---------+------------+-------+----------------+----------------+---------+-------------------------+------+----------+------------------------------+
|  1 | SIMPLE      | teacher | NULL       | index | idx_name       | idx_name       | 83      | NULL                    |    4 |   100.00 | Using index; Using temporary |
|  1 | SIMPLE      | subject | NULL       | ref   | idx_teacher_id | idx_teacher_id | 5       | spider_tmall.teacher.id |    1 |   100.00 | Using index; Distinct        |
+----+-------------+---------+------------+-------+----------------+----------------+---------+-------------------------+------+----------+------------------------------+
2 rows in set, 1 warning (0.00 sec)

	```
8. select tables optimized away
	SELECT操作已经优化到不能再优化了（MySQL根本没有遍历表或索引就返回数据了）
   
   ```sql
   mysql> explain select min(id) from subject;
   +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------------------+
   | id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra                        |
   +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------------------+
   |  1 | SIMPLE      | NULL  | NULL       | NULL | NULL          | NULL | NULL    | NULL | NULL |     NULL | Select tables optimized away |
   +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+------------------------------+
   1 row in set, 1 warning (0.00 sec)
   ```
### 检查

[你现在看明白这个explain 的执行效果吗？](#### explain的执行效果　12个字段)

### explain的用途
1. 表的读取顺序如何
2. 数据读取操作有哪些操作类型
3. 哪些索引可以使用
4. 哪些索引被实际使用
5. 表之间是如何引用
6. 每张表有多少行被优化器查询


## 后记
[一张图彻底搞懂MySQL的 explain](https://segmentfault.com/a/1190000021458117?utm_source=tag-newest)
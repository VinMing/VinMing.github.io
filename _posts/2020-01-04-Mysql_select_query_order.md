---
layout:     post
title:      MySQL SELECT query order
subtitle:    "\"Mysql 查询语句执行顺序\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - MySQL
    - SELECT query
---
## 前言

下面是一段MySQL 查询语句代码

```SQL
SELECT 
DISTINCT <字段列表>
FROM <左表>
<连接方式> JOIN <右表>
ON <连接条件>
WHERE <过滤条件>
GROUP BY <分组字段>
HAVING <包含条件>
ORDER BY <排序方式>
LIMIT <限制行数>[, <偏移行数>]
```

以上的伪代码，有联结、过滤、分组、排序等，基本覆盖了查询语句的所有子句。



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
在SQL中，处理的第一个子句是FROM子句，而SELECT在SQL查询中首先出现的子句将在以后进行处理。SQL查询的逻辑处理涉及的阶段如下：
1. FROM 阶段
2. WHERE 阶段
3. GROUP BY 阶段
4. HAVING 阶段
5. SELECT 阶段
6. ORDER BY 阶段
### 1. FROM 阶段

FROM阶段标识出查询的来源表，并处理表运算符。在涉及到联接运算的查询中（各种join），主要有以下几个步骤：

1. 求**笛卡尔积**。不论是什么类型的联接运算，首先都是执行交叉连接（cross join），求笛卡儿积，生成虚拟表VT1-J1。
2. ON筛选器。这个阶段对上个步骤生成的VT1-J1进行筛选，根据ON子句中出现的谓词进行筛选，让谓词取值为true的行通过了考验，插入到VT1-J2。
3. 添加外部行（join）。如果指定了outer join，还需要将VT1-J2中没有找到匹配的行，作为外部行添加到VT1-J2中，生成VT1-J3。

### 2. WHERE 阶段

WHERE阶段是根据 <过滤条件> 中条件对VT1中的行进行筛选，让条件成立的行才会插入到VT2中。

### 3. GROUP BY 阶段

GROUP阶段按照指定的列名列表，将VT2中的行进行分组，生成VT3。最后每个分组只有一行。

### 4. HAVING 阶段

该阶段根据HAVING子句中出现的谓词对VT3的分组进行筛选，并将符合条件的组插入到VT4中。

### 5. SELECT 阶段

这个阶段是投影的过程，处理SELECT子句提到的元素，产生VT5。这个步骤一般按下列顺序进行：

1. 计算SELECT列表中的表达式，生成VT5-1。
2. 若有DISTINCT，则删除VT5-1中的重复行，生成VT5-2
3. 若有TOP，则根据ORDER BY子句定义的逻辑顺序，从VT5-2中选择签名指定数量或者百分比的行，生成VT5-3

### 6. ORDER BY 阶段

根据ORDER BY子句中指定的列明列表，对VT5-3中的行，进行排序，生成游标VC6.

## 参考
[MySQL query / clause execution order](https://stackoverflow.com/questions/24127932/mysql-query-clause-execution-order)
[What Is The Order Of Execution Of An SQL Query?](https://www.designcise.com/web/tutorial/what-is-the-order-of-execution-of-an-sql-query)



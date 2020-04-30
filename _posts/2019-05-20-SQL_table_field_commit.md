---
layout:     post
title:      MySQL Table Commit
subtitle:    "\"MySQL Table  Commit\""
date:       2019-05-20
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - MySQL

---
## 前言

今天在创建完表之后，发现没有办法给表添加注释说明，字段的注释可以在建表的时候就添加

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
### 创表添加表注释
``` sql
Create table test_tb
(
	id int identity(1,1) primary key COMMENT '商品id',
	inserttime date COMMENT '日期'
)COMMENT '测试表'

```
### 修改表注释
``` sql
ALTER TABLE test_tb COMMENT='修改-测试表';  
```
## 后记

@[TOC](这里写自定义目录标题)



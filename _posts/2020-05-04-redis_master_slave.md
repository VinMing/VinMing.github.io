---
layout:     post
title:      Redis Master Slave
subtitle:    "\"redis master slave\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Redis
---
## 前言

本文档是在docker的环境在搭建redis主从模式。

这个跟redis集群不是量级别，redis主从模式只是小case的集群，不包含晦涩难懂的分布式概念， 也没有像 [*Redis 集群规范*](http://doc.redisfans.com/topic/cluster-spec.html#cluster-spec) 那样包含 Redis 集群的实现细节， 如果你打算深入地学习 Redis 集群的部署方法， 那么推荐你在阅读完这个教程之后， 再去看一看集群规范。

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

#### Redis主从复制简单介绍

为了使得集群在一部分节点下线或者无法与集群的大多数节点进行通讯的情况下， 仍然可以正常运作， Redis 集群对节点使用了主从复制功能： 集群中的每个节点都有 1 个至 N 个复制品（replica）， 其中一个复制品为主节点（master）， 而其余的 N-1 个复制品为从节点（slave）。

#### Redis主从复制的常用的几种方式

1. 一主二仆 A(B、C) 一个Master两个Slave
2. 薪火相传（去中心化）A - B - C ，B既是主节点（C的主节点），又是从节点（A的从节点）
3. 反客为主（主节点down掉后，手动操作升级从节点为主节点） & 哨兵模式（主节点down掉后，自动升级从节点为主节点）

本次主要介绍一主二仆，和反客为主的操作，薪火相传不做介绍。哨兵模式后面专门写一篇进行介绍！

环境说明：

| 名称       | 对应IP          |
| ---------- | --------------- |
| 主机master | 192.168.10.153  |
| 从机slave  | 192.168.152.133 |

搭建：







## 后记

@[TOC](这里写自定义目录标题)



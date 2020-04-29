---
layout:     post
title:      Python Autogen Unique Flag 
subtitle:    "\" Python Autogen Unique Flag \""
date:       2020-04-20
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - hashlib
    - uuid

---
## 前言

在python存入数据库时，如果数据库的主键不是自增方式，那么我们可能需要自己生成一个唯一标识符，现在最好的方法就是md5加密生成的32位作为主键，本文将会介绍python的两种自动生成唯一标识的方式。

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
     Python 3.7.0
```

## 正文

###  1. hashlib

依赖包 ：

```python
import  hashlib
```
用法：
```python
def md5(string):
    # 对要加密的字符串进行指定编码
    string = string.encode(encoding ='UTF-8')
    # md5加密
	print(hashlib.md5(string))
    # 将md5 加密结果转字符串显示
    return hashlib.md5(string).hexdigest()
```

###  2. uuid
依赖包
```python
import uuid
```
用法：
```python
# 获取唯一加密值，uuid1 根据主机mac地址和时间戳生成全球唯一加密值

# 唯一缺点会暴露mac地址

id = uuid.uuid1()

#将生成的加密值去除-获得32位加密值

id = id.replace('-','')
```
知识点
``` tex
   uuid是128位的全局唯一标识符（univeral unique identifier），通常用32位的一个字符串的形式来表现。有时也称guid(global unique identifier)。
  
   python中自带了uuid模块来进行uuid的生成和管理工作。

　　python中的uuid模块基于信息如MAC地址、时间戳、命名空间、随机数、伪随机数来uuid。具体方法有如下几个：　　

　　uuid.uuid1()　　基于MAC地址，时间戳，随机数来生成唯一的uuid，可以保证全球范围内的唯一性，缺点会暴露mac地址。

　　uuid.uuid2()　　算法与uuid1相同，不同的是把时间戳的前4位置换为POSIX的UID。不过需要注意的是python中没有基于DCE的算法，所以python的uuid模块中没有uuid2这个方法。

　　uuid.uuid3(namespace,name)　　通过计算一个命名空间和名字的md5散列值来给出一个uuid

　　uuid.uuid4()　　通过伪随机数得到uuid，是有一定概率重复的

　　uuid.uuid5(namespace,name)　　和uuid3基本相同，只不过采用的散列算法是sha1
```
## 参考
[python_uuid](https://vinming.github.io/2018/05/01/python_uuid/)
## 后记

@[TOC](这里写自定义目录标题)



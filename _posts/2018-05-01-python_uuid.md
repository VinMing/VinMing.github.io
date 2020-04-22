---
layout:     post
title:      Python uuid
subtitle:    "\"Python uuid\""
date:       2018-05-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python
    - uuid

---
## 前言

Python标准库 -- UUID模块(生成唯一标识)

UUID可以被用作多种用途, 既可以用来短时间内标记一个对象，也可以可靠的辨别网络中的持久性对象。

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

### UUID是什么：

UUID： 通用唯一标识符 ( Universally Unique Identifier )，对于所有的UUID它可以保证在空间和时间上的唯一性，也称为GUID，全称为：

UUID —— Universally Unique IDentifier  Python中称为 UUID
GUID —— Globally Unique IDentifier     C#中称为 GUID

它是通过MAC地址、 时间戳、 命名空间、 随机数、 伪随机数来保证生成ID的唯一性,，有着固定的大小( 128 bit位 )，通常由 32 字节的字符串（十六进制）表示。

它的唯一性和一致性特点，使得可以无需注册过程就能够产生一个新的UUID；


### UUID有什么用?
很多应用场景需要一个id，但是又不要求这个id 有具体的意义，仅仅用来标识一个对象。

常见的用处有数据库表的id字段；另一个例子是前端的各种UI库，因为它们通常需要动态创建各种UI元素，这些元素需要唯一的id， 这时候就需要使用UUID了。例如：一个网站在存储视频、图片等格式的文件时，这些文件的命名方式就可以采用 UUID生成的随机标识符，避免重名的出现。


### UUID模块提供的UUID类和函数
python的uuid模块提供的UUID类和函数uuid1()，uuid3()，uuid4()，uuid5() 来生成1, 3, 4, 5各个版本的UUID ( 需要注意的是：python中没有uuid2()这个函数)。

```tex
对uuid模块中最常用的几个函数总结如下:

1.  uuid.uuid1([node[, clock_seq]]) -- 基于时间戳

由 MAC 地址（主机物理地址）、当前时间戳、随机数生成。可以保证全球范围内的唯一性，
但 MAC 的使用同时带来安全性问题，局域网中可以使用 IP 来代替MAC。

该函数有两个参数, 如果 node 参数未指定, 系统将会自动调用 getnode() 函数来获取主机的硬件地址. 如果 clock_seq  参数未指定系统会使用一个随机产生的14位序列号来代替.

注意： uuid1() 返回的不是普通的字符串，而是一个 uuid 对象，其内含有丰富的成员函数和变量。

2.  uuid.uuid2() -- 基于分布式计算环境DCE（Python中没有这个函数）

算法与uuid1相同，不同的是把时间戳的前 4 位置换为 POSIX 的 UID。
实际中很少用到该方法。

3.  uuid.uuid3(namespace, name) -- 基于名字的MD5散列值

通过计算名字和命名空间的MD5散列值得到，保证了同一命名空间中不同名字的唯一性，
和不同命名空间的唯一性，但同一命名空间的同一名字生成相同的uuid。

4.  uuid.uuid4() -- 基于随机数

由伪随机数得到，有一定的重复概率，该概率可以计算出来。

5.  uuid.uuid5() -- 基于名字的SHA-1散列值

算法与uuid3相同，不同的是使用 Secure Hash Algorithm 1 算法
```
## 后记
首先，Python中没有基于 DCE 的，所以uuid2可以忽略；

其次，uuid4存在概率性重复，由无映射性，最好不用；

再次，若在Global的分布式计算环境下，最好用uuid1；

最后，若有名字的唯一性要求，最好用uuid3或uuid5。

```python
import uuid
name = "t_name"
namespace = "t_namespace"

print(uuid.uuid1()) # 带参的方法参见Python Doc

print(uuid.uuid3(namespace, name))
print(uuid.uuid4())
print(uuid.uuid5(namespace, name))
```
## 参考
[Python uuid Doc](https://docs.python.org/3.7/library/uuid.html)




---
layout:     post
title:      Python Multiprocessing
subtitle:    "\"Python Multiprocessing\""
date:       2019-11-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python

---
## 前言

本教程实例python 进程池，可用于处理并发编程场景。

## 环境
#### 系统环境
```text
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.4 LTS
Release:	18.04
Codename:	bionic
Linux version :       5.3.0-46-generic ( buildd@lcy01-amd64-013 ) 
Gcc version:         7.5.0  ( Ubuntu 7.5.0-3ubuntu1~18.04 )

Distributor ID:	Deepin
Description:	Deepin 20 Beta
Release:	20 Beta
Codename:	n/a
Linux version :     5.3.0-3-amd64 (debian-kernel@lists.debian.org)
Gcc version:        8.3.0 (Debian 8.3.0-6)
```
#### 软件信息
```text
version : 	
     None
```

## 正文

引入进程池 : from multiprocessing import Pool

实例化进程池 : p = Pool( numprocess , initializer , initargs )

```txt
参数 : 
    numprocess : 要创建的进程数,如果省略,默认使用cpu_count() + 1的值

    initializer : 每个工作进程启动时要执行的可调用对象,默认为None

    initargs : 是要传给initializer的参数组
```

**三个主要方法 :**

1. p.apply ( func,args = ())    同步的效率,也就是说池中的进程一个一个的去执行任务

```tex
参数 : 
   func : 进程池中的进程要执行的任务函数

　　args : 可迭代对象性参数,是传给任务函数的参数
```

**同步处理任务时,不需要close和join,并且进程池中的所有进程都是普通进程(主进程等其执行完再结束)**

例子：

```python
from multiprocessing import Pool
import time

def func(num):
    num += 1
    return num

if __name__ == '__main__':
    p = Pool(5)
    start = time.time()
    for i in range(10000):
        res = p.apply(func,args=(i,))# 同步处理这100个任务，同步是指，哪怕我进程中有5个进程，也依旧是1个进程1个进程的去执行任务
        # time.sleep(0.5)
        print(res)
    print(time.time() - start)
```
2. p.apply_async( func,args = () , callback = None) : 异步的效率,也就是池中的进程一次性都去执行任务。
```tex
参数 : 
	func : 进程池中的进程执行的任务函数

	args : 可迭代对象性的参数,是传给任务函数的参数
	
	callback : 回调函数,就是每当进程池中有进程处理完任务了,返回的结果可以交给回调函数,由回调函数进行进一步处理,回调函数只异步才有,同步没有.回调函数是父进程调用
```
例子：
```python
from multiprocessing import Process,Pool
def func(i):
    i+=1
    return i#普通进程处理过的数据返回给主进程p1

def call_back(p1):
    p1+=1
    print(p1)

if __name__ == '__main__':
    p = Pool()
    for i in range(10):
        p1 = p.apply_async(func,args=(i,),callback = call_back)#p调用普通进程并且接受其返回值,将返回值给要执行的回调函数处理
    p.close()
    p.join()
```
**异步处理任务时 : 必须要加上close和join. 进程池的所有进程都是守护进程(主进程代码执行结束,守护进程就结束).** 
例子：

```python
from multiprocessing import Pool
import time

def func(num):
    num += 1
    return num

if __name__ == '__main__':
    p = Pool(5)
    start = time.time()
    l = []
    for i in range(10000):
        res = p.apply_async(func,args=(i,))# 异步处理这100个任务，异步是指，进程中有5个进程，一下就处理5个任务，接下来哪个进程处理完任务了，就马上去接收下一个任务
        l.append(res)
        # print(res.get())#也可以使用这种方法打印返回结果,但是会使异步进程变成同步,因为get()只能一个一个取
    p.close()
    p.join()
    [print(i.get()) for i in l]
    print(time.time() - start)

```
3. map( func,iterable)
```
参数 : 

	func : 进程池中的进程执行的任务函数

	iterable : 可迭代对象,是把可迭代对象那个中的每个元素一次传给任务函数当参数.
```
**map方法自带close和join。**
例子：

```python
from multiprocessing import Pool

def func(num):
    num += 1
    print(num)
    return num

if __name__ == '__main__':
    p = Pool(5)
    res = p.map(func,[i for i in range(100)])
    # p.close()#map方法自带这两种功能
    # p.join()
    print('主进程中map的返回值',res)
```
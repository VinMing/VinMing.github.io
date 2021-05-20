---
layout:     post
title:      Python Gevent
subtitle:    "\"Python Gevent\""
date:       2019-10-11
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python
---
## 前言

协程是一种用户态的轻量级线程，又称微线程。

协程拥有自己的寄存器上下文和栈，调度切换时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈。因此：协程能保留上一次调用时的状态（即所有局部状态的一个特定组合），每次过程重入时，就相当于进入上一次调用的状态，换种说法：进入上一次离开时所处逻辑流的位置。

优点：

1. 无需线程上下文切换的开销
2. 无需原子操作锁定及同步的开销
3. 方便切换控制流，简化编程模型
4. 高并发+高扩展性+低成本：一个CPU支持上万的协程都不是问题。所以很适合用于高并发处理。

所谓原子操作是指不会被线程调度机制打断的操作；这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch （切换到另一个线程）。

原子操作可以是一个步骤，也可以是多个操作步骤，但是其顺序是不可以被打乱，或者切割掉只执行部分。视作整体是原子性的核心。

缺点：

1. 无法利用多核资源：协程的本质是个单线程,它不能同时将 单个CPU 的多个核用上,协程需要和进程配合才能运行在多CPU上.当然我们日常所编写的绝大部分应用都没有这个必要，除非是cpu密集型应用。
2. 进行阻塞（Blocking）操作（如IO时）会阻塞掉整个程序

## 环境
#### 系统环境
```text
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

### 使用Gevent

gevent是python的一个并发框架,以微线程greenlet为核心，使用了epoll事件监听机制以及诸多其他优化而变得高效.

- #### 简单示例

gevent的sleep可以交出控制权，当我们在受限于网络或IO的函数中使用gevent，这些函数会被协作式的调度， gevent的真正能力会得到发挥。Gevent处理了所有的细节， 来保证你的网络库会在可能的时候，隐式交出greenlet上下文的执行权。

```python
import gevent
 
def foo():
    print('running in foo')
    gevent.sleep(0)
    print('com back from bar in to foo')
 
def bar():
    print('running in bar')
    gevent.sleep(0)
    print('com back from foo in to bar')
 
# 创建线程并行执行程序
gevent.joinall([
    gevent.spawn(foo),
    gevent.spawn(bar),
])
```

　　执行结果

```python
running in foo
running in bar
com back from bar in to foo
com back from foo in to bar
```

- ### 同步异步

```python
import random
import gevent
 
def task(pid):
    gevent.sleep(random.randint(0, 2) * 0.001)
    print('Task %s done' % pid)
 
def synchronous():
    for i in range(1, 10):
        task(i)
 
def asynchronous():
    threads = [gevent.spawn(task, i) for i in range(10)]
    gevent.joinall(threads)
 
print('Synchronous:')
synchronous()
 
print('Asynchronous:')
asynchronous()
```

　　执行输出

```tex
Synchronous:
Task 1 done
Task 2 done
Task 3 done
Task 4 done
Task 5 done
Task 6 done
Task 7 done
Task 8 done
Task 9 done
Asynchronous:
Task 1 done
Task 4 done
Task 5 done
Task 9 done
Task 6 done
Task 0 done
Task 2 done
Task 3 done
Task 7 done
Task 8 done
```

- ### 以子类的方法使用协程

可以子类化Greenlet类，重载它的_run方法，类似多线程和多进程模块

```python
import gevent
from gevent import Greenlet
 
class Test(Greenlet):
 
    def __init__(self, message, n):
        Greenlet.__init__(self)
        self.message = message
        self.n = n
 
    def _run(self):
        print(self.message, 'start')
        gevent.sleep(self.n)
        print(self.message, 'end')
 
tests = [
    Test("hello", 3),
    Test("world", 2),
]
 
for test in tests:
    test.start()  # 启动
 
for test in tests:
    test.join()  # 等待执行结束
```

- ### 使用monkey patch修改系统标准库(自动切换协程)

当一个greenlet遇到IO操作时，比如访问网络，就自动切换到其他的greenlet，等到IO操作完成，再在适当的时候切换回来继续执行。

由于IO操作非常耗时，经常使程序处于等待状态，有了gevent为我们自动切换协程，就保证总有greenlet在运行，而不是等待IO。

由于切换是在IO操作时自动完成，所以gevent需要修改Python自带的一些标准库，这一过程在启动时通过monkey patch完成

```python
import gevent
import requests
from gevent import monkey
 
monkey.patch_socket()
 
def task(url):
    r = requests.get(url)
    print('%s bytes received from %s' % (len(r.text), url))
 
gevent.joinall([
    gevent.spawn(task, 'https://www.baidu.com/'),
    gevent.spawn(task, 'https://www.qq.com/'),
    gevent.spawn(task, 'https://www.jd.com/'),
])
```

　　执行输出

```tex
2443 bytes received from https://www.baidu.com/
108315 bytes received from https://www.jd.com/
231873 bytes received from https://www.qq.com/
```

可以看出3个网络操作是并发执行的，而且结束顺序不同

#### 协程池

```python
process_list = []
pool= gevent.pool.Pool(100)  # 协程池固定为100个
for num in range(1, 256):
    test = 'https://www.jd.com/.{}'.format(num)
    # 将任务加到列表中
    process_list.append(pool.spawn(self.task, ip))

gevent.joinall(process_list)  # 等待所有协程结束
```


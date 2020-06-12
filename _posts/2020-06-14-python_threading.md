---
layout:     post
title:      Hello 2020
subtitle:    "\"Hello World, Hello Blog\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 生活

---
## 前言

Python多线程是通过threading模块来实现的。

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

#### 一、多线程共享全局变量
```python
from threading import Thread

list_a = [1, 2, 3]


def add_list():
    global list_a
    list_a.append(100)
    print(list_a)


if __name__ == '__main__':
    t1 = Thread(target=add_list)
    t2 = Thread(target=add_list)
    print(t1.name)
    t1.start()
    print(t2.name)
    t2.start()
```
运行结果：
```sh
Thread-1
[1, 2, 3, 100]
Thread-2
[1, 2, 3, 100, 100]
```
在上面的代码中，我们创建了两个线程，这两个线程都是执行一次函数add_list，在线程t1执行完后，全局变量list_a中多了一个100，在线程t2执行完后，list_a中多了两个100，说明线程t2是在线程t1的基础上进行添加的。也就是说t1和t2两个线程是共享全局变量的。

在一个进程内的所有线程共享全局变量，很方便在多个线程间共享数据。

但是，多线程对全局变量随意修改可能造成全局变量的混乱，产生线程安全问题。

二、多线程的资源竞争问题（线程非安全）
```python
from threading import Thread

num = 0


def add_num():
    global num
    for i in range(100000):
        num += 1


if __name__ == '__main__':
    t1 = Thread(target=add_num)
    t2 = Thread(target=add_num)
    t3 = Thread(target=add_num)
    t1.start()
    t2.start()
    t3.start()
    print(num)
```
运行结果：
```sh
173131
```
在上面的代码中，我们创建了三个线程，每个线程都是将num进行十万次+1运算，因为三个线程是共享全局变量的，所以结果应该是三十万300000。但是，结果却少了很多（每次运行结果不一样）。

在多个线程对全局变量进行修改时，造成得到的结果不正确，这种情况就是线程安全问题。

如果多个线程同时对同一个全局变量操作，会出现资源竞争问题，从而数据结果会不正确，即遇到线程安全问题。

那么，为什么多线程操作全局变量时会有资源竞争问题呢？

先假设两个线程t1和t2都要对全局变量num(从0开始)进行加1运算，t1和t2都各对num加10次，num的最终的结果应该为20。

但是由于是多线程同时操作，有可能出现下面情况：

1.在num=0时，t1取得num=0，但还没有开始做+1运算。此时系统把t1调度为”sleeping”状态，把t2转换为”running”状态，t2也获得num=0

2.然后t2对得到的值进行加1并赋给num，使得num=1

3.然后系统又把t2调度为”sleeping”，把t1转为”running”。线程t1把它之前得到的0加1后赋值给num。

4.这样导致虽然t1和t2都对num加1，但结果仍然是num=1

不过，一般在万级运算次数以下，不会出现资源竞争问题，当上了十万级或更高量级时，资源竞争问题会越来越明显。当然，这与电脑的配置也有关。

#### 三、通过同步机制来解决线程安全问题
```python
from threading import Thread, Lock, enumerate
import time

num = 0
mutex = Lock()


def add_num():
    global num
    for i in range(100000):
        mutex.acquire()
        num += 1
        mutex.release()


if __name__ == '__main__':
    t1 = Thread(target=add_num)
    t2 = Thread(target=add_num)
    t3 = Thread(target=add_num)
    t1.start()
    t2.start()
    t3.start()

    while len(enumerate()) != 1:
        time.sleep(1)
    print(num)
```
运行结果：
```sh
300000
```
上面的代码中，我们给之前的三个线程中加了锁，这样最后执行的结果就是我们想要的结果。

这种方式是使用互斥锁来实现同步，避免资源竞争问题发生。

除了使用互斥锁可以保证线程同步外，还有其他方式可以实现同步，解决线程安全，如通过队列来实现同步，因为队列是串行的，底层封装了锁。

#### 四、同步和互斥锁

同步就是程序按预定的先后次序依次运行。

通过线程同步机制，能保证共享数据在任何时刻，最多有一个线程访问，以保证数据的正确性。

注意：

1.线程同步就是线程排队

2.共享资源的读写才需要同步

3.变量才需要同步，常量不需要同步

当多个线程几乎同时修改某一个共享数据的时候，需要进行同步控制。

线程同步能够保证多个线程安全地访问竞争资源，最简单的同步机制是使用互斥锁。

互斥锁为资源引入了一个状态：锁定/非锁定

某个线程要更改共享数据时，先将其锁定，此时资源的状态为“锁定”，其他线程不能更改。直到该线程释放资源，将资源的状态变成“非锁定”，其他的线程才能再次锁定该资源。互斥锁保证了每次只有一个线程进行操作，从而保证了多线程情况下数据的正确性。

注意：

多个线程使用的是同一个锁，如果数据没有被锁锁上，那么acquire()方法不会堵塞，会执行上锁操作。

如果在调用acquire时，数据已经被其他线程上了锁，那么acquire()方法会堵塞，直到数据被解锁为止。

上锁解锁过程：

当一个线程调用锁的acquire()方法获得锁时，锁就进入“locked”状态。

每次只有一个线程可以获得锁。如果此时另一个线程试图获得这个锁，该线程就会变为“blocked”状态，称为“阻塞”，直到拥有锁的线程调用锁的release()方法释放锁之后，锁进入“unlocked”状态。

线程调度程序从处于同步阻塞状态的线程中选择一个来获得锁，并使得该线程进入运行（running）状态。

#### 五、死锁及解决方法
```python
from threading import Thread, Lock
import time

mutex_x = Lock()
mutex_y = Lock()


def x_func():
    print('X...')
    mutex_x.acquire()
    print('Lock x something')
    time.sleep(1)
    mutex_y.acquire()
    print('Use y something')
    time.sleep(1)
    mutex_y.release()
    print('x...')
    mutex_x.release()


def y_func():
    print('Y...')
    mutex_y.acquire()
    print('Lock y something')
    time.sleep(1)
    mutex_x.acquire()
    print('Use x something')
    time.sleep(1)
    mutex_x.release()
    print('y...')
    mutex_y.release()


if __name__ == '__main__':
    t1 = Thread(target=x_func)
    t2 = Thread(target=y_func)

    t1.start()
    t2.start()
```
运行结果：
```python
X...
Lock x something
Y...
Lock y something
```
上面的代码会一直阻塞，程序一直不会结束，因为线程1将mutex_x上了锁，等着锁mutex_y，与此同时，线程2已经将mutex_y上了锁，等着锁mutex_x。这样就形成了死锁。

在线程间共享多个资源的时候，如果两个线程分别占有一部分资源并且同时等待对方的资源，就会造成死锁。

尽管死锁很少发生，但一旦发生就会造成应用的停止响应。

在程序设计时，要尽量避免死锁的出现。

为了避免死锁一直阻塞下去，可以在其中一方添加超时时间，如果超时了，就跳过。
```python
    def x_func():
        print('X...')
        mutex_x.acquire()
        print('Lock x something')
        time.sleep(1)
     
        result = mutex_y.acquire(timeout=10)
        print(result)
        print('Use y something')
        time.sleep(1)
        if result:
            mutex_y.release()
     
        print('x...')
        mutex_x.release()
```
运行结果：
```sh
X...
Lock x something
Y...
Lock y something
False
Use y something
x...
Use x something
y...

Process finished with exit code 0
```
## 后记

@[TOC](这里写自定义目录标题)



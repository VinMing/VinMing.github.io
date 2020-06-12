---
layout:     post
title:      Python Logging Handlers
subtitle:    "\"python logging handlers\""
date:       2020-06-12
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Logging
---
## 前言

假设已经知道了[logging基本使用](https://vinming.github.io/2020/06/12/logging_basic_use/)

实际工作中，对于日志是使用不仅限于输出那么简单，还要处理日益堆积的日志文件，logging 模块中实现了很多日志处理的方法，可以帮我们实现日志的管理功能。

本文除了logging处理类的基本介绍，还加多了日志文件按时间切分、按大小切分、实现单例的程序demo。

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

### 一、logging 中的类

| 类名                | 类路径                               | 功能                                                         |
| ------------------- | ------------------------------------ | ------------------------------------------------------------ |
| StreamHandler       | logging.StreamHandler                | 日志输出到流，可以是sys.stderr，sys.stdout或者文件，这个方法通常用来将日志信息输出到控制台 |
| FileHandler         | logging.FileHandler                  | 日志输出到文件，指定文件，将日志信息写入到文件中             |
| BaseRotatingHandler | logging.handlers.BaseRotatingHandler | 基本的日志轮转方式，这个类是日志轮转的基类，后面日志按时间轮转，按大小轮转的类都继承于此。轮转的意思就是保留一定数量的日志量，如设置保持7天日志，则会自动删除旧的日志，只保留最近7天 |
| RotatingHandler     | logging.handlers.RotatingHandler     | 继承BaseRotatingHandler，支持日志文件按大小轮转              |
| TimeRotatingHandler | logging.handlers.TimeRotatingHandler | 继承BaseRotatingHandler，支持日志文件按时间轮转              |
| SocketHandler       | logging.handlers.SocketHandler       | 远程输出日志到TCP/IP sockets                                 |
| DatagramHandler     | logging.handlers.DatagramHandler     | 远程输出日志到UDP sockets                                    |
| SMTPHandler         | logging.handlers.SMTPHandler         | 远程输出日志到邮件地址                                       |
| MemoryHandler       | logging.handlers.MemoryHandler       | 日志输出到内存中的指定buffer                                 |
| HTTPHandler         | logging.handlers.HTTPHandler         | 通过"GET"或者"POST"远程输出到HTTP服务器                      |

### 二、日志文件按时间切分
```python
import logging
from logging.handlers import TimedRotatingFileHandler
import time

logger = logging.getLogger(__name__)
logger.setLevel(level=logging.INFO)
formatter = '%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s: %(message)s'
time_rotate_file = TimedRotatingFileHandler(filename='time_rotate', when='S', interval=2, backupCount=5)
time_rotate_file.setFormatter(logging.Formatter(formatter))
time_rotate_file.setLevel(logging.INFO)

console_handler = logging.StreamHandler()
console_handler.setLevel(level=logging.INFO)
console_handler.setFormatter(logging.Formatter(formatter))

logger.addHandler(time_rotate_file)
logger.addHandler(console_handler)

while True:
    logger.info('info')
    logger.error('error')
    time.sleep(1)
```
运行结果：
```sh
2020-06-12 14:30:40,473 postman_test.py[line:20] INFO: info
2020-06-12 14:30:40,474 postman_test.py[line:21] ERROR: error
2020-06-12 14:30:41,475 postman_test.py[line:20] INFO: info
2020-06-12 14:30:41,476 postman_test.py[line:21] ERROR: error
.........
```
上面的代码是无限循环，永远也不会停止，为了演示，我将写入文件的日志写信也打印到了控制台。运行代码后，将日志写到文件中，每个文件只保存两秒钟的数据，只保留最新的5个日志文件，文件名是 time_rotate 加时间字符串。

使用 logging.handlers 中的 TimedRotatingFileHandler 类，可以帮助我们实现日志按时间来切分和轮转。

在实际工作中，日志量是很大的，不可能将全部日志写到同一个文件中，这样无法删除旧的日志，且这个文件会越来越大，直到撑爆磁盘。日志按时间切分和轮转的方式根据具体情况来定，如按月切分，保留3年，按天切分，保留30天，按小时切分，保留7天等等，这些 TimedRotatingFileHandler 都可以帮助我们实现。

TimedRotatingFileHandler 的主要参数：

1. filename： 指定日志文件的名字，会在指定的位置创建一个 filename 文件，然后会按照轮转数量创建对应数量的日志文件，每个轮转文件的文件名为 filename 拼接时间，默认YY-mm-DD_HH-MM-SS，可以自定义。

2. when： 指定日志文件轮转的时间单位
    S - Seconds
    M - Minutes
    H - Hours
    D - Days
    midnight - roll over at midnight
    W{0-6} - roll over on a certain day; 0 - Monday
    
3. interval： 指定日志文件轮转的周期，如 when='S', interval=10，表示每10秒轮转一次，when='D', interval=7，表示每周轮转一次。

4. backupCount： 指定日志文件保留的数量，指定一个整数，则日志文件只保留这么多个，自动删除旧的文件。

### 三、日志文件按大小切分
```python
import logging
from logging.handlers import RotatingFileHandler
import time

logger = logging.getLogger(__name__)
logger.setLevel(level=logging.INFO)

formatter = '%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s: %(message)s'
size_rotate_file = RotatingFileHandler(filename='size_rotate', maxBytes=1*1024, backupCount=5)
size_rotate_file.setFormatter(logging.Formatter(formatter))
size_rotate_file.setLevel(logging.INFO)

console_handler = logging.StreamHandler()
console_handler.setLevel(level=logging.INFO)
console_handler.setFormatter(logging.Formatter(formatter))

logger.addHandler(size_rotate_file)
logger.addHandler(console_handler)

while True:
    logger.info('info')
    logger.error('error')
    time.sleep(1)
```

运行结果：
```sh
2020-06-12 14:31:06,254 postman_test.py[line:21] INFO: info
2020-06-12 14:31:06,255 postman_test.py[line:22] ERROR: error
2020-06-12 14:31:07,256 postman_test.py[line:21] INFO: info
2020-06-12 14:31:07,256 postman_test.py[line:22] ERROR: error
..........
```

运行代码后，将日志写到文件中，每个文件只保存 1kb 的数据，只保留最新的5个日志文件，文件名是 size_rotate 加编号，编号从1开始，最新的日志永远保存在 size_rotate.1 中，编号越大，日志时间越靠前。

使用 logging.handlers 中的 RotatingFileHandler 类，可以帮助我们实现日志按文件大小来切分和轮转。

日志按时间切分和轮转的方式根据具体情况来定，如一个文件最多 1G，100M等，保留文件数可以按需定义，这些 RotatingFileHandler 都可以帮助我们实现。

RotatingFileHandler 的主要参数：

1. filename： 指定日志文件的名字，会在指定的位置创建一个 filename 文件，然后会按照轮转数量创建对应数量的日志文件，每个轮转文件的文件名为 filename 拼接编号，编号从1开始。

2. maxBytes： 设置日志文件的大小，单位是字节，如 1kb 是1024，1M 是 1024*1024 ，1G 是 1024*1024*1024 。

3. mode： 设置文件的写入模式，默认 mode='a' ，即追加写入。

4. backupCount： 指定日志文件保留的数量，指定一个整数，日志文件只保留这么多个，自动删除旧的文件。

###  四、实现日志对象单例

在一个项目中，项目的代码是分很多功能模块的，在同一个项目中，最好保证使用的是同一个日志对象，所有日志都由同一个对象来输出，才能保证所有日志写到一个文件之中，这就需要使用单例来实现。
```python
import logging
from logging.handlers import RotatingFileHandler
from threading import Lock

class LoggerProject(object):

    def __init__(self):
        self.mutex = Lock()
        self.formatter = '%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s: %(message)s'

    def _create_logger(self):
        _logger = logging.getLogger(__name__)
        _logger.setLevel(level=logging.INFO)
        return _logger

    def _file_logger(self):
        size_rotate_file = RotatingFileHandler(filename='size_rotate', maxBytes=1024*1024, backupCount=5)
        size_rotate_file.setFormatter(logging.Formatter(self.formatter))
        size_rotate_file.setLevel(logging.INFO)
        return size_rotate_file

    def _console_logger(self):
        console_handler = logging.StreamHandler()
        console_handler.setLevel(level=logging.INFO)
        console_handler.setFormatter(logging.Formatter(self.formatter))
        return console_handler

    def pub_logger(self):
        logger = self._create_logger()
        self.mutex.acquire()
        logger.addHandler(self._file_logger())
        logger.addHandler(self._console_logger())
        self.mutex.release()
        return logger

log_pro1 = LoggerProject()
log_pro2 = LoggerProject()
logger1 = log_pro1.pub_logger()
logger2 = log_pro2.pub_logger()
logger1.info('aaa')
logger2.info('aaa')
print('logger1: ', id(logger1))
print('logger2: ', id(logger2))
print("log_pro1: logger1.info('aaa') ", id(log_pro1))
print("log_pro2: logger2.info('aaa') ", id(log_pro2))
```
运行结果：
```sh
2020-06-12 14:31:50,736 postman_test.py[line:40] INFO: aaa
2020-06-12 14:31:50,736 postman_test.py[line:40] INFO: aaa
2020-06-12 14:31:50,736 postman_test.py[line:41] INFO: aaa
2020-06-12 14:31:50,736 postman_test.py[line:41] INFO: aaa

logger1:  139747377038288
logger2:  139747377038288
log_pro1: logger1.info('aaa')  139747377037840
log_pro2: logger2.info('aaa')  139747377038096
```


## 进阶

将创建 logger 对象的代码封装到一个类中，然后定义一个返回 logger 对象的方法，实例化这个类的不同对象，id 不相同，但是通过它们调用类的方法返回的 logger 对象，id 值是相等的，是同一个实例。只是这个实例会在多个线程中运行，会造成线程安全问题，所以在代码中加了锁来避免线程安全问题。

这样创建出来的 logger 对象已经实现单例了，如果想连类的对象也实现单例，写一个单例装饰器装饰这个类就行了。

[单例参考](https://vinming.github.io/2020/06/12/python_singleton_pattern/)

[线程安全参考](https://vinming.github.io/2020/06/14/python_threading/)



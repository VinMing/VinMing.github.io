---
layout:     post
title:      Python Logging Basic Use
subtitle:    "\"python logging basic use\""
date:       2020-06-12
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Logging

---
## 前言

logging 模块是 Python 内置的标准模块，用于输出代码日志。

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

### 一、简介

在工作中，运行的代码量是非常大的，为了更方便的管理代码运行，监控代码运行的过程，需要在代码中添加一些必要的日志输出。

Python 内置了 logging 模块，在 Python 中，可以使用 logging 模块来实现与日志相关的功能。如输出运行日志到控制台，将运行日志写入文件，日志文件滚动存储等。

相对直接 print 打印运行信息而言，使用 logging 模块输出日志可以设置日志等级，指定输出位置，写入文件等，使用起来方便很多。在一个成熟的项目中，打印的日志量是非常大的，logging 模块还可以帮助我们管理日志，以便更好地维护项目和定位问题(非常重要，项目问题回溯。可以让你减少莫名其妙的背锅风险)。

logging 模块主要包含四个部分：

Loggers: 提供程序调用的接口，在代码中调用 api 来记录日志

Handlers: 对日志信息进行不同的处理，如记录日志的方式

Formatters: 定义日志的打印格式

Filters:对日志信息进行过滤， 自定义日志是否输出的判断

### 二、logging 模块的基本使用
```python
# coding=utf-8
import logging
# 参考　Linux 系统日志
formatter = '%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s: %(message)s'
logging.basicConfig(format=formatter, level=logging.DEBUG)

logger = logging.getLogger(__name__)

logger.debug('test debug')
logger.info('test info')
logger.warning('test warning')
logger.error('test error')
logger.critical('test critical')
```
运行结果：
``` sh
2020-06-12 14:27:08,857 postman_test.py[line:9] DEBUG: test debug
2020-06-12 14:27:08,857 postman_test.py[line:10] INFO: test info
2020-06-12 14:27:08,857 postman_test.py[line:11] WARNING: test warning
2020-06-12 14:27:08,857 postman_test.py[line:12] ERROR: test error
2020-06-12 14:27:08,857 postman_test.py[line:13] CRITICAL: test critical
```
上面的代码中，使用 logging 中的 basicConfig() 方法，传入日志的格式和输出日志的等级，然后通过 getLogger() 方法创建一个  logger 对象，就可以通过 logger 对象来输出日志了。basicConfig() 是 logging 中实现日志输出最简单和最基本的方法。

运行上面的代码，会在控制台打印代码的日志信息，因为 basicConfig() 默认是将日志信息打印到控制台。如果在 basicConfig() 中传入 filename 参数，指定日志输出的文件，则日志信息会写到文件中，不会在控制台打印。

logger 对象有 debug() 、info() 等5个基本的日志输出方法，分别对应了5个日志等级。除此之外还有几个方法，可以在 PyCharm 中点击 getLogger 进入源码查看。

### 三、basicConfig的参数说明

1. filename： 日志输出到文件的文件名

2. filemode： 文件读写模式，r[+]、w[+]、a[+]
3. format： 日志输出的格式

4. datefat： 日志附带日期时间的格式

5. style：格式占位符，默认为 "%" 和 “{}”

6. level：设置日志输出级别

7. stream： 定义输出流，用来初始化 StreamHandler 对象，不能和 filename 参数一起使用，否则会 ValueError 异常

8. handles： 定义处理器，用来创建 Handler 对象，不能和 filename 、stream 参数一起使用，否则也会抛出 ValueError 异常

### 四、logging 的日志级别
在 logging 中，日志主要有5个等级。
1. DEBUG： 对应数值10，打印调式信息。

2. INFO： 对应数值20，处理请求，状态变化等日常信息。

3. WARNING： 对应数值30，比较重要的提醒信息，警告信息。

4. ERROR： 对应数值40，程序发生了报错，如连接错误，编译错误。

5. CRITICAL： 对应数值50，特别严重的问题，如服务器故障等。

对于日志级别来说，对应的数值表示日志信息的重要程度，或问题的严重程度，数值越大，日志等级越高。

通常，日志级别越低，量越大，级别越高，量越少(天天有重大故障项目就不用做了)，在日志的输出过程中，可以指定输出级别，来过滤掉低级别的日志信息。指定日志级别后，只会输出大于等于指定级别的日志，如指定 DEBUG 则会输出所有5个级别的日志，指定 INFO 则不再输出 DEBUG 级别的日志(参考上面代码)。

上面5个日志等级对应的日志输出方法分别为它们的小写。debug()，inifo(), ... 除此之外，还有两个方法，exception() 和 fatal() 。exception() 对应的日志等级与 error() 相同，都是40，exception() 默认返回一个空值。fatal() 有对应的日志等级 FATAL ，fatal() 对应的日志等级与 critical() 相同，都是50，在源码中就是 fatal = critical，两个完全相同。

关于日志级别，我们也可以自定义，自定义日志等级时注意不要和默认的日志等级数值相同。设置日志输出等级的时候，也可以通过数值来指定，只有大于数值的日志等级会被输出(参考上面的代码)。

### 五、logging 日志的输出格式
  1. asctime：日志的输出时间，默认为 YYYY-mm-DD HH:MM:SS,SSS，如： 2019-10-02 21:29:41,016，一直精确到毫秒。可以额外指定 datefmt 参数来指定该变量的格式

  2. name： 日志对象的名称

  3. filename： 不包含路径的文件名

  4. pathname：包含路径的文件名(完整路径)

  5. funcName： 日志记录所在的函数名

  6. levelname： 日志的级别名称

  7. message： 具体的日志信息

  8. lineno： 日志记录的代码所在的行号

  10. process： 当前进程ID

  11. processName： 当前进程名称

  12. thread： 当前线程ID

  12. threadName： 当前线程名称

      ```python
      format = '%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s: %(message)s'
      ```

这些日志信息是可选的，需要哪些可以自定义，其中 asctime 和 message 是必须有的，不然就失去日志的意义了。常用的有 filename，lineno，levelname，其他的根据情况选用。

定义日志输出格式时，%() 是用来为实际的数据占位用的，后面的 s ，d 表示数据的类型，s 表示字符串， d 表示整数。

### 六、日志信息同时输出控制台和写入文件
```python
import logging
file_name = 'logger.txt'
formatter = '%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s: %(message)s'
logging.basicConfig(filename=file_name, format=formatter, level=logging.DEBUG)

logger = logging.getLogger(__name__)

console = logging.StreamHandler()
# console.setLevel(logging.DEBUG)
console.setLevel(20)
console_formatter = logging.Formatter('%(asctime)s - %(filename)s - [line]:%(lineno)d - %(levelname)s - %(message)s')
console.setFormatter(console_formatter)
logger.addHandler(console)

logger.debug('debug->10')
logger.info('info->20')
logger.warning('warning->30')
logger.error('error->40')
logger.exception('exception->40')
logger.critical('critical->50')
logger.fatal('fatal->50')
```

  运行结果：

```sh
2020-06-12 14:30:07,782 - postman_test.py - [line]:16 - INFO - info->20
2020-06-12 14:30:07,782 - postman_test.py - [line]:17 - WARNING - warning->30
2020-06-12 14:30:07,782 - postman_test.py - [line]:18 - ERROR - error->40
2020-06-12 14:30:07,782 - postman_test.py - [line]:19 - ERROR - exception->40
NoneType: None
2020-06-12 14:30:07,782 - postman_test.py - [line]:20 - CRITICAL - critical->50
2020-06-12 14:30:07,782 - postman_test.py - [line]:21 - CRITICAL - fatal->50
```

在使用 basicConfig() 方法实现日志输出时，如果不指定 filename 参数，则日志信息被输出在控制台，如果指定 filename 参数，则日志被写入文件中。

在实际开发中，通常是即需要写入文件，也需要控制台输出。

这时，可以再定义一个日志处理对象，一个对象写文件，一个对象输出控制台。如上面通过 logging 中的 StreamHandler() 创建一个 console 对象，然后通过 addHandler() 将 console 加入到 logger 对象中。console 可以设置与  basicConfig() 不一样的日志输出格式，可以设置不一样的日志输出等级，是互相独立的。

## 进阶
以上只是logging的基础用法，到项目长期运行，日志文件很大，或者很多，该如何处理呢？
[logging模块切分和轮转日志](https://vinming.github.io/2020/06/12/logging_handlers/)

## 参考

[Python + logging 输出到屏幕，将log日志写入文件](https://www.cnblogs.com/nancyzhu/p/8551506.html)



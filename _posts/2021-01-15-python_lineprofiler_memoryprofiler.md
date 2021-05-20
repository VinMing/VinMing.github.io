---
layout:     post
title:      Python Lineprofiler And Memoryprofiler
subtitle:    "\"Python lineprofiler and memoryprofiler\""
date:       2021-01-15
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python

---
## 前言

总会遇到一个时候你会想提高程序执行效率，想看看哪部分耗时长成为瓶颈，想知道程序运行时内存和CPU使用情况。这时候你会需要一些方法对程序进行性能分析和调优。

本例只介绍Lineprofiler 和 Memoryprofiler 两个性能优化工具

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
     Python 3.7.9
```

## 正文

### lineprofiler

lineprofiler是一个对函数进行逐行性能分析的工具，可以参见[lineprofiler github]( https://github.com/rkern/line_profiler)项目说明

### 示例

```python
#coding=utf8

def sum_num(max_num):
    total = 0
    for i in range(max_num):
        total += i
    return total


@profile                     # 添加@profile 来标注分析哪个函数
def test():
    total = 0
    for i in range(40000):
        total += i

    t1 = sum_num(10000000)
    t2 = sum_num(200000)
    t3 = sum_num(300000)
    t4 = sum_num(400000)
    t5 = sum_num(500000)
    test2()

    return total

def test2():
    total = 0
    for i in range(40000):
        total += i

    t6 = sum_num(600000)
    t7 = sum_num(700000)

    return total

test()
```

通过 `kernprof` 命令来注入分析，运行结果如下：

```sh
➜ kernprof -l -v profile.py
Wrote profile results to profile.py.lprof
Timer unit: 1e-06 s

Total time: 3.80125 s
File: profile.py
Function: test at line 10

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
    10                                           @profile
    11                                           def test():
    12         1            5      5.0      0.0      total = 0
    13     40001        19511      0.5      0.5      for i in range(40000):
    14     40000        19066      0.5      0.5          total += i
    15
    16         1      2974373 2974373.0     78.2      t1 = sum_num(10000000)
    17         1        58702  58702.0      1.5      t2 = sum_num(200000)
    18         1        81170  81170.0      2.1      t3 = sum_num(300000)
    19         1       114901 114901.0      3.0      t4 = sum_num(400000)
    20         1       155261 155261.0      4.1      t5 = sum_num(500000)
    21         1       378257 378257.0     10.0      test2()
    22
    23         1            2      2.0      0.0      return total
```

hits（执行次数） 和 time（耗时） 值高的地方是有比较大优化空间的地方。

使用API(推荐)

第一种方法是通过命令行分析，其实你还可以通过API来分析，line_profiler提供了和cProfile类似的API，

Code Example:

```python
import line_profiler
import sys


# 上面的test函数
prof = line_profiler.LineProfiler(test)
prof.enable()  # 开始性能分析
test()
prof.disable()  # 停止性能分析
prof.print_stats(sys.stdout)
```


```sh

Timer unit: 1e-07 s

Total time: 5.9848 s
File: test.py
Function: test at line 280

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
   280                                           def test():
   281         1         33.0     33.0      0.0      total = 0
   282     40001     330769.0      8.3      0.6      for i in range(40000):
   283     40000     363873.0      9.1      0.6          total += i
   284
   285         1   45941592.0 45941592.0     76.8      t1 = sum_num(10000000)
   286         1     813287.0 813287.0      1.4      t2 = sum_num(200000)
   287         1    1507709.0 1507709.0      2.5      t3 = sum_num(300000)
   288         1    1931633.0 1931633.0      3.2      t4 = sum_num(400000)
   289         1    2450905.0 2450905.0      4.1      t5 = sum_num(500000)
   290         1    6508182.0 6508182.0     10.9      test2()
   291
   292         1         27.0     27.0      0.0      return total
```





### memoryprofiler

类似于"lineprofiler"对基于行分析程序内存使用情况的模块。[memory_profiler github](https://github.com/fabianp/memory_profiler) 。ps:安装 `psutil`, 会分析的更快。

同样是上面"lineprofiler"中的代码，运行 `python -m memory_profiler profile.py` 命令生成结果如下:

```
➜ python -m memory_profiler profile.py
Filename: profile.py

Line #    Mem usage    Increment   Line Contents
================================================
    10   24.473 MiB    0.000 MiB   @profile
    11                             def test():
    12   24.473 MiB    0.000 MiB       total = 0
    13   25.719 MiB    1.246 MiB       for i in range(40000):
    14   25.719 MiB    0.000 MiB           total += i
    15
    16  335.594 MiB  309.875 MiB       t1 = sum_num(10000000)
    17  337.121 MiB    1.527 MiB       t2 = sum_num(200000)
    18  339.410 MiB    2.289 MiB       t3 = sum_num(300000)
    19  342.465 MiB    3.055 MiB       t4 = sum_num(400000)
    20  346.281 MiB    3.816 MiB       t5 = sum_num(500000)
    21  356.203 MiB    9.922 MiB       test2()
    22
    23  356.203 MiB    0.000 MiB       return total
```


## 后记
To be continued


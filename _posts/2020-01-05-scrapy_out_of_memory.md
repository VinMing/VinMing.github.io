---
layout:     post
title:      Scrapy Out Of Memory
subtitle:    "\"scrapy out of memory\""
date:       2020-01-05
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Scrapy
---
## 前言

Scrapy这个爬虫框架的的内存泄露问题就是一个很让人头疼的问题。

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
     None
```

## 正文

历来OOM(OOM - Out of  Memory，内存溢出)问题都是项目里最棘手的问题，这种问题debug的难度很大，原因在于问题不太好定位。因为OOM的成因往往比较复杂，不合理的对象创建，数据结构使用的不合理性，分布式架构中各系统的配合不好等情况，都有可能出现这个问题。

 而爬虫这个任务，涉及网站各个页面的遍历，通常会在网站抓去期间产生大量的HTTP  Request，而Request的处理往往是通过任务队列来处理的。由于不是网站的所有的页面都要抓去，但我们几乎需要遍历大部分的页面，再考虑并发的情况，爬虫任务开始后，往往会有大量的pending  request进入队列。而这些队列，框架对其最常见的处理方式是放在内存中，因此，当要抓去的页面层次位于网站的较深层时，这个队列的内存占用到了任务的中后期会变得非常可观。以我这次碰到的情况看，在用完我所有想到的能用到的方法优化内存使用以后，在抓到将近20w条数据的时候，队列里pending request数量已经占到了差不多40w个。此时消耗了差不多1.7G内存，而我整个虚拟机也只有2G内存。

 需要说明的一点是，这个数值，根据不同的网站，爬虫写法的不同，会有不同的值。这里只是我的项目中的数据。在没有做任何优化之前，基本上跑到几千甚至1w条数据的时候，爬虫就吃光内存被ubuntu强制杀掉了。Scrapy的官方文档也提到过memory leak的debug方法，大家可以参考Scrapy文档中的Debugging memory leaks一节。官方文档给了比较详细的说明，  详情参考 Scrapy文档 - 调试内存溢出



 那么，Scrapy会造成OOM的原因是否只有request？答案是不是。这个项目里面使用了django的orm作为数据操作的框架。可以说，之前吃内存太快的原因，主要不在于Scrapy的pending request膨胀得太快，而是django带来的坑。

 因为我们要抓取的数据量也是很大的，因此也会有大量的数据会往数据库里写，同时，也会有很多数据被读取。有时候为了避免过多的数据库io，我们会将常用的数据留在内存中，需要的时候直接取用。但很不幸的是，django的orm也会考虑同样的问题。当我们调用django  orm的QuerySet接口查询的时候，django会把数据缓存。这样，同样的东西会保持2份（一份是我们自己维护的内存缓存，一份是orm自己的session缓存）。只有使用QuerySet的iterator方法来迭代输出的时候，django orm才不会有缓存行为，这是django官方文档中的解释。

但是，事情并不会如此简单，在开发阶段，我们往往会把框架的debug参数设为True以便于debug的时候做profile和异常定位。但是在开发阶段用debug=True的配置来跑程序的时候，orm永远都会缓存，因此在这种配置下，依然无法避免内存泄露的问题。另一个坑就是，像MySQL-Python（MySQLdb）和Psycopg2这样的数据库驱动，都有客户端缓存，因此，数据库驱动也会保存一份缓存，这样内存中就是3份缓存，如果数据量大了，内存开销的增长速度会相当可观。因此，在有需要缓存的数据的查询的时候，要避免把所有的东西查出来，只取出自己需要的字段就好。

 django的QuerySet有values和value_list方法来保证只取到需要的字段，另一个办法就是自己操作数据库连接，写SQL来获取自己需要的字段。前一种方法是较好的方式。其实这个问题对于其他的orm也会存在，因此也是相当的具有参考性。

### 总结

当你在scrapy遇到内存泄露的问题的时候应该检查以下情况：

 1.Scrapy的任务队列中的请求数是否过多？

  如果是，那么你应该看看自己的url抓取的rule是否应该优化，将完全没有必要访问的页面都剔除掉，不抓取，不访问。查看pending  request数量的方法请参看Scrapy的telnet console说明和文档中debugging memory leaks这一节。

 2.orm的写法是否使用了缓存，如果是，请寻找不缓存的查询方法，或者定时手动清理orm缓存。

 3.看看数据库驱动的特性里是否存在缓存结果的特性，是的话，可以看看能否关闭并在查询的时候只查出自己需要的字段，不要查出所有字段。


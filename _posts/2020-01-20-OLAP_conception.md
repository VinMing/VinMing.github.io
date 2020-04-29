---
layout:     post
title:      OLAP 概念
subtitle:    "\"OLAP 概念\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - OLAP

---
## 前言

本文主要介绍OLAP的起源、核心概念和当前发展趋势。主要面对OLAP入门同学，仅供学习交流
OLAP：Online analytical processing　联机分析处理
## 正文

### OLAP 起源
OLAP(Online analytical processing)，即联机分析处理，主要用于支持企业决策管理分析。数据库概念最初源于1962年Kenneth Iverson发表的名为“A Programming Language” (APL)的著作，它第一次提出了处理操作和多维变量的的数学表达式，后来APL语言由IBM实现。

简单一句话：OLAP给企业决策者管理分析

随后数据库之父Edgar F. Codd提出了著名的关系数据模型理论《[A Relational Model of Data for Large Shared Data Banks ](https://www.seas.upenn.edu/~zives/03f/cis550/codd.pdf)》，为后面数据库发展奠定基础。

第一款OLAP产品Express于1975年问世，随着被Oracle收购后繁荣发展了30余年，最后由继任者Oracle 9i替代。这么多年过去，基本的OLAP理念和数据模型仍然未变。

OLAP这个名词是数据库之父Edgar F. Codd于1993年在文章《Providing OLAP (On-Line Analytical Processing) to User-Analysts: An IT Mandate》提出，他总结了OLAP产品的12个原则，随后OLAP产品相继问世并逐渐形成今天的格局。
### OLAP核心概念
#### 基本概念
##### １．维
- 维(Dimension)：人们观察事物的视角，如时间、地理位置、年龄和性别等，是单一角度概念。

- 维的层次(Lever of Dimension)：表示维度概念基础上进一步的细分，如时间可以细分为年、季度、月三个层次。

- 维成员(Member of Dimension)：表示维不可再细分的原子取值，如时间维的成员可以是2019年1月10日。

- 度量(Measure)：表示在这个维成员上的取值。
除了维的基本概念，还有多维分析的分析操作。
##### ２． 操作
- 下探(Drill down)：维度是有层次的，下探表示进入维度的下一层，将汇总数据拆分到下一层所在细节数据信息，如下图从第二季度下探到看4、5、6月的明细数据。

- 上钻(Drill up)： 下探的反向操作，回到更高汇聚层的汇总数据。

- 切片(Slice)：切片可以理解成把立体按某一个维度进行切分，就可以看两维数据，如图中按电子产品切分，看到的是时间和地理位置关系的二维数据。

- 切块(Dice)：相对于切片是按一个点切分，切块就是按一个范围(区间)来做切分。

- 旋转(Pivot)：维的行列位置交换，换一个视角分析数据。

![Image text](/img/OLAP_conception_whole.png)

![Image text](/img/OLAP_conception_spilt.png)

#### OLAP分类
OLAP按存储器的数据存储格式分为ROLAP、MOLAP和HOLAP。
```tex
MOLAP（Multi-dimensional OLAP）
以多维数组（Multi-dimensional Array）存储模型的OLAP，是OLAP发源最初的形态，某些方面也等同于OLAP。它的特点是数据需要预计算（pre-computaion），然后把预计算之后的结果（cube）存在多维数组里。
	优点：
		cube包含所有维度的聚合结果，所以查询速度非常快。
		计算结果数据占用的磁盘空间相对关系型数据库更小
	缺点：
		空间和时间开销大。update cube的时间跟计算维度（degree）相关，			随着维度增加计算时间大幅增加，此外预计算还会造成数据库占用急剧	膨胀。
		查询灵活度比较低。需要提前设计维度模型，查询分析的内容仅限于这些		指定维度，增加维度需要重新计算。
ROLAP（Relational OLAP）
基于关系模型存放数据，一般要求事实表（fact table）和维度表（dimensition table）按一定关系设计，它不需要预计算，使用标准SQL就可以根据需要即时查询不同维度数据。
	优点
		扩展性强，适用于维度数量多的模型，MOLAP对于维度多的模型预计算慢，空间占用大。
		更适合处理non-aggregate事实，例如文本描述
		基于row数据更容易做权限管理
	缺点
		因为是即时计算，查询响应时间一般比预计算的MOLAP长。
HOLAP
业界还没有一致的定义，它是MOLAP和ROLAP类型的混合运用，细节的数据以ROLAP的形式存放，更加方便灵活，而高度聚合的数据以MOLAP的形式展现，更适合于高效的分析处理。公司使用HOLAP的目的是根据不同场景来利用不同OLAP的特性。
```
#### OLAP业界产品
- MOLAP 产品有 Cognos Powerplay, Oracle Database OLAP Option, MicroStrategy, Microsoft Analysis Services, Essbase, TM1, Jedox ,icCube和kylin等。
- ROLAP产品有Vertica、Amazon Redshift、Google Dremel、Hulu Nesto、Presto、Druid、Impala、Greenplum、HAWQ和Doris等。

#### 当前OLAP的发展状态
在国内，不论传统公司还是互联网公司，都开始利用OLAP技术分析挖掘大数据的价值，国内除BAT等大厂会自研OLAP产品外，其他中小互联网公司普遍拥抱开源，会使用Kylin、Presto、impala、Druid和Greenplum等开源技术来实现OLAP分析查询业务。

开源OLAP产品可以进一步分类作为技术选型参考：

- MOLAP：Kylin、Druid（其中druid用于实时在线分析场景）
- ROLAP：Presto、impala （都是基于MPP架构的OLAP分析框架）


## 后记

@[TOC](这里写自定义目录标题)

LOAP的来龙去脉：https://esc.fnwi.uva.nl/thesis/centraal/files/f347829190.pdf



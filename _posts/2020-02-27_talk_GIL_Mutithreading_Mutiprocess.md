---
layout:     post
title:      浅谈 python GIL、多线程、多进程
subtitle:    "\"浅谈 python GIL、多线程、多进程\""
date:       2020-02-27
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - python
	- GIL

---




## 前言
说起python的多线程、多进程，首先要想到的GIL全局解释器锁。

在看Python的多线程，经常我们会听到老手说：“python下多线程是鸡肋，推荐使用多进程！”，但是为什么这么说呢？


## 正文
首先强调一下概念：
1、**GIL是什么？**GIL的全称是Global Interpreter Lock(全局解释器锁)，来源是python设计之初的考虑，为了数据安全所做的决定。

2、每个CPU在同一时间只能执行一个线程（在单核CPU下的多线程其实都只是并发，不是并行，并发和并行从宏观上来讲都是同时处理多路请求的概念。但并发和并行又有区别，并行是指两个或者多个事件在同一时刻发生；而并发是指两个或多个事件在同一时间间隔内发生。）

## 后记

@[TOC](这里写自定义目录标题)


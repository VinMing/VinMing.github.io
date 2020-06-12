---
layout:     post
title:      Python MetaClass
subtitle:    "\"python metaclass\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python

---
## 前言

Python中的元类

Python一切皆对象，所以类也是对象。

我们知道，对象是通过类实例化创建出来的。但我们创建类时并没有进行实例化操作，为什么类也是对象呢？

类既然是对象，类肯定是另外某个类的实例。这说明在我们使用class声明一个类的时候Python解释器为我们做了些什么。

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

#### 一、元类介绍

通过一个类，可以创建无数个实例对象，类就相当于一个生产实例对象的机器，可以不断的生产出实例对象。

在Python中，类是通过元类来创建的。元类就是用来创建类的类，如果类是一个机器，那么元类就是可以生产机器的机器。
```python
class Job(object):
    pass


j = Job()
print(j)
print(j.__class__)
print(Job)
print(Job.__class__)
print(type(Job))
```
运行结果：
```sh
<__main__.Job object at 0x7f92d9acf210>
<class '__main__.Job'>
<class '__main__.Job'>
<class 'type'>
<class 'type'>

```
通过__class__属性，我们可以找到一个对象所属的类。

在上面的代码中，我们创建了一个Job类，并实例化了一个Job的实例对象j。打印j对象，j是一个Job对象，打印j.__class__，可以知道j是Job类的对象。我们再打印Job，Job是一个类，打印Job.__class__，我们看到Job是type类的对象。type函数通常用来查看一个变量或对象的类型，所以我们继续打印type(Job)，发现Job类的类型也是type。

这说明，我们创建的Job类是type的实例对象。因为type函数实际上是一个元类。元类是制造类的工厂，是一个类。

#### 二、Python中常见的内置类

python中定义了很多的内置类，我们看一下这些内置类都是哪个类的实例。
```python
str_a = 'python'
str_b = str('python')
print('str_a', str_a)
print('str_a', str_a.__class__)
print('str_a', str.__class__)
print('str_b', str_b)
print('str_b', str_b.__class__)
print('str_b', str.__class__)

print('-' * 20)
list_a = [x for x in range(5)]
list_b = list([x for x in range(5)])
print('list_a', list_a)
print('list_a', list_a.__class__)
print('list_a', list.__class__)
print('list_b', list_b)
print('list_b', list_b.__class__)
print('list_b', list.__class__)
```
运行结果：
```sh
str_a python
str_a <class 'str'>
str_a <class 'type'>
str_b python
str_b <class 'str'>
str_b <class 'type'>
--------------------
list_a [0, 1, 2, 3, 4]
list_a <class 'list'>
list_a <class 'type'>
list_b [0, 1, 2, 3, 4]
list_b <class 'list'>
list_b <class 'type'>
```
我们例举了直接声明定义一个字符串变量和通过类的方式来实例化一个字符串，直接声明定义一个列表变量和通过类的方式来实例化一个列表。两种方式的结果是一样的，打印字符串的__class__属性，他们都是str类的对象，打印列表的__class__，他们都是list类的对象。

首先，这说明str和list都是类，其次，在我们平时定义字符串或列表时，str和list的作用是一个创建字符串或列表的“机器”。

其实，str、list、int、tuple这些Python中的数据类型关键字都是类，我们创建一个变量就是实例化一个变量对象。

我们在打印str.__class__和list.__class__，发现他们都是type类的对象。

在Python中，当我们创建一个类的时候，创建的这个类就是type的对象。这包括整数、字符串、函数以及类 。它们全部都是对象，⽽且它们都是从⼀个类创建⽽来，这个类就是type。

#### 三、type和object

type是Python在背后⽤来创建所有类的元类。注意，这里说的是所有类，自定义的类，内置类，还有Python标准库中一些我们不会直接使用的其他元类，就连最基类object也是，同时，Python为了避免无限回溯，创建type类自己的元类也是type。
```python
    print(object.__class__)
    print(type.__class__)
     
    print(type.__mro__)
    print(object.__mro__)
```
运行结果：
```sh
<class 'type'>
<class 'type'>
(<class 'type'>, <class 'object'>)
(<class 'object'>,)

```
我们知道，所有的类都默认继承了object类。不过，现在说的是类对象属于哪个类，类对象是由哪个类创建的，这与继承是不干扰的。object类是type类的对象，type类继承自object类，关系如图。

这两张图描述了类的继承关系和实例化关系。

1.所有类都继承自object类，type元类、str等内置类、我们自定义的Job类全都是，当然object类没有父类，它已经是最基类。

2.所有类都是type类的实例，object类、str等内置类、我们自定义的Job类全都是，就连type类自己都是自己的实例，无一例外。

object类和type类之间的这种关系很独特：object是type的实例，而type是object的子类。这种关系很“神奇”，因为定义其中一个之前另一个必须存在。type是自身的实例这一点也很“神奇”，不过这是Python面向对象最初的实现。

#### 四、自定义元类

除了type元类，在Python标准库中还有其他的元类，也就是说不止一个元类。

不过，所有的元类都是从type类继承了构建类的能力。也就是说这些元类既是type的子类，也是type的实例。
```python
from abc import ABCMeta

print(ABCMeta.__class__)
print(ABCMeta.__mro__)
```
运行结果：
```sh
<class 'type'>
(<class 'abc.ABCMeta'>, <class 'type'>, <class 'object'>)
```
我们要抓住的重点是，所有类都是type的实例，但是元类还是type的子类，因此可以作为制造类的工厂。

所以我们可以尝试自定义元类。
```python
class OurMetaClass(type):

    def __new__(cls, *args):
        print('Our meta class!')
        return super(OurMetaClass, cls).__new__(cls, *args)


class Study(object, metaclass=OurMetaClass):
    item = 'English'

    def score(self):
        return 100


print(OurMetaClass.__class__)
print(OurMetaClass.__mro__)
s = Study()
print(s.item)
print(s.score())
print(s)
print(s.__class__)
print(Study.__class__)
```
运行结果：
```sh
Our meta class!
<class 'type'>
(<class '__main__.OurMetaClass'>, <class 'type'>, <class 'object'>)
English
100
<__main__.Study object at 0x7f29f13c4d50>
<class '__main__.Study'>
<class '__main__.OurMetaClass'>
```
我们定义了一个OurMetaClass类，这个类继承了type类，然后重写了type的__new__方法。在定义另一个类Study类的时候，指定metaclass=OurMetaClass，这时候OurMetaClass是一个元类，Study类是OurMetaClass的实例。

我们可以正常使用Study类中的属性和方法，可以正常实例化一个Study类的对象s，s是一个对象，s是Study类的实例，而Study类是OurMetaClass类的实例。

#### 五、不要轻易自定义元类
框架和库会使用元类协助程序员执行很多任务，例如：验证属性、一次把装饰器依附到多个方法上、序列化对象或转换数据、对象关系映射、基于对象的持久存储、动态转换使用其他语言编写的类结构。

元类功能强大，但是难以掌握。除非开发框架，否则不要编写元类。

当然，为了学习相关的概念，可以这么做。



## 后记

@[TOC](这里写自定义目录标题)



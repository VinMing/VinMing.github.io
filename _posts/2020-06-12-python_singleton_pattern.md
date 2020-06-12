---
layout:     post
title:      Python Singleton Pattern
subtitle:    "\"python singleton pattern\""
date:       2020-06-12
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python
---
## 前言

单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

**注意：**

- 1、单例类只能有一个实例。
- 2、单例类必须自己创建自己的唯一实例。
- 3、单例类必须给所有其他对象提供这一实例。

本文用以python 为语言通过**装饰器、实例化、类装饰器、重写类的\__new__方法、元类**这五种方法实现单例

## 正文

### 介绍

**意图：**保证一个类仅有一个实例，并提供一个访问它的全局访问点。

**主要解决：**一个全局使用的类频繁地创建与销毁。

**何时使用：**当您想控制实例数目，节省系统资源的时候。

**如何解决：**判断系统是否已经有这个单例，如果有则返回，如果没有则创建。

**关键代码：**构造函数是私有的。

**应用实例：**

- 1、一个班级只有一个班主任。
- 2、Windows 是多进程多线程的，在操作一个文件的时候，就不可避免地出现多个进程或线程同时操作一个文件的现象，所以所有文件的处理必须通过唯一的实例来进行。
- 3、一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。

**优点：**

- 1、在内存里只有一个实例，减少了内存的开销，尤其是频繁的创建和销毁实例（比如管理学院首页页面缓存）。
- 2、避免对资源的多重占用（比如写文件操作）。

**缺点：**没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。

**使用场景：**

- 1、要求生产唯一序列号。 

- 2、WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。

- 3、创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。

  

**简单的一句话**：在面向对象编程中，通过单例模式只能创建一个类实例，也就是一个类永远只有一个实例对象。

在工作中，为了确保某一个类只会创建出一个实例，就需要使用单例模式。



###  Python 实现单例的方法

#### 一、通过装饰器的方式实现单例
```python
def singleton_func(cls):
    instances = {}

    def _singleton(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]

    return _singleton


@singleton_func
class Phone(object):

    def phone_id(self):
        return id(self)


p1 = Phone()
p2 = Phone()
print(p1.phone_id())
print(p2.phone_id())
```
运行结果：
``` sh
140188653591952
140188653591952
```
通过装饰器来实现单例，直接用装饰器装饰类，实例化两遍类对象，对象的id是相等的，说明并没有两个实例对象，只是两个变量指向一个单例。如果注释掉上面的装饰器，则创建的是两个不同的实例（id不相等）。

在 Python 中，一切皆对象，所以字典中的数据也是一个个的对象。

在装饰器的内函数中，判断字典是否已经有键值对。如果没有，则添加一个类和类实例的键值对，如果有，则不添加。最后返回字典中的类实例，可以保证每次返回的都是同一个实例。

要使用这个单例装饰器，只要将其装饰到需要实现单例的类上即可。

在单例的多种实现方式中，个人最推荐这种方式，因为装饰器的使用方式即方便又优雅。我们可以将以上装饰器写在一个py文件中，所有需要使用单例的地方，都可以使用，步骤就两个，导入，装饰，如果不需要单例了，注释掉装饰器即可。

详细的装饰器介绍和用法，请参考：[Python Decorator Closure]()

二、使用\__call__实例化方式实现单例
``` python
class SingletonInstance(object):

    def __call__(self, *args, **kwargs):
        return self

SingletonInstance = SingletonInstance()
s1 = SingletonInstance()
s2 = SingletonInstance()
print(id(s1))
print(id(s2))
```
运行结果：
``` sh
140290857926608
140290857926608
```
在类中，先重写类的\_\_call\_\_方法，在 \\_\_call\_\_ 方法中返回自己。先实例化一个类的对象，后面所有需要使用这个类的地方，都调用这个实例对象。这样，每次调用的都是同一个实例，所以也能实现单例。

其实 Python 中的模块默认是单例模式的，在其他py文件中导入这个实例，然后使用，也是满足单例模式的。

三、使用类装饰器实现单例
```python
class SingletonDecorator(object):
    _instance = None

    def __init__(self, cls):
        self._cls = cls

    def __call__(self, *args, **kwargs):
        if self._instance is None:
            self._instance = self._cls(*args, **kwargs)
        return self._instance


@SingletonDecorator
class Phone(object):

    def phone_id(self):
        return id(self)


p1 = Phone()
p2 = Phone()
print(p1.phone_id())
print(p2.phone_id())
```
运行结果：
``` sh
140122539760656
140122539760656
```
使用装饰器实现单例，因为装饰器除了可以使用闭包实现，还可以使用类实现，所以也可以使用类装饰器来实现单例。

通过重写类的 _\_call__ 方法实现类装饰器，在 _\_call__ 方法中判断当前是否有类实例，没有才会创建，从而实现单例。

四、重写类的 _\_new__ 方法实现单例
``` python
class SingletonClass(object):
    _instance = None

    def __new__(cls, *args, **kwargs):
        if cls._instance is None:
            cls._instance = super(SingletonClass, cls).__new__(cls)
        return cls._instance

    _is_init = False

    def __init__(self):
        if self._is_init is False:
            print('-*-')
            self._is_init = True


s1 = SingletonClass()
s2 = SingletonClass()
print(id(s1))
print(id(s2))

```
运行结果：
```
-*-
139948101446672
139948101446672
```
在 Python 类中，每次实例化一个类对象时，都会自动先执行 __new__ 方法和 __init__ 方法。 __new__ 方法先在内存中为实例对象申请空间，然后 __init__ 方法初始化实例对象。

通过重写 __new__ 方法，如果这个类没有实例对象，则执行 __new__ 方法，有则返回已有的实例，这样就实现了单例。

但是，因为 __init__ 方法是在 __new__ 执行完成后自动执行的，每次实例类的对象时都会执行 __init__ 方法，所以也要对 __init__ 方法进行重写，只有第一次实例化类对象时才执行初始化操作。如果上面代码中不重写 __init__ 方法，则会执行两遍__init__ 方法。

五、通过元类实现单例
``` python
class SingletonMeta(type, object):

    def __init__(self, *args, **kwargs):
        self._instance = None
        super(SingletonMeta, self).__init__(*args, **kwargs)

    # _instance = None

    def __call__(self, *args, **kwargs):
        if self._instance is None:
            self._instance = super(SingletonMeta, self).__call__(*args, **kwargs)
        return self._instance


class Phone(object, metaclass=SingletonMeta):

    def phone_id(self):
        return id(self)


p1 = Phone()
p2 = Phone()
print(p1.phone_id())
print(p2.phone_id())

```
运行结果：
``` sh
139846757211408
139846757211408
```
在 Python 中，元类是创建类的类，是创建类的工厂，所有类的元类都是 type 类，所有类都是 type 类的实例对象。

详细的元类请参考:[Python MetaClass]()

如果自定义一个元类，在元类中重写 \_\_call\_\_ 方法，判断当前是否有实例，没有实例才创建，有则不创建。对需要实现单例的类，指定类的元类是我们自定义的元类，从而实现单例。

不过，不推荐使用这种方式。
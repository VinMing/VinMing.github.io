---
layout:     post
title:      Python Decorator Closure
subtitle:    "\"python decorator closure\""
date:       2020-06-12
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python
---
## 前言

在Python中，装饰器是在不改变已有函数的代码的前提下，给函数增加新的功能的一种函数。

装饰器接收一个函数作为参数，返回值也是一个函数。

在Python中，实现装饰器的方式叫做闭包。

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
### 一、闭包
#### 1. 闭包的介绍
闭包是指函数中定义了一个内函数，内函数里运用了外函数的临时变量，并且外函数的返回值是内函数的引用。
#### ２. 闭包的实现
闭包的使用，可以隐藏内部函数的工作细节，只给外部使用者提供一个可以执行的内部函数的引用。
```python
# 闭包

def outer_func():
    a = 1

    def inner_func(b):
        nonlocal a
        a += b
        print(a)
        return a

    return inner_func


func = outer_func()
func(100)

print(outer_func()(1000))

```
运行结果：
```sh
101
1001
1001
```
闭包的重点：

1.外函数的内部定义了一个内函数

2.内部函数使用了外部函数的临时变量

3.外函数的返回值是内函数的引用

在上面的代码中，外函数是outer_func,在他的内部有内函数inner_func，a是外函数的临时变量，在内函数中使用了a,外函数的返回值是内函数inner_func。

如果在内部函数中直接使用外部函数的变量a时，不改变a的值，直接使用就可以了，如果要修改a的值，需要将变量声明为 nonlocal。

内部函数可以接收参数，然后在内函数里使用参数。

使用func接收outer_func()的返回值，返回的值func是一个函数，相当于inner_func，给func()传入参数并执行，则会运行inner_func中的代码。

也可以不使用变量来接收，而是直接在outer_func()后面直接传参和执行，后面有两个小括号:outer_func()(),第二个括号中传入内函数的参数。

### 二、装饰器
#### 1. 装饰器的介绍
装饰器是通过闭包的方式实现的，外函数接收一个函数作为外函数的临时变量，然后在内函数中执行这个函数。

内函数将需要的参数接收进来并传给执行的函数，然后将执行结果返回。在内函数中，可以添加额外的功能的代码，这些额外的功能就是装饰器添加的功能。

最后外函数将内函数返回。

使用装饰器来装饰函数时，在被装饰的函数的前一行，使用 @装饰器函数名 形式来装饰，则函数的本身的功能正常实现，装饰器中添加的功能也实现了。如上面代码中打印被装饰函数的函数名。
#### 2. 装饰器的实现
```python
def func_name(func):
    def wrapper(*args, **kwargs):
        print("func name is {}".format(func.__name__))
        return func(*args, **kwargs)

    return wrapper


@func_name
def func_one():
    print('this is function one')


@func_name
def func_two():
    print('this is function two')


func_one()
func_two()
```
运行结果：
```sh
func name is func_one
this is function one
func name is func_two
this is function two
```


#### 3、多个装饰器同时装饰一个函数

```python
def decorator_one(func):
    def wrapper(*args, **kwargs):
        print('decorator one start')
        result = func(*args, **kwargs)
        print('decorator one end')
        return result

    return wrapper


def decorator_two(func):
    def wrapper(*args, **kwargs):
        print('decorator two start')
        result = func(*args, **kwargs)
        print('decorator two end')
        return result

    return wrapper


@decorator_two
@decorator_one
def hello_python():
    print('Hello Python！')


hello_python()
```
运行结果：
```sh
decorator two start
decorator one start
Hello Python！
decorator one end
decorator two end
```
可以看到，当多个装饰器装饰同一个函数时，会是一个嵌套的装饰结果，也就是说，先执行完离函数近的一个装饰器，然后再用离函数远的装饰器来装饰执行结果。

#### 4、万能装饰器

装饰器的外函数会接收一个函数作为参数，这个函数在内函数内部执行，这个函数可以有参数也可以没有参数，可以有返回值也可以没有返回值。

所以装饰器也分为四类，无参无返回值、无参有返回值、有参无返回值、有参有返回值。是否有参数和返回值完全取决于被装饰的函数。

但是，我们写装饰器的目的就是用一个装饰器来装饰不同的函数，所以要考虑装饰器的通用性。我们通过可变参数来实现一种可以用来装饰任何函数的装饰器，万能装饰器。
```python
def decorator_all(func):
    def wrapper(*args, **kwargs):
        print('add some coding')
        return func(*args, **kwargs)

    return wrapper
```
使用这种装饰器，可以用来装饰任意函数，不管函数是否有参数，是否有返回值。

#### 5、类装饰器

在Python中，也可以通过类的方式来实现装饰器，通过使用 \_\_init_\_ 和 _\_call\_\_方法来实现。
```python
class DecoratorClass(object):

    def __init__(self, func):
        self._func = func

    def __call__(self, *args, **kwargs):
        print('class decorator')
        return self._func(*args, **kwargs)


@DecoratorClass
def func_three():
    print('this is function three')


func_three()
```
运行结果：
```sh
class decorator
this is function three
```
在实现类装饰器的时候，使用\_\_init\_\_()方法来接收被装饰函数，使用\_\_call_\_()方法来添加装饰器要实现的功能，并在\_\_call__()方法中执行和返回被装饰函数。

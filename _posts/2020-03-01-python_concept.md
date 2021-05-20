---
layout:     post
title:      Python Concept
subtitle:    "\"Python concept\""
date:       2020-03-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python
---
## 前言



## 环境
#### 系统环境
```text
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.4 LTS
Release:	18.04
Codename:	bionic
Linux version :       5.3.0-46-generic ( buildd@lcy01-amd64-013 ) 
Gcc version:         7.5.0  ( Ubuntu 7.5.0-3ubuntu1~18.04 )

Distributor ID:	Deepin
Description:	Deepin 20 Beta
Release:	20 Beta
Codename:	n/a
Linux version :     5.3.0-3-amd64 (debian-kernel@lists.debian.org)
Gcc version:        8.3.0 (Debian 8.3.0-6)
Debian Version     10.4
```


#### 软件信息
```text
version : 	
     
```

## 正文

#### Q1、Python中的列表和元组有什么区别？

定义不同

列表[]

元组()

操作不同

​	列表可以排序、替换、添加

​	元组只能合并、遍历

#### Q2、Python的主要功能是什么？
Python是一种解释型语言。与C语言等语言不同，Python不需要在运行之前进行编译。Python是动态语言，当您声明变量或类似变量时，您不需要声明变量的类型。

Python适合面向对象的编程，因为它允许类的定义以及组合和继承。Python没有访问说明（如C ++的public，private）。在Python中，函数是第一类对象。它们可以分配给变量。类也是第一类对象编写Python代码很快，但运行比较慢。

Python允许基于C的扩展，例如numpy函数库。Python可用于许多领域。Web应用程序开发，自动化，数学建模，大数据应用程序等等。它也经常被用作“胶水”代码。

#### Q3、Python是通用编程语言吗？
Python能够编写脚本，但从一般意义上讲，它被认为是一种通用编程语言。

#### Q4、Python是如何解释语言的？
Python在运行之前不需要对程序进行解释。因此，Python是一种解释型语言。

#### Q5、什么是pep？
PEP代表Python Enhancement Proposal。它是一组规则，指定如何格式化Python代码以获得最大可读性。

#### Q6、如何在Python中管理内存？
python中的内存管理由Python私有堆空间管理。所有Python对象和数据结构都位于私有堆中。程序员无权访问此私有堆。python解释器负责处理这个问题。

Python对象的堆空间分配由Python的内存管理器完成。核心API提供了一些程序员编写代码的工具。Python还有一个内置的垃圾收集器，它可以回收所有未使用的内存，并使其可用于堆空间。

#### Q7、Python中的命名空间是什么？
命名空间是一个命名系统，用于确保名称是唯一性，以避免命名冲突。

#### Q8、什么是PYTHONPATH？
它是导入模块时使用的环境变量。每当导入模块时，也会查找PYTHONPATH以检查各个目录中是否存在导入的模块。解释器使用它来确定要加载的模块。

#### Q9、什么是python模块？Python中有哪些常用的内置模块？
Python模块是包含Python代码的.py文件。此代码可以是函数类或变量。一些常用的内置模块包括：sys、math、random、data time、JSON。、

#### Q10、Python中的局部变量和全局变量是什么？
全局变量：在函数外或全局空间中声明的变量称为全局变量。这些变量可以由程序中的任何函数访问。

局部变量：在函数内声明的任何变量都称为局部变量。此变量存在于局部空间中，而不是全局空间中。

#### Q11、python是否区分大小写？
是。Python是一种区分大小写的语言。

#### Q12、什么是Python中的类型转换？
类型转换是指将一种数据类型转换为另一种数据类型。

int（）  - 将任何数据类型转换为整数类型
float（）  - 将任何数据类型转换为float类型
ord（）  - 将字符转换为整数
hex（） - 将整数转换为十六进制
oct（）  - 将整数转换为八进制
tuple（） - 此函数用于转换为元组。
set（） - 此函数在转换为set后返回类型。
list（） - 此函数用于将任何数据类型转换为列表类型。
dict（） - 此函数用于将顺序元组（键，值）转换为字典。
str（） - 用于将整数转换为字符串。
complex（real，imag）  - 此函数将实数转换为复数（实数，图像）数。
#### Q13、如何在Windows上安装Python并设置路径变量？
要在Windows上安装Python，请按照以下步骤操作：从以下链接安装python：https：//http://www.python.org/downloads/下载之后，将其安装在您的PC上。在命令提示符下使用以下命令查找PC上安装PYTHON的位置：cmd python。

然后转到高级系统设置并添加新变量并将其命名为PYTHON_NAME并粘贴复制的路径。查找路径变量，选择其值并选择“编辑”。

如果值不存在，请在值的末尾添加分号，然后键入％PYTHON_HOME％

#### Q14、python中是否需要缩进？
缩进是Python必需的。它指定了一个代码块。循环，类，函数等中的所有代码都在缩进块中指定。通常使用四个空格字符来完成。

如果您的代码没有必要缩进，它将无法准确执行并且也会抛出错误。

#### Q15、Python数组和列表有什么区别？
Python中的数组和列表具有相同的存储数据方式。但是，数组只能包含单个数据类型元素，而列表可以包含任何数据类型元素。

#### Q16、Python中的函数是什么？
函数是一个代码块，只有在被调用时才会执行。要在Python中定义函数，需要使用def关键字。

#### Q17、什么是init?
init是Python中的方法或者结构。在创建类的新对象/实例时，将自动调用此方法来分配内存。所有类都有init方法。

#### Q18、什么是lambda函数？
lambda函数也叫匿名函数，该函数可以包含任意数量的参数，但只能有一个执行操作的语句。

#### Q19、Python中的self是什么？
self是类的实例或对象。在Python中，self包含在第一个参数中。

但是，Java中的情况并非如此，它是可选的。它有助于区分具有局部变量的类的方法和属性。init方法中的self变量引用新创建的对象，而在其他方法中，它引用其方法被调用的对象。

#### Q20、区分break，continue和pass？

break 跳出 多层循环

continue 跳出当前循环

pass 暂位符号

#### Q21、[:: - 1}表示什么？

[:: - 1]用于反转数组或序列的顺序。

#### Q22、如何在Python中随机化列表中的元素？
可以使用shuffle函数进行随机列表元素。

#### Q23、什么是python迭代器？
迭代器是可以遍历或迭代的对象。

#### Q24、如何在Python中生成随机数？
random模块是用于生成随机数的标准模块。该方法定义为：

random.random()方法返回[0,1]范围内的浮点数。 该函数生成随机浮点数。随机类使用的方法是隐藏实例的绑定方法。可以使用Random的实例来显示创建不同线程实例的多线程程序。

其中使用的其他随机生成器是：randrange(a,b)：它选择一个整数并定义[a，b]之间的范围。它通过从指定范围中随机选择元素来返回元素。它不构建范围对象

uniform(a,b)：它选择一个在[a，b)范围内定义的浮点数normalvariate(mean,sdev)：它用于正态分布，其中mean是平均值，sdev是用于标准偏差的sigma。

使用和实例化的Random类创建一个独立的多个随机数生成器。

#### Q25、range＆xrange有什么区别？
在大多数情况下，xrange和range在功能方面完全相同。

它们都提供了一种生成整数列表的方法，唯一的区别是range返回一个Python列表对象，x range返回一个xrange对象。这就表示xrange实际上在运行时并不是生成静态列表。

它使用称为yielding的特殊技术根据需要创建值。该技术与一种称为生成器的对象一起使用。因此如果你有一个非常巨大的列表，那么就要考虑xrange。




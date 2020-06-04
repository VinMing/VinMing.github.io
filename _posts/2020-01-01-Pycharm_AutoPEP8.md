---
layout:     post
title:      Pycharrm install AutoPEP8 Setting
subtitle:    "\"Pycharm install AutoPEP8 Setting\""
date:       2020-01-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Pycharm
    - AutoPEP8
---
## 前言
在学习和开发python的过程中，我们经常会遇到代码不规范而导致的程序报错和
新增**交接项目的代码**（别问这个原因我为什么要加强调）。代码的规范和严谨就显得尤为重要了，所以编写代码的过程中，我们需要遵循PE8规范，在Pycharm 中我们可以安装插件 AutoPEP8来效验我们的代码的规范性，并且它还有一个很特别的功能，就是当我们一些空行和锁紧之类的没有规范的话，它可以自动帮我们补齐。

## 环境
#### 软件信息
```text
version : 	
PyCharm 2019.2 (Community Edition)
Build #PC-192.5728.105, built on July 24, 2019
Runtime version: 11.0.3+12-b304.10 amd64
VM: OpenJDK 64-Bit Server VM by JetBrains s.r.o
Linux 5.3.0-3-amd64
GC: ParNew, ConcurrentMarkSweep
```

## 正文

### 1. 安装 AutoPEP8
```sh
pip install autopep8
```
### 2. 配置 AutoPEP8
将autopep8添加到Pycharm 的External Tools
File  ---> Settings----->Tools ---->External Tools 
![Image text](/img/pycharm_autopep8_setting.png)

点击添加
```tex
Name ：autopep8

Program ：autopep8

Arguments ：--in-place --aggressive --aggressive $FilePath$

Working directory ：$ProjectFileDir$

Output filters ：$FILE_PATH$\:$LINE$\:$COLUMN$\:.*
```

### 3. 使用
右键 -> External Tools -> autopep8
![Image text](/img/pycharm_autopep8_use.png)
便会帮我们自动调整格式，效果如下就没有波浪线了。
## 进阶
更多的PEP8规范，去看PEP的官方文档，[PEP８英文版](https://www.python.org/dev/peps/pep-0008/)

官方是英文的，有大神在CSDN把它翻译成中文版[PEP８中文版](https://blog.csdn.net/ratsniper/article/details/78954852)




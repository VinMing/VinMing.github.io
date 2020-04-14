---
layout:     post
title:     CMake错误No CMAKE_CXX_COMPILER could be found
subtitle:    "\"CMake错误No CMAKE_CXX_COMPILER could be found\""
date:       2020-04-02
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - CMake

---
## 环境
### 系统环境
```text
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.4 LTS
Release:	18.04
Codename:	bionic
Linux version :       5.3.0-46-generic ( buildd@lcy01-amd64-013 ) 
Gcc version:         7.5.0  ( Ubuntu 7.5.0-3ubuntu1~18.04 )
```

### 软件信息
```text
version : 	
     version : 	cmake version 3.10.2
                    CMake suite maintained and supported by Kitware (kitware.com/cmake).

```




## 前言
运行某make的sh文件

```sh
../make-share.sh
```

出现：
 **CMake错误No CMAKE_CXX_COMPILER could be found**


## 正文
### 具体情况
```
-- Detecting C compile features - done
CMake Error at CMakeLists.txt:5 (project):
  No CMAKE_CXX_COMPILER could be found.

  Tell CMake where to find the compiler by setting either the environment
  variable "CXX" or the CMake cache entry CMAKE_CXX_COMPILER to the full path
  to the compiler, or to the compiler name if it is in the PATH.

```

###　解决办法
```sh
sudo apt-get update
sudo apt-get install -y build-essential
```


## 后记

@[TOC](这里写自定义目录标题)



---
layout:     post
title:      Docker Get Permisson Denied
subtitle:    "\"Docker Get Permisson Denied\""
date:       2020-03-04
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Docker
---
## 前言

安装完docker之后，查看docker版本命令，出现

```sh
docker version
Client: Docker Engine - Community
 Version:           19.03.9
 API version:       1.40
 Go version:        go1.13.10
 Git commit:        9d988398e7
 Built:             Fri May 15 00:25:25 2020
 OS/Arch:           linux/amd64
 Experimental:      false
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/version: dial unix /var/run/docker.sock: connect: permission denied
```



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
    Client: Docker Engine - Community
 	Version:           19.03.9
 	API version:       1.40
 	Go version:        go1.13.10
 	Git commit:        9d988398e7
 	Built:             Fri May 15 00:25:25 2020
 	OS/Arch:           linux/amd64
 	Experimental:      false

```

## 正文

### 原因
摘自docker mannual上的一段话
```tex
Manage Docker as a non-root user

The docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user root and other users can only access it using sudo. The docker daemon always runs as the root user.

If you don’t want to use sudo when you use the docker command, create a Unix group called docker and add users to it. When the docker daemon starts, it makes the ownership of the Unix socket read/writable by the docker group.
```
分析：docker进程使用Unix Socket而不是TCP端口。而默认情况下，Unix socket属于root用户，需要root权限才能访问。

### 解决方案
docker守护进程启动的时候，会默认赋予名字为docker的用户组读写Unix socket的权限，因此只要创建docker用户组，并将当前用户加入到docker用户组中，那么当前用户就有权限访问Unix socket了，进而也就可以执行docker相关命令
```sh
sudo groupadd docker     # 添加docker用户组
sudo gpasswd -a $USER docker     # 将登陆用户加入到docker用户组中
newgrp docker     # 更新用户组
docker version
```
## 后记

@[TOC](这里写自定义目录标题)



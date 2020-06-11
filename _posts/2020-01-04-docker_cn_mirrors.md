---
layout:     post
title:      Docker change China Mirror
subtitle:    "\"docker change china mirrors\""
date:       2020-01-04
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Docker
---
## 前言

在国内访问 Docker 官方的镜像，一直以来速度都慢如蜗牛。为了快速访问 Docker 官方镜像都会配置第三方加速器，目前常用第三方加速器有：网易、USTC、阿里云。

现在 Docker 官方针对中国区推出了镜像加速服务。通过 Docker 官方镜像加速，国内用户能够以更快的下载速度和更强的稳定性访问最流行的 Docker 镜像。

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
Docker Engine - Community
 	Version:           19.03.9
```

## 正文

#### 给Docker守护进程配置加速器

通过配置文件启动Docker,修改`/etc/docker/daemon.json` 文件并添加上 registry-mirrors 键值。

```sh
$ sudo vim /etc/docker/daemon.json
{
 "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

> 也可选用网易的镜像地址：http://hub-mirror.c.163.com
>  {
>  "registry-mirrors": ["http://hub-mirror.c.163.com"]
>  }

修改保存后，重启 Docker 以使配置生效。

```undefined
sudo service docker restart
```


## 后记

@[TOC](这里写自定义目录标题)



---
layout:     post
title:      Ubuntu install Postman
subtitle:    "\"Ubuntu Install Postman\""
date:       2020-04-18
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Postman

---


## 前言

开发的过程中经常使用Postman来发起网络请求,下面就来介绍Postman这个工具到安装方法

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
     Postman v7.22.1
```
## 正文

1):官网下载软件包:
https://www.getpostman.com/apps
我这里下载的是Postman-linux-x64-7.22.1.tar.gz安装包
2):解压到指定文件夹:

```sh
SOFTWARE_INSTALL_PATH="/home/stephen/software"
sudo tar -xzf Postman-linux-x64-7.22.1.tar.gz -C ${SOFTWARE_INSTALL_PATH}
```
3):修改权限
```sh
chmod 770 -R ${SOFTWARE_INSTALL_PATH}/Postman
```

4):全局变量,以后可以直接在命令行输入postman启动
  建立软链接,创建解压后的Postman文件中的Postman到/usr/bin/目录下

```sh
sudo ln -s  ${SOFTWARE_INSTALL_PATH}/Postman/app/Postman   /usr/bin/postman
```
## 后记

@[TOC](这里写自定义目录标题)



---
layout:     post
title:      Hello 2020
subtitle:    "\"Hello World, Hello Blog\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 生活

---
## 前言

BY 的 Blog 就这么开通了。

本来打算在年前完成 Blog 的搭建，不曾料想踩了很多坑。。。

[跳过废话，直接看技术实现 ](#build) 

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
     
```

## 正文
ubuntu 安装 wechat
出现问题 
```sh
(Reading database ... 144555 files and directories currently installed.)
Preparing to unpack .../deepin.com.weixin.work_2.8.10.2010deepin0_i386.deb ...
Unpacking deepin.com.weixin.work:i386 (2.8.10.2010deepin0) over (2.4.16.1347deepin0) ...
dpkg: dependency problems prevent configuration of deepin.com.weixin.work:i386:
 deepin.com.weixin.work:i386 depends on deepin-wine (>= 2.18-0).
 deepin.com.weixin.work:i386 depends on deepin-wine32.
 deepin.com.weixin.work:i386 depends on deepin-wine32-preloader.
 deepin.com.weixin.work:i386 depends on deepin-wine-helper (>= 1.2deepin2).

dpkg: error processing package deepin.com.weixin.work:i386 (--install):
 dependency problems - leaving unconfigured
Processing triggers for gnome-menus (3.13.3-11ubuntu1.1) ...
Processing triggers for desktop-file-utils (0.23-1ubuntu3.18.04.2) ...
Processing triggers for mime-support (3.60ubuntu1) ...
Processing triggers for hicolor-icon-theme (0.17-2) ...
Errors were encountered while processing:
 deepin.com.weixin.work:i386

```

分析
安装wine 时候没有加上32位架构
先卸载deepin-wine
cd deepin-wine
sudo ./uninstall.sh
增加32位架构
``` sh
sudo dpkg --add-architecture i386
sudo apt-get update
```

重新安装deepin-wine

sudo ./install.sh
sudo dpkg -i ./deepin.com.weixin.work_2.8.10.2010deepin0_i386.deb
## 后记

@[TOC](这里写自定义目录标题)



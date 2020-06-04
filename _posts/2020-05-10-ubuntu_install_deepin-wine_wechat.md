---
layout:     post
title:      Ubuntu Install Deepin-wine wechat
subtitle:    "\"Ubuntu Install Deepin-wine wechat\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Ubuntu
    - wechat

---
## 前言
在Ubuntu 系统下安装wechat是意见很困难的事情，建议去网页版微信。
但是你说硬要安装客户端，那你就往下看。


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
ubuntu 安装 wechat出现问题信息：
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

### 分析
看错误日志提示信息：
```sh
dpkg: dependency problems prevent configuration of deepin.com.weixin.work:i386:
 deepin.com.weixin.work:i386 depends on deepin-wine (>= 2.18-0).
 deepin.com.weixin.work:i386 depends on deepin-wine32.
 deepin.com.weixin.work:i386 depends on deepin-wine32-preloader.
 deepin.com.weixin.work:i386 depends on deepin-wine-helper (>= 1.2deepin2).

dpkg: error processing package deepin.com.weixin.work:i386 (--install):
 dependency problems - leaving unconfigured
```
缺少４个依赖包

宗旨：缺啥补啥。

####  去[Ubuntu官方网站](https://packages.ubuntu.com/)下载依赖包

   1. 查看当前系统版本对应的版本代号

      ```
      xenial (16.04LTS)
      bionic (18.04LTS)
      disco (19.04)
      ```

   2. 查找想要的依赖包，如 deepin-wine

   3. 下载并安装

      ```sh
      sudo dpkg -i XXX.deb
      ```



## 后记



---
layout:     post
title:      Ubuntu18.04安装shadowSocks-qt5
subtitle:    "\"Ubuntu18.04安装shadowSocks-qt5\""
date:       2020-02-10
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Ubuntu
---

## 前言

Ubuntu16.04安装shadowSocks-qt5正常步骤是这样的

```sh
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

但是在18.04中出现了问题

```sh
Err:8 http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu bionic Release           
  404  Not Found [IP: 91.189.95.83 80]                             
Reading package lists... Done
E: The repository 'http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu bionic Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```
原因：ppa:hzwhuang/ss-qt5 并没有18.04版本的源


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
version : 	shadowSocks-qt5
```



## 解决方法


1. 修改sources.list.d/hzwhuang-ubuntu-ss-qt5-bionic.list的内容
``` sh
sudo gedit /etc/apt/sources.list.d/hzwhuang-ubuntu-ss-qt5-bionic.list
```
2. 将bionic(18.04版本代号)改成xenial(16.04版本代号)
```sh
deb http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu xenial main
# deb-src http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu xenial main
# deb-src http://ppa.launchpad.net/hzwhuang/ss-qt5/ubuntu xenial main
```

3. 最后再更新即可
```sh
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```



---




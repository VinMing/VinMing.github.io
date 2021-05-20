---
layout:     post
title:      Ubuntu install Redis Desktop Manager
subtitle:    "\"Ubuntu install Redis Desktop Manager\""
date:       2020-04-13
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Redis Desktop Manager
    - Ubuntu
    - deb

---

## 前言

Redis Desktop Manager（RDM）,这个工具操作redis还是蛮好用的，[RDM官网](https://github.com/uglide/RedisDesktopManager)

使用deb包安装0.8版本64位的RDM[官网deb下载](https://github.com/uglide/RedisDesktopManager/releases/download/0.8.3/redis-desktop-manager_0.8.3-120_amd64.deb)

失效就下载这个[百度云盘](https://pan.baidu.com/s/1cA3jWU)

历史版本 [redisdesktopmanager地址](https://github.com/uglide/RedisDesktopManager/releases)

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
version : 	redis-desktop-manager_0.8.3-120_amd64
```

## 正文

### 安装
```sh
sudo dpkg -i redis-desktop-manager_0.8.3-120_amd64.deb
```


可能你会出现以下错误
```sh
sudo dpkg -i redis-desktop-manager_0.8.3-120_amd64.deb 
(Reading database ... 189965 files and directories currently installed.)
Preparing to unpack redis-desktop-manager_0.8.3-120_amd64.deb ...
Unpacking redis-desktop-manager (0.8.3-120) over (0.8.3-120) ...
dpkg: dependency problems prevent configuration of redis-desktop-manager:
 redis-desktop-manager depends on zlibc; however:
  Package zlibc is not installed.
 redis-desktop-manager depends on libssh2-1; however:
  Package libssh2-1 is not installed.

dpkg: error processing package redis-desktop-manager (--install):
 dependency problems - leaving unconfigured
Processing triggers for gnome-menus (3.13.3-11ubuntu1.1) ...
Processing triggers for desktop-file-utils (0.23-1ubuntu3.18.04.2) ...
Processing triggers for mime-support (3.60ubuntu1) ...
Errors were encountered while processing:
 redis-desktop-manager
```
再运行
```sh
sudo apt-get -f install
```
还是出现错误
```sh
 redis-desktop-manager depends on zlibc; however:
  Package zlibc is not installed.
 redis-desktop-manager depends on libssh2-1; however:
  Package libssh2-1 is not installed.
```
### 错误分析
```sh
redis-desktop-manager depends on zlibc; however:
  Package zlibc is not installed.
 redis-desktop-manager depends on libssh2-1; however:
  Package libssh2-1 is not installed.
```
缺少zlibc、libssh2-1依赖包,根据不同的系统情况，依赖包可能不一样，不过也没关系，照葫芦画瓢
### 解决方案
#### 1. 修改源（个人觉得比较麻烦）

   1. 首先打开终端，用root登陆.

   2. 切换到/etc/apt/目录下

      ```sh
      cd /apt/get/
      ```

   3. 将里面的sources.list复制一下，为了安全备份一下.

      ```sh
      cp  sources.list sources.list.bk
      ```

   4. 用编辑sources.list文件，删除里面所有的东西，把163的源链接(文章后记提供)全部复制进去，保存即可.

   5. 更新163源到当前系统

      ```sh
      sudo apt-get update
      ```


#### 2. 去[Ubuntu官方网站](https://packages.ubuntu.com/)下载依赖包

   1. 查看当前系统版本对应的版本代号

      ```
      xenial (16.04LTS)
      bionic (18.04LTS)
      disco (19.04)
      ```

   2. 查找想要的依赖包，如 libssh2-1

      [libssh2-1的检索结果](https://packages.ubuntu.com/search?keywords=libssh2-1&searchon=names&suite=eoan&section=all)

   3. 下载并安装

      ```sh
      sudo dpkg -i XXX.deb
      ```

## 更新线
网上有大佬已经把rdm打包成appimage（牛逼～土拨鼠叫声），不是官方的rdm，[传送门](https://github.com/qishibo/AnotherRedisDesktopManager)

## 后记

@[TOC](这里写自定义目录标题)

1. 163源

   ```list
   deb http://mirrors.163.com/ubuntu/ trusty main restricted universe multiversedeb http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiversedeb http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiversedeb http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiversedeb http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiversedeb-src http://mirrors.163.com/ubuntu/ trusty main restricted universe multiversedeb-src http://mirrors.163.com/ubuntu/ trusty-security main restricted universe multiversedeb-src http://mirrors.163.com/ubuntu/ trusty-updates main restricted universe multiversedeb-src http://mirrors.163.com/ubuntu/ trusty-proposed main restricted universe multiversedeb-src http://mirrors.163.com/ubuntu/ trusty-backports main restricted universe multiverse
   ```

2. aliyun源

   ```
   deb http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ quantal main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ quantal-security main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ quantal-updates main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ quantal-proposed main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ quantal-backports main restricted universe multiverse Precise(12.04) deb http://mirrors.aliyun.com/ubuntu/ precise main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ precise-security main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ precise-updates main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ precise-proposed main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ precise-backports main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ precise main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ precise-security main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ precise-updates main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ precise-proposed main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ precise-backports main restricted universe multiverse Oneiric(11.10) deb http://mirrors.aliyun.com/ubuntu/ oneiric main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ oneiric-security main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ oneiric-updates main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ oneiric-proposed main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ oneiric-backports main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ oneiric main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ oneiric-security main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ oneiric-updates main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ oneiric-proposed main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ oneiric-backports main restricted universe multiverse Natty(11.04) deb http://mirrors.aliyun.com/ubuntu/ natty main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ natty-security main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ natty-updates main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ natty-proposed main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ natty-backports main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ natty main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ natty-security main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ natty-updates main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ natty-proposed main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ natty-backports main restricted universe multiverse
   ```

   
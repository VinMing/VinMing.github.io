---
layout:     post
title:      Deepin Install Node
subtitle:    "\"Deepin Install Node\""
date:       2020-02-06
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Node
---
## 前言

#### 问题：

```sh
sudo apt-get install npm
正在读取软件包列表... 完成
正在分析软件包的依赖关系树       
正在读取状态信息... 完成       
没有可用的软件包 npm，但是它被其它的软件包引用了。
这可能意味着这个缺失的软件包可能已被废弃，
或者只能在其他发布源中找到
然而下列软件包会取代它：
  node nodejs-bin

E: 软件包 npm 没有可安装候选
```

#### 分析 

源没有npm的软件包又不想更换源来破坏系统软件的依赖稳定性。

#### 解决方案

通过源码编译安装Node(node+npm)

## 环境
#### 系统环境
```text
Distributor ID:	Deepin
Description:	Deepin 20 Beta
Release:	20 Beta
Codename:	n/a
Linux version :     5.3.0-3-amd64 (debian-kernel@lists.debian.org)
Gcc version:        8.3.0 (Debian 8.3.0-6)
```
#### 软件信息
```text
version : 	
     None
```

## 正文

在[node官网下载专区](https://nodejs.org/en/download/)找到系统对应的版本，鼠标右键复制下载链接。在终端中输入：

1. 下载node
```sh
wget https://nodejs.org/dist/v8.11.4/node-v8.11.4-linux-x64.tar.xz
```
2. 解压文件
```sh
tar -xvf node-v8.11.4-linux-x64.tar.xz
```
3. 切换并查看node所在路径
```sh
cd node-v8.11.4-linux-x64/bin
pwd
```
4. 查看node版本
```sh
node -v
```
5. 将node和npm设置为全局(注意路径为第3步的路径)
``` sh
sudo ln /home/ubuntu/node-v8.11.4-linux-x64/bin/node /usr/local/bin/node
sudo ln -s /home/stephen/software/node-v12.18.2-linux-x64/bin/npm /usr/bin
```
6. 检查
```sh
$ node -v
v12.18.2
$ npm -v
6.14.5
```

## 后记

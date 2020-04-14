---
layout:     post
title:      Ubuntu18 install Navicat Premium 15
subtitle:    "\"Ubuntu18 install Navicat Premium 15\""
date:       2020-04-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Ubuntu
    - Navicat Premium 15

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
version : 	Navicat Premium 15.0.8
```

## 相关工具
```text
navicat15-premium-cs.AppImage：Navicat 15 premium 官方简体中文试用版
navicat-patcher：补丁
navicat-keygen ：注册机
appimagetool-x86_64.AppImage：Linux 独立运行软件打包工具
```
相关[工具百度网盘下载地址](https://pan.baidu.com/s/1u01pL0Fz7A0L1sfvU6_ZHg#list/path=%2F) 『提取码』：2xat

## 系统环境配置
### 1.  安装capstone

```sh
sudo apt-get install libcapstone-dev
```
### 2.  安装keystone
```sh
sudo apt-get install cmake
git clone https://github.com/keystone-engine/keystone.git
cd keystone
mkdir build
cd build
../make-share.sh
sudo make install
sudo ldconfig
```
运行../make-share.sh　可能出现的bug: **CMake错误No CMAKE_CXX_COMPILER could be found**，[解决办法](https://vinming.github.io/2020/04/02/CMAKE_CXX_COMPILER_Err_/)
安装rapidjson
```sh
sudo apt-get install rapidjson-dev
```
## 操作步骤
### 1.  赋予执行权限
```sh
chmod +x appimagetool-x86_64.AppImage
chmod +x navicat-patcher
chmod +x navicat-keygen
```
### 2.  解包官方软件
```sh
mkdir navicat15
mount -o loop navicat15-premium-cs.AppImage navicat15
cp -r navicat15 navicat15-patched
```

### 3.  运行补丁
```sh
./navicat-patcher navicat15-patched
```
生成RegPrivateKey.pem文件,记住这个RSA-2048 private key的pem文件路径

```sh
 **********************************************************
*       Navicat Patcher (Linux) by @DoubleLabyrinth      *
*                  Version: 1.0                          *
**********************************************************

Press ENTER to continue or Ctrl + C to abort.
...
...
...
 New RSA-2048 private key has been saved to
    ~/RegPrivateKey.pem

*******************************************************
*           PATCH HAS BEEN DONE SUCCESSFULLY!         *
*                  HAVE FUN AND ENJOY~                *

```



### 4.  打包成独立运行软件

```sh
./appimagetool-x86_64.Appimage navicat15 navicat15-premium-cs-pathed.AppImage
```
成功效果图
```sh
appimagetool, continuous build (commit 64321b7), build 2111 built on 2019-11-23 22:20:53 UTC
Generating squashfs...
...
...
Marking the AppImage as executable...
Embedding MD5 digest
Success

Please consider submitting your AppImage to AppImageHub, the crowd-sourced
central directory of available AppImages, by opening a pull request
at https://github.com/AppImage/appimage.github.io

```
### 5.  运行补丁后软件包
```sh
chmod +x navicat15-premium-cs-pathed.AppImage
./navicat15-premium-cs-pathed.AppImage
```
### 6.  运行注册机

```sh
./navicat-keygen --text ./RegPrivateKey.pem 
```
选择产品类型：　１　premium
```sh
**********************************************************
*       Navicat Keygen (Linux) by @DoubleLabyrinth       *
*                   Version: 1.0                         *
**********************************************************

[*] Select Navicat product:
 0. DataModeler
 1. Premium
 2. MySQL
 3. PostgreSQL
 4. Oracle
 5. SQLServer
 6. SQLite
 7. MariaDB
 8. MongoDB
 9. ReportViewer

(Input index)> 1

```
选择语言：　１　simplified chinese
```sh
[*] Select product language:
 0. English
 1. Simplified Chinese
 2. Traditional Chinese
 3. Japanese
 4. Polish
 5. Spanish
 6. French
 7. German
 8. Korean
 9. Russian
 10. Portuguese

(Input index)> 1

```
选择版本： 15
```sh
[*] Input major version number:
(range: 0 ~ 15, default: 12)> 15
```
回车生成序列号（自己的）：填写至软件注册页面的永久许可证，并在断网后选择手工激活
```sh
[*] Serial number:
NAVC-BDAP-LC2M-4HER
```
填写个人信息：
```sh
[*] Your name: stephen
[*] Your organization: stephen

```
输入注册码：复制软件激活也没注册码并粘贴到此处
```sh
[*] Input request code in Base64: (Double press ENTER to end)
*****== #(自己的)
```

你可能遇到的bug
```sh
[-] ./navicat-keygen/GenerateLicense.cpp:251 ->
    RSA_private_decrypt failed.
    Hints: Are your sure you DO provide a correct private key?
```
经过多次试验，查阅了很多资料，
	[网上的教程](https://www.cnblogs.com/miketian/p/11898510.html)说是pem文件出错，建议重新第一步生成新的pem秘钥文件，结果还是不行

看到[网上的大佬分享破解](https://www.liangzl.com/get-article-detail-164367.html)详细过程才发现，我是一味的复制请求码的，我这里先卖个关子。**想大家多思考一下，我就不明说是怎么搞定的，我给个思路：输入注册码的时候不是以为顾着复制和粘贴，还需要了解linux文本字符串的转义和复制的文本的机制**

**生成激活码：** *复制Activation Code内容粘贴至软件激活页面*

```sh
[*] Request Info:
{"K":"NAVAM2BY6NBTSSIZ", "DI":"0503DCB4CD351D584C4F", "P":"linux"}

[*] Response Info:
{"K":"NAVAM2BY6NBTSSIZ","DI":"0503DCB4CD351D584C4F","N":"stephen","O":"stephen","T":1586485590}

[*] Activation Code:
*****==　#(自己的)

```

### done
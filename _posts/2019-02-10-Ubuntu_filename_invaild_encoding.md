---
layout:     post
title:      ubuntu 文件名 乱码
subtitle:    "\"ubuntu 文件名 乱码\""
date:       2019-02-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - ubuntu
---


#### 前言

ubuntu 下文件名 乱码,显示invaild encoding 等字符,如下图

![Image text](/img/Ubuntu_filename_home.png)




#### 正文

文件是在WIndows下创建的,Windows 的文件名中文编码默认为GBK,而Linux中默认文件名编码为UTF8,由于编码不一致所以导致了文件名乱码的问题，解决这个问题需要对文件名进行转码。

##### 安装：

``` shell
sudo apt-get install convmv
```
###### 使用:
```shell
sudo convmv -f gbk -t utf-8 -r --notest /your/path
# 详解:
# convmv -f 源编码 -t 新编码 [选项] 文件名
# 常用参数：
# -r 递归处理子文件夹
# –notest 真正进行操作，默认情况下是不对文件进行真实操作
# –list 显示所有支持的编码
```


#### 后记

注意:转换成功之后,在window下访问修改过文件夹是无法访问的,但是文件内部是完整无损.

![Image text](/img/Ubuntu_filename_houji.png)

只能在ubuntu下才能访问

![Image text](/img/Ubuntu_filename_fix_after.png)

---


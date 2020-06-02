---
layout:     post
title:      FAILED TO WRITE REG 28b4 WAIT REG 28c6
subtitle:    "\" failed to write reg 28b4 wait reg 28c6 \""
date:       2019-12-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Dedian Bug
---
## 前言

关机时候提示：

​           **FAILED TO WRITE REG 28b4 WAIT REG 28c6**

导致无法关机或者卡在关机中。。要强制关机。

## 环境
#### 系统环境
```text
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.4 LTS
Release:	18.04
Codename:	bionic
Linux version :       5.3.0-53-generic (buildd@lgw01-amd64-016) 
Gcc version:         7.5.0  (Ubuntu 7.5.0-3ubuntu1~18.04)
```


#### 软件信息

```text
version : 	
     None
```

## 正文

### 解决方案：

进入grub，设置 amdgpu.noretry=0


## 后记

详细的错误信息：

```sh
$ dmsg
[    0.000000] Linux version 5.3.0-53-generic (buildd@lgw01-amd64-016) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #47~18.04.1-Ubuntu SMP Thu May 7 13:10:50 UTC 2020 (Ubuntu 5.3.0-53.47~18.04.1-generic 5.3.18)
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-5.3.0-53-generic root=UUID=fd6fdfc1-4ec2-4069-8376-dbff8bfcc277 ro quiet splash vt.handoff=1
[    0.000000] KERNEL supported cpus:
[    0.000000]   Intel GenuineIntel
[    0.000000]   AMD AuthenticAMD
[    0.000000]   Hygon HygonGenuine
[    0.000000]   Centaur CentaurHauls
[    0.000000]   zhaoxin   Shanghai  

[    8.503190] failed to write reg 28b4 wait reg 28c6
[   10.111201] failed to write reg 1a6f4 wait reg 1a706
[   11.963177] failed to write reg 28b4 wait reg 28c6
[   13.570821] failed to write reg 1a6f4 wait reg 1a706
[   15.223197] failed to write reg 28b4 wait reg 28c6
[   16.831153] failed to write reg 1a6f4 wait reg 1a706
[   24.695214] failed to write reg 28b4 wait reg 28c6
[   26.307244] failed to write reg 1a6f4 wait reg 1a706
[   28.023171] failed to write reg 28b4 wait reg 28c6
[   29.631173] failed to write reg 1a6f4 wait reg 1a706
[   31.271218] failed to write reg 28b4 wait reg 28c6
[   32.879210] failed to write reg 1a6f4 wait reg 1a706
[   35.095637] failed to write reg 28b4 wait reg 28c6
[   36.680117] failed to write reg 1a6f4 wait reg 1a706
[   38.281082] failed to write reg 28b4 wait reg 28c6
[   39.874224] failed to write reg 1a6f4 wait reg 1a706
[   41.743882] failed to write reg 28b4 wait reg 28c6
[   43.342273] failed to write reg 1a6f4 wait reg 1a706
[   43.733242] Bluetooth: RFCOMM TTY layer initialized
[   43.733248] Bluetooth: RFCOMM socket layer initialized
[   43.733254] Bluetooth: RFCOMM ver 1.11
[   46.137530] failed to write reg 28b4 wait reg 28c6
[   47.740511] failed to write reg 1a6f4 wait reg 1a706
[   48.307494] rfkill: input handler disabled
[  150.994401] failed to write reg 28b4 wait reg 28c6
[  152.614078] failed to write reg 1a6f4 wait reg 1a706
[  154.242623] failed to write reg 28b4 wait reg 28c6
[  155.858560] failed to write reg 1a6f4 wait reg 1a706
```




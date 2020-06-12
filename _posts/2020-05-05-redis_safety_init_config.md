---
layout:     post
title:      Redis Safety Init Configuration
subtitle:    "\"redis safety init configuration\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Redis-Config
---
## 前言

针对[database hacker attack](https://vinming.github.io/2020/01/20/database_hack_attack)，redis安装完之后，要有几个初始化安全配置。

## 环境
#### 系统环境
```text
version 
    No LSB modules are available.
    Distributor ID:	Deepin
    Description:	Deepin 20 Beta
    Release:	20 Beta
    Codename:	n/a
```
#### 软件信息
```text
version:
    Redis server v=3.2.6 sha=00000000:0 malloc=jemalloc-3.6.0 bits=64 
```

## 正文

默认知道redis.xx.conf的相关配置的！如果不知道，请看：
[redis_common_config]()

### 配置redis防范攻击措施

#### 一、 redis版本升级

去[redis官网](https://redis.io/download)查看版本情况，注意redis 偶数为稳定版本，奇数为开发版本。

##### 1. 初始化升级

直接删除旧的版本，安装新的版本，这里就不再叙述，自行google和百度。

##### 2. 大版本平滑升级（保存已有的key）

大版本注释：例如从Redis 2.8升级到Redis 4.0。
设置主从模式，备份主库到从库，再卸载主库安装新版本再同步到主库，详情请看[redis主从设置]()

#### 二、设置高强度密码(重启生效)

修改 redis.conf 文件，添加

```
requirepass mypassword
```
（注意redis不要用-a参数，明文输入密码，连接后使用auth认证）


#### 三、禁止一些高危命令(重启生效)

修改 redis.conf 文件，禁用远程修改 DB 文件地址
```sh
rename-command FLUSHALL ""
rename-command CONFIG ""
rename-command EVAL ""
```
设置为空即为禁用该命令

或者通过修改redis.conf文件，改变这些高危命令的名称
```sh
rename-command FLUSHALL "name1"
rename-command CONFIG "name2"
rename-command EVAL "name3"
```


#### 四、以低权限运行redis服务(重启生效)

为 Redis 服务创建单独的用户和家目录，并且配置禁止登陆

```
groupadd -r redis && useradd -r -g redis redis
```

#### 五、修改默认端口(重启生效)

修改配置文件redis.conf文件

```
Port 6379
```

默认端口是6379，可以改变成其他端口（不要冲突就好）

#### 六、保证authorized_keys文件的安全

为了保证安全，您应该阻止其他用户添加新的公钥。

- 将 authorized_keys 的权限设置为对拥有者只读，其他用户没有任何权限：
	```
	chmod 400 ~/.ssh/authorized_keys
```
- 为保证 authorized_keys 的权限不会被改掉，您还需要设置该文件的 immutable 位权限:
	```
chattr +i ~/.ssh/authorized_keys
```

- 然而，用户还可以重命名 ~/.ssh，然后新建新的 ~/.ssh 目录和 authorized_keys 文件。要避免这种情况，需要设置 ~./ssh 的 immutable 权限：
	```
chattr +i ~/.ssh
```

#### 七、设置防火墙策略　开放外网访问，限制已知ip访问

如果正常业务中Redis服务需要被其他服务器来访问，可以设置iptables策略仅允许指定的IP来访问Redis服务。

修改redis的配置文件，将所有bind信息全部屏蔽。
```config
# bind 192.168.1.8
# bind 127.0.0.1
```
屏蔽掉 bind，即允许本机以外的机器访问它
修改完成后，需要重新启动redis服务。
``` sh
redis-server redis.conf
```

如果iptables 没有开启6379端口，用这个方法开启端口

```sh
/sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
/etc/rc.d/init.d/iptables save
```
通过iptables 允许指定的外网ip访问

修改 Linux 的防火墙(iptables)，开启你的redis服务端口，默认是6379。

``` sh
# 只允许112.122.13.39访问6379
iptables -A INPUT -s 112.122.13.39 -p tcp --dport 6379 -j ACCEPT
# 其他ip访问全部拒绝
iptables -A INPUT -p TCP --dport 6379 -j REJECT
```

## 后记

@[TOC](这里写自定义目录标题)



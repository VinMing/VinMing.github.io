---
layout:     post
title:      Deepin JAVA Env
subtitle:    "\"Deepin JAVA Env\""
date:       2020-02-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Java Env
---
## 前言

在Deepin系统下java开发环境部署

## 环境
#### 系统环境
```text
No LSB modules are available.
Distributor ID:	Deepin
Description:	Deepin 20 Beta
Release:	20 Beta
Codename:	n/a
```
#### 软件信息
```text
version : 	
     java 
```

## 正文

### 一、Oracle JDK

1.下载JDK压缩包（.tar.gz），[官方下载地址]()，选择其中的32/64位Linux版本。

2.将其解压缩：

```
sudo tar -zxvf ~/Downloads/jdk-14.0.1_linux-x64_bin.tar.gz -C /usr/lib
```

其中参数-C后面的路径是解压缩的目标路径。

3.根据官网的说法：

> Starting with version 8u40, the JDK installation is integrated with  the alternatives framework and after installation, the alternatives  framework is updated to reflect the binaries from the recently installed JDK. Java commands such as java, javac, javadoc, and javap can be  invoked from the command line.  

所以：

```
sudo update-alternatives --install /usr/bin/java java  /usr/lib/jdk-14.0.1/bin/java 1000 
sudo update-alternatives --install /usr/bin/javac javac  /usr/lib/jdk-14.0.1/bin/javac 1000
```

现在可以验证一下JDK安装是否已成功 

```
java -version
```

### 二、MYSQL安装和使用

 1) 解压并移动（注意不要修改解压到的地址），cd到~/Downloads，输入以下代码

```sh
sudo tar -xvJf mysql-8.0.19-linux-glibc2.12-x86_64.tar.xz -C /usr/local 
```

 2) 进入/usr/local目录

```sh
cd /usr/local
```

 3) 为mysql-8.0.19-linux-glibc2.12-x86_64目录创建软链接（方便操作）

```sh
sudo ln -s mysql-8.0.19-linux-glibc2.12-x86_64 mysql
```

 4) 添加mysql用户组和mysql用户(-s /bin/false参数指定mysql用户仅拥有所有权，而没有登录权限)

```sh
sudo groupadd mysql
sudo useradd -r -g mysql -s /bin/false mysql
```

 5) 进入安装mysql软件的目录

```sh
cd /usr/local/mysql
```
 6) 在/usr/local/mysql下建立data文件夹用于存放数据库文件
```sh
sudo mkdir /usr/local/mysql/data
```
 7) 修改当前目录拥有者为新建的mysql用户
```sh
sudo chown -R mysql:mysql ./
```
 8) 安装mysql
```sh
sudo ./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
```
 正常安装之后会显示如下结果：
 ```sh
sudo ./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
 ```
 记下随机产生的密码（root@localhost:后面跟的所有字符就是密码）

 9) 开启mysql服务
```sh
sudo ./support-files/mysql.server start
```
 显示:
```sh
Starting MySQL
.OK
```
 显示是这样的话就基本完成了

 10) 将mysql进程放入系统进程中
```sh
sudo cp support-files/mysql.server /etc/init.d/mysqld
```
 11) 重新启动mysql服务（这里可能会报错，不用理他）
```sh
service mysqld restart
```
 12) 在/usr/bin下建立指向mysql的软连接之后使用随机密码登录mysql数据库
```sh
sudo ln -s /usr/local/mysql/bin/mysql /usr/bin
mysql -u root -p
```
 根据提示输入第8步的随机密码

 13) 进入mysql操作行，为root用户设置新密码
```sh
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
```
完成

### 三、MAVEN安装

1.[下载](http://maven.apache.org/download.cgi)合适自己的版本并加压包到安装位置 exp:/usr/local/

```
tar zxvf /your/path/apache-maven-3.6.3.tar.gz -C /your/path/
```

2.配置命令连接符

```sh
export MAVEN_HOME=/home/stephen/software/apache-maven-3.6.3
export PATH=${MAVEN_HOME}/bin:$PATH
```

3.配置默认jdk版本和默认编译级别在目录conf/setting.xml中配置

```
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>      
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>            
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```

## 后记

参考：[Deepin 2015系统下java开发环境部署](https://bbs.deepin.org/forum.php?mod=viewthread&tid=36225)



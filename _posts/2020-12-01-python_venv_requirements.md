---
layout:     post
title:      Python Venv & requirements
subtitle:    "\"Python venv and requirements\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Python

---
## 前言

python 的虚拟环境venv和requirements 两者的使用事宜。及其配搭使用。

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
     Python 3.7.2
```

## 正文

### 1. 虚拟环境venv

   在 python3 中，虚拟环境由标准库中的 venv 包原生支持，创建venv虚拟环境的命令格式如下：
   `python3 -m venv virtual-environment-name`

   -m venv 选项的作用是以独立的脚本运行标准库中的 venv 包，后面的参数为虚拟环境的名称。
   本例子为创建一个名为 venv 的虚拟环境，命令如下：
   `python3 -m venv venv`

   若想使用虚拟环境，要先将其“激活”，命令如下：
   `source venv/bin/activate`

   windows“激活”示例：
   ```sh
   cd venv\Scripts
   actives
   ```
   取消激活：
   ```sh
   venv\Scripts\deactives
   ```

### 2. requirements.txt依赖包

   #### 描述问题：

   在对项目进行打包时，经常需要编写requirements.txt(一个项目所需要的模块)
   当项目太大的时候，手动添加模块和对应的版本的工作量就大起来了。

   #### 问题：

   python自动python 导出一个项目所需要的模块及其对应版本

   #### 解决方案：

   - **使用 pip freeze**

   ```
   pip freeze > requirements.txt
   ```

   这种方式配合virtualenv才好使，否则把整个环境中的包都列出来了。

   - **使用 pipreqs**

   这个工具的好处是可以通过对项目目录的扫描，自动发现使用了那些类库，自动生成依赖清单。

   ```
   pip install pipreqs
   cd $project_location
   pipreqs ./
   INFO: Successfully saved requirements file in ./requirements.txt
   ```

   如果你是window环境，
   运行pipreqs ./ 可能会报错：
   UnicodeDecodeError: 'gbk' codec

   只需将  pipreqs ./ 修改为

   ```
   pipreqs ./ --encoding=utf-8
   ```
   #### 依赖包 requirements使用方法 ~= ＞= ＜

   ```sh
   # 指定一个版本
   project == 1.3
    
   # 指定版本区间
   project >=1.2,<2.0
    
   # 使用该版本的兼容发行版本
   project~=1.4.2
    
   # 6.0 以后的特性，可以指定环境
    
   # Python 版本小于 2.7 时安装 5.4 版本
   project == 5.4; python_version < '2.7'
    
   # 仅在 Windows 环境下安装
   project; sys_platform == 'win32'
   # 仅在linux 环境下安装
   project; sys_platform == 'linux'
   ```

   



## 后记




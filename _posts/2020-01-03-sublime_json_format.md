---
layout:     post
title:      Sublime Json Format
subtitle:    "\"sublime json format\""
date:       2020-01-28
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Sublime
---
## 前言

使用sublime Text 美化 json格式

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
     Sublime Text Build 3211
```

## 正文

#### 一、安装Install Package

点击菜单中的 “View”–“ShowConsole”（快捷键 ```Ctrl+ ` ```）调出Console。然后把下面的代码粘贴进去后回车即可，需稍微等待一段时间。

注：若失效，请以官网https://packagecontrol.io/installation#st2代码为准。）

SublimeText3
```
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' +'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp= sublime.installed_packages_path(); urllib.request.install_opener(urllib.request.build_opener( urllib.request.ProxyHandler()) ); by =urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ','%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validatingdownload (got %s instead of %s), please try manual install' % (dh, h)) if dh !=h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
sublimetext2
```
import urllib2,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' +'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp= sublime.installed_packages_path(); os.makedirs( ipp ) if notos.path.exists(ipp) else None; urllib2.install_opener( urllib2.build_opener(urllib2.ProxyHandler()) ); by = urllib2.urlopen( 'http://packagecontrol.io/' +pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); open(os.path.join( ipp, pf), 'wb' ).write(by) if dh == h else None; print('Errorvalidating download (got %s instead of %s), please try manual install' % (dh,h) if dh != h else 'Please restart Sublime Text to finish installation')
```
 输入后，按回车键，稍等片刻，重启sublime text；
#### 二、安装pretty json

快捷键ctrl+shift+p，打开面板，输入pci，选中“PackageControl: Install Package”并回车，然后通过输入插件的名字pretty json找到插件并回车安装即可。

#### 三、验证

使用ctrl+ alt+ j快捷键来格式化当前页面的内容。

## 后记


sublime text 鲜为人知的快捷键：重复字段的复制粘贴
先选中一行你想要复制的文字，比如game
然后按下ctrl + d
重复按，你就可以同时编辑多行数据
再按下ctrl + i 选中光标所在的行
最后就是ctrl + c 到另外一个文件 ctrl + v




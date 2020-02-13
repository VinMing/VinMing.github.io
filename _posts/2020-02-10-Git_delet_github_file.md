---
layout:     post
title:      在git目录下删除放在远程仓库的文件
subtitle:    "\"在本地git目录下删除远程git仓库的文件 \""
date:       2020-02-10
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Git
---

## 前言

项目开发初期由于`.gitignore` 文件配置不正确很有可能导致某些不需要的目录上传到 git 远程仓库上了，这样会导致每个开发者提交的时候这些文件每次都会不同。除了一开始提交的时候注意配置好 `.gitignore` 文件外，我们也需要了解下出现这种问题后的解决办法。

总的一句话:在github上只能删除仓库,却无法删除文件夹或文件, 所以只能通过命令来解决 .

## 正文
1. 将远程仓库里面的项目拉下来
```bash
git pull origin master 
```
2. 预览将要删除的文件(这个时候在你的本地是没有该文件的)
```bash
git rm -r -n --cached /your/path
#加上 -n 这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的文件列表预览。
```

3. 确定无误后删除文件
```bash
git rm -r --cached /your/path
```

4. 提交到本地并推送到远程服务器
```bash
git commit -m "delet github file"
git push origin master
```

5. 检查远程仓库

# 后记

之所以会出现本地git和远程git的目录不一致:是因为删除文件或者文件夹**没有使用git命令行**

请回看**前言的第一段话**,你品,你细品...
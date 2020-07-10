---
layout:     post
title:      Scrapy Debug With mitmproxy
subtitle:    "\"scrapy debug with mitmproxy\""
date:       2019-12-04
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Scrapy
---
## 前言

pypi 自动打包的抓包工具- [mitmproxy](https://mitmproxy.org),也有[docker镜像](https://hub.docker.com/r/mitmproxy/mitmproxy)

mitmproxy还有两个关联组件，一个是mitmdump，它是mitmproxy的命令行接口，利用它可以对接Python脚本，实现监听后的处理；另一个是mitmweb，它是一个Web程序，通过它以清楚地观察到mitmproxy捕获的请求。



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
     python 3.7.0
```

## 正文

安装：

```sh
pip install mitmproxy
```

scrapy配置监听请求

```python
# ......
      return Request(url=url, headers=headers,
                     meta={"proxy": "https://127.0.0.1:8888"}
                    )
```



启动监听

```sh
mitmweb -p 8888

Web server listening at http://127.0.0.1:8081/
Proxy server listening at http://*:8888
127.0.0.1:37070: clientconnect
127.0.0.1:37232: clientconnect
...........
```

点击生成的地址，查看scrapy请求情况

![Image text](/img/scrapy_mitmweb_request.png)




## 后记

@[TOC](这里写自定义目录标题)



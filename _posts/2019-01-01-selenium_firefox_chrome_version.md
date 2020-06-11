---
layout:     post
title:      Selenium In Drive With Firefox and Chrome Version 
subtitle:    "\"selenium drive with firefox and chrome version\""
date:       2019-01-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Selenium WebDriver
---
## 前言

使用过selenium 自动化框架，都要了解selenium和drive驱动和浏览器的之间的关系

selenium 通过一套协议（[JsonWireProtocol](https://github.com/SeleniumHQ/selenium/wiki/JsonWireProtocol)）与 ChromeDriver、geckodriver、MicrosoftWebDriver、IEDriverServer和operadriver五大驱动进行通信，

selenium 实质上是对这套协议的底层封装，同时提供外部 WebDriver 的上层调用类库。

不过有时候因为这三者的版本不对应，导致在调试selenium自动化时候会出现程序报错。例如

```sh
×××××××××××××××××××
  raise exception_class(message, screen, stacktrace)
selenium.common.exceptions.SessionNotCreatedException: Message: Unable to find a matching set of capabilities
```

下面只是列举Chrome和Firefox，其他可以自行google或者百度查找。

## 正文

#### Chrome

selenium 调用 [ChromeDriver](http://chromedriver.chromium.org/)提供的python接口来驱动chrome浏览器。更多的了解[ChromeDriver](http://chromedriver.chromium.org/)

| chromedriver版本 | 支持的Chrome版本 |
| ---------------- | ---------------- |
| v2.46            | v71-73           |
| v2.45            | v70-72           |
| v2.44            | v69-71           |
| v2.43            | v69-71           |
| v2.42            | v68-70           |
| v2.41            | v67-69           |
| v2.40            | v66-68           |
| v2.39            | v66-68           |
| v2.38            | v65-67           |
| v2.37            | v64-66           |
| v2.36            | v63-65           |
| v2.35            | v62-64           |
| v2.34            | v61-63           |
| v2.33            | v60-62           |
| v2.32            | v59-61           |
| v2.31            | v58-60           |
| v2.30            | v58-60           |
| v2.29            | v56-58           |
|       ....           |       ....          |

附：上表更新在2020-06-11
所有chromedriver均可在[chromedriver](http://chromedriver.storage.googleapis.com/index.html)仓库中下载到



#### Firefox

selenium 调用geckodriver提供的python接口来驱动firefox浏览器。更多的了解[firefox说明文档](https://firefox-source-docs.mozilla.org/index.html)、[geckodriver github官网](https://github.com/mozilla/geckodriver)

下图展示[geckodriver版本](https://github.com/mozilla/geckodriver/releases)与Selenium和Firefox所需版本之间的对应关系： 

| geckodriver | Selenium             | Firefox  (min) | Firefox(max) |
| ----------- | -------------------- | -------------- | ------------ |
| 0.26.0      | ≥ 3.11 (3.14 Python) | 60             | n/a          |
| 0.25.0      | ≥ 3.11 (3.14 Python) | 57             | n/a          |
| 0.24.0      | ≥ 3.11 (3.14 Python) | 57             | n/a          |
| 0.23.0      | ≥ 3.11 (3.14 Python) | 57             | n/a          |
| 0.22.0      | ≥ 3.11 (3.14 Python) | 57             | n/a          |
| 0.21.0      | ≥ 3.11 (3.14 Python) | 57             | n/a          |
| 0.20.1      | ≥ 3.5                | 55             | 62           |
| 0.20.0      | ≥ 3.5                | 55             | 62           |
| 0.19.1      | ≥ 3.5                | 55             | 62           |
| 0.19.0      | ≥ 3.5                | 55             | 62           |
| 0.18.0      | ≥ 3.4                | 53             | 62           |
| 0.17.0      | ≥ 3.4                | 52             | 62           |

## 后记

@[TOC](这里写自定义目录标题)



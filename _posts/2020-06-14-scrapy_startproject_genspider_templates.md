---

layout:     post
title:      Scrapy Startproject Genspider Templates
subtitle:    "\"scrapy startproject genspider templates\""
date:       2020-06-14
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Scrapy

---
## 前言

Scrapy爬虫框架，在写多了genspider 爬虫模板的时候，就有点觉得有点繁琐的情况。

本文主要解释scrapy的模板创建的流程，最后实战一下创建属于自己的模板。

本文适合于对scrapy框架有一定的了解的读者。

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
     Scrapy 1.7.3
     python是虚拟环境
     在~/software/anaconda3/envs/YS/lib/python3.7
```

## 正文

回顾一下：

Scrapy的"Hello World"语句：

```sh
$ scrapy startproject helloScrapy
New Scrapy project 'helloScrapy', using template directory '~/software/anaconda3/envs/YS/lib/python3.7/site-packages/scrapy/templates/project', created in:
    ~/helloScrapy
You can start your first spider with:
    cd helloScrapy
    scrapy genspider example example.com
$ cd helloScrapy/
$ scrapy genspider helloscrapy baidu.com
Created spider 'helloscrapy' using template 'basic' in module:
  helloScrapy.spiders.helloscrapy
```

从第一行代码开始看起，细心的朋友就会发现在scrapy startproject helloScrapy这句运行时打印了一个地址：

```sh
New Scrapy project 'helloScrapy', using template directory '~/software/anaconda3/envs/YS/lib/python3.7/site-packages/scrapy/templates/project', 
```

字面意思：startproject使用了在~/software/anaconda3/envs/YS/lib/python3.7/site-packages/scrapy/templates/目录下面的project

~/software/anaconda3/envs/YS/lib/python3.7这个是我自己的python环境包所在目录，按照自己的python环境来看

```sh
stephen@stephen-PC:~/software/anaconda3/envs/YS/lib/python3.7/site-packages/scrapy/templates$ tree
.
├── project
│   ├── module
│   │   ├── __init__.py
│   │   ├── items.py.tmpl
│   │   ├── middlewares.py.tmpl
│   │   ├── pipelines.py.tmpl
│   │   ├── __pycache__
│   │   │   └── __init__.cpython-37.pyc
│   │   ├── settings.py.tmpl
│   │   └── spiders
│   │       ├── __init__.py
│   │       └── __pycache__
│   │           └── __init__.cpython-37.pyc
│   └── scrapy.cfg
└── spiders
    ├── basic.tmpl
    ├── crawl.tmpl
    ├── csvfeed.tmpl
    └── xmlfeed.tmpl

6 directories, 13 files
```

查看下面的结构，这有点熟悉呀，project下面的目录，这不是我们平时建立scrapy框架吗？只是后面多了tmpl的后缀。**这里先记住这个project目录文件架构和spider目录的文件。**



再看Scrapy的"Hello World"下一句（省去cd）

```sh
$ scrapy genspider helloscrapy baidu.com
Created spider 'helloscrapy' using template 'basic' in module:
  helloScrapy.spiders.helloscrapy
```

熟悉scrapy genspider的朋友都知道，(不是很了解的朋友可以使用scrapy genspider -h查看)他的全貌命令行是

```sh
$ scrapy genspider -t basic helloscrapy baidu.com
```

省去默认参数 -t **basic** 这个**basic**是不是有点熟悉，这不是上面的spider目录的文件的basic.tmpl吗？

有点意思。

怀着有点意思的心情去看scrapy框架的源码。看源码是一件非常有意思的事情。

经过一轮review源码，看到

![Image text](/img/scrapy_start_project_genspider_template_commands.png)

先对genspider.py生成一个spiders爬虫文件这个小单位看

```python
from __future__ import print_function
import os
import shutil
import string

from importlib import import_module
from os.path import join, dirname, abspath, exists, splitext

import scrapy
from scrapy.commands import ScrapyCommand
from scrapy.utils.template import render_templatefile, string_camelcase
from scrapy.exceptions import UsageError


def sanitize_module_name(module_name):
    """Sanitize the given module name, by replacing dashes and points
    with underscores and prefixing it with a letter if it doesn't start
    with one
    """
    module_name = module_name.replace('-', '_').replace('.', '_')
    if module_name[0] not in string.ascii_letters:
        module_name = "a" + module_name
    return module_name


class Command(ScrapyCommand):

    requires_project = False
    default_settings = {'LOG_ENABLED': False}

    def syntax(self):
        return "[options] <name> <domain>"

    def short_desc(self):
        return "Generate new spider using pre-defined templates"

    def add_options(self, parser):
        ScrapyCommand.add_options(self, parser)
        parser.add_option("-l", "--list", dest="list", action="store_true",
            help="List available templates")
        parser.add_option("-e", "--edit", dest="edit", action="store_true",
            help="Edit spider after creating it")
        parser.add_option("-d", "--dump", dest="dump", metavar="TEMPLATE",
            help="Dump template to standard output")
        parser.add_option("-t", "--template", dest="template", default="basic",
            help="Uses a custom template.")
        parser.add_option("--force", dest="force", action="store_true",
            help="If the spider already exists, overwrite it with the template")

    def run(self, args, opts):
        if opts.list:
            self._list_templates()
            return
        if opts.dump:
            template_file = self._find_template(opts.dump)
            if template_file:
                with open(template_file, "r") as f:
                    print(f.read())
            return
        if len(args) != 2:
            raise UsageError()

        name, domain = args[0:2]
        module = sanitize_module_name(name)

        if self.settings.get('BOT_NAME') == module:
            print("Cannot create a spider with the same name as your project")
            return

        try:
            spidercls = self.crawler_process.spider_loader.load(name)
        except KeyError:
            pass
        else:
            # if spider already exists and not --force then halt
            if not opts.force:
                print("Spider %r already exists in module:" % name)
                print("  %s" % spidercls.__module__)
                return
        template_file = self._find_template(opts.template)
        if template_file:
            self._genspider(module, name, domain, opts.template, template_file)
            if opts.edit:
                self.exitcode = os.system('scrapy edit "%s"' % name)

    def _genspider(self, module, name, domain, template_name, template_file):
        """Generate the spider module, based on the given template"""
        tvars = {
            'project_name': self.settings.get('BOT_NAME'),
            'ProjectName': string_camelcase(self.settings.get('BOT_NAME')),
            'module': module,
            'name': name,
            'domain': domain,
            'classname': '%sSpider' % ''.join(s.capitalize() \
                for s in module.split('_'))
        }
        if self.settings.get('NEWSPIDER_MODULE'):
            spiders_module = import_module(self.settings['NEWSPIDER_MODULE'])
            spiders_dir = abspath(dirname(spiders_module.__file__))
        else:
            spiders_module = None
            spiders_dir = "."
        spider_file = "%s.py" % join(spiders_dir, module)
        shutil.copyfile(template_file, spider_file)
        render_templatefile(spider_file, **tvars)
        print("Created spider %r using template %r " % (name, \
            template_name), end=('' if spiders_module else '\n'))
        if spiders_module:
            print("in module:\n  %s.%s" % (spiders_module.__name__, module))

    def _find_template(self, template):
        template_file = join(self.templates_dir, '%s.tmpl' % template)
        if exists(template_file):
            return template_file
        print("Unable to find template: %s\n" % template)
        print('Use "scrapy genspider --list" to see all available templates.')

    def _list_templates(self):
        print("Available templates:")
        for filename in sorted(os.listdir(self.templates_dir)):
            if filename.endswith('.tmpl'):
                print("  %s" % splitext(filename)[0])

    @property
    def templates_dir(self):
        _templates_base_dir = self.settings['TEMPLATES_DIR'] or \
            join(scrapy.__path__[0], 'templates')
        return join(_templates_base_dir, 'spiders')

```

过一下这个函数的逻辑，关键一个函数templates_dir()查找spider的模板文件，去python环境打印路径是

```python
import scrapy
scrapy.__path__[0]
Out[3]: '/home/stephen/software/anaconda3/envs/YS/lib/python3.7/site-packages/scrapy'
```

这个不是上面的路径

以-t basic命令行为例。略一下逻辑，他是先找到basic.tmpl文件再对该模板文件进行渲染render_templatefile(spider_file, **tvars)生成spider文件，底层是用了string.Template这个函数进行模板渲染。移动重命名py文件到指定位置。

备注：**tvars就是

```
 tvars = {
            'project_name': self.settings.get('BOT_NAME'),
            'ProjectName': string_camelcase(self.settings.get('BOT_NAME')),
            'module': module,
            'name': name,
            'domain': domain,
            'classname': '%sSpider' % ''.join(s.capitalize() \
                for s in module.split('_'))
        }
```



去细看basic.tmpl文件的内容

```python
# -*- coding: utf-8 -*-
import scrapy


class $classname(scrapy.Spider):
    name = '$name'
    allowed_domains = ['$domain']
    start_urls = ['http://$domain/']

    def parse(self, response):
        pass
```

这个不就是我们刚刚创建的helloscrapy.py文件吗

```python
# -*- coding: utf-8 -*-
import scrapy


class HelloscrapySpider(scrapy.Spider):
    name = 'helloscrapy'
    allowed_domains = ['baidu.com']
    start_urls = ['http://baidu.com/']

    def parse(self, response):
        pass

```

而那些参数变量，就是命令行传入。

```sh
$ scrapy genspider -t basic helloscrapy baidu.com
```

略清楚scrapy genspider的代码逻辑。接下来就是要创建属于自己的spider template，这个需要多写spider才有感觉，想到自己反复写重用的代码，就可以创建属于自己的spider template。

在'/home/stephen/software/anaconda3/envs/YS/lib/python3.7/site-packages/scrapy/spider'目录下创建test_basic.tmpl文件，文件情况如下。

```python
# -*- coding: utf-8 -*-
"""
	属于自己的spider模板
	更新日期：
	作者：
"""
import scrapy



class $classname(scrapy.Spider):
    name = '$name'
    allowed_domains = ['$domain']

    custom_settings = {

    }

    def parse(self, response):
        pass

```


测试
```sh
$ scrapy genspider -t test_basic test test.com
Created spider 'test' using template 'test_basic' in module:
  helloScrapy.spiders.test

```










## 后记

@[TOC](这里写自定义目录标题)



---

layout:     post
title:      MySQL Character and Collation
subtitle:    "\"MySQL Character and Collation\""
date:       2020-04-17
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - MySQL
    - Character
    - Collation


---


## 前言

了解ＭySQL新建数据库时出现字符集和排序规则

专业术语：

- 字符集（编码格式）　Character　charset
- 排序规则  Collation  collation

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
mysql version : 
		mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper	
```
## 正文
### １．字符集（编码格式）

概念：
ＭySQL有两个字符集概念:
一个就是字符集的本身
一个是字符集的校验规则。

影响：
字符集影响数据在传输和存储过程中的处理方式，而字符集校验规则影响ORDER BY和GROUP BY这些排序方式，字符集都相应着一个默认的字符集校验规则(COLLATION),当然一个字符集也可能相应多个校对规则,可是两个不同的字符集不能相应同一个规则。使用默认的就可以了。

#### 1.1 介绍

#####  字符集的本身


查看MySQL当前字符集：
```sql
mysql> show variables like 'char%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       | 
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.00 sec)
```

对应的含义解析：

|      字符集相关变量      |            含义            |                             含义                             |
| :----------------------: | :------------------------: | :----------------------------------------------------------: |
|   character_set_client   |   客户端数据使用的字符集   |       MySQL Client发送给mysqld的语句或数据使用字符集。       |
| character_set_connection |        连接层字符集        | 其实很多人对这个字符集一脸懵逼，这个字符集与character_set_client有啥区别呢？ 这个字符集用于没有introducer修饰的字符串和数字到字符串的转换。 |
|  character_set_database  |        数据库字符集        | MySQL可以给实例下不同数据库单独设置各自的字符集。这个跟SQL Server是类似的。 |
| character_set_filesystem |       文件系统字符集       | 文件系统字符集编码,主要用于解析用于文件名称的字符串字面值,如LOAD DATA INFILE和SELECT ...INTO OUTFILE等语句以及LOAD_FILE()函数 |
|  character_set_results   |       查询结果字符集       |     server返回给客户端的查询结果或者错误提示的字符集编码     |
|   character_set_server   | 服务器字符集，默认的字符集 | 服务器默认字符集编码,假设创建数据库的时候没有指定编码,则采用character_set_server指定编码 |
|   character_set_system   |      系统元数据字符集      | 它是系统元数据(表名、字段名等)存储时使用的编码字符集，该字段和具体存储的数据无关。总是固定不变的UTF8字符集 |
|    character_sets_dir    | /usr/share/mysql/charsets/ |                  mysql字符集编码存储文件夹                   |

**character_set_client、character_set_connection、character_set_results这3个参数值是由客户端每次连接进来设置的，和服务器端没关系。MySQL会存在不同字符集的转换过程，**



MySQL(4.1以后版本) 服务器中有六个关键位置使用了字符集的概念,

他们是　system、server、database、connection、client、results

查看数据库编码 (即字符集)

```sql
mysql> show create database mysql;
+----------+------------------------------------------------------------------+
| Database | Create Database                                                  |
+----------+------------------------------------------------------------------+
| mysql    | CREATE DATABASE `mysql` /*!40100 DEFAULT CHARACTER SET latin1 */ |
+----------+------------------------------------------------------------------+
```

#### 　1.2 字符集编码层次

```tex
   第一段主要说明字符集编码的说明，这部分主要说明下ＭySQL中字符集编码层次：服务端－>数据库－>　表－>字段
   关于系统的编码主要针对的是在存储文件的时候，有可能会将文件直接存贮在mysql的服务器上，那么，我们在数据库里面存的就是这些文件的路径，实际文件是存在系统里面的，那么文件名称就会受到你系统编码的影响，比如我们mysql设置的utf8编码的格式存储的文件路径，但是系统默认是gbk编码的，那么文件在保存到系统里的时候，文件的名称和你存在mysql里面的文件名称就对应不上了，出现乱码显示的问题，所以也要注意系统的编码。客户端的编码问题。以下mysql内部设置的这些编码问题。
   简单来说,服务器编码就是character_set_server来指定的.当我们创建数据库的时候能够指定编码,假设没有指定,采用的就是character_set_server指定的编码.比如:我们使用"create database t1 character set gbk",这里我们指定了数据库t1的编码为gbk,所以不会采用character_set_server指定的编码.而假设我们使用"create database t2",则通过"show create database t2"能够看到t2的编码为character_set_server定的编码.
   同理,mysql表也能够有自己独立的编码,在创建表的时候能够指定,假设没有指定,则默认采用数据库的编码.比方我们再之前的数据库t1创建表t11,"create table t11(i int) character set utf8",则表t11的编码为utf8,假设不指定编码则编码为数据库t1的编码gbk.
   此外,mysql表中的字段也能够有自己的编码,假设不指定字段编码,则字段编码与表的编码一致.
```


#### 　1.3 MySQL连接字符集
```tex
  前面谈到的编码内容基本都不会产生乱码问题,mysql中容易产生乱码的地方在character_set_client, character_set_connection, character_set_results这三个变量的设定.能够简单的通过set names utf8或者charset utf8命令来一次设置这三个參数.
  从文档中的解释来看,mysql连接字符集转换主要包含以下三个步骤:
  1. character_set_client是client发送过来的sql语句的编码,由于服务端本身并不知道client的sql语句的编码是什么,所以是以这个变量作为clientsql语句的初始编码.而服务端接收到sql语句后,则会将sql语句转换为character_set_connection指定的编码(注意,对于字面值字符串,假设前面有introducer标记如latin1或utf8,则不会进行这一步转换).转换完毕,才会真正运行sql语句.
  2. 进行内部操作前将sql语句中的数据从character_set_connection转换为数据表中对应字段的编码.
  3. 将操作结果从内部字符集编码转换为character_set_results编码.
```


### 2.排序规则

排序规则：
    是指对指定字符集下不同字符的比较规则。(这个跟字符集的校验规则差不多)

其特征有以下几点：

1、 两个不同的字符集不能有相同的排序规则

2、 两个字符集有一个默认的排序规则

3、 有一些常用的命名规则。如_ci结尾表示大小写不敏感（caseinsensitive）,_cs表示大小写敏感（case sensitive）,_bin表示二进制的比较（binary）。

####  字符集的排序规则

查看MySQL支持的字符集和默认排序规则

注：Maxlen，字符集的一个字符占用的最大字节数

```sql
mysql> show character set;
+----------+---------------------------------+---------------------+--------+
| Charset  | Description                     | Default collation   | Maxlen |
+----------+---------------------------------+---------------------+--------+
| big5     | Big5 Traditional Chinese        | big5_chinese_ci     |      2 |
| dec8     | DEC West European               | dec8_swedish_ci     |      1 |
| cp850    | DOS West European               | cp850_general_ci    |      1 |
| hp8      | HP West European                | hp8_english_ci      |      1 |
| koi8r    | KOI8-R Relcom Russian           | koi8r_general_ci    |      1 |
| latin1   | cp1252 West European            | latin1_swedish_ci   |      1 |
| latin2   | ISO 8859-2 Central European     | latin2_general_ci   |      1 |
| swe7     | 7bit Swedish                    | swe7_swedish_ci     |      1 |
| ascii    | US ASCII                        | ascii_general_ci    |      1 |
| ujis     | EUC-JP Japanese                 | ujis_japanese_ci    |      3 |
| sjis     | Shift-JIS Japanese              | sjis_japanese_ci    |      2 |
| hebrew   | ISO 8859-8 Hebrew               | hebrew_general_ci   |      1 |
| tis620   | TIS620 Thai                     | tis620_thai_ci      |      1 |
| euckr    | EUC-KR Korean                   | euckr_korean_ci     |      2 |
| koi8u    | KOI8-U Ukrainian                | koi8u_general_ci    |      1 |
| gb2312   | GB2312 Simplified Chinese       | gb2312_chinese_ci   |      2 |
| greek    | ISO 8859-7 Greek                | greek_general_ci    |      1 |
| cp1250   | Windows Central European        | cp1250_general_ci   |      1 |
| gbk      | GBK Simplified Chinese          | gbk_chinese_ci      |      2 |
| latin5   | ISO 8859-9 Turkish              | latin5_turkish_ci   |      1 |
| armscii8 | ARMSCII-8 Armenian              | armscii8_general_ci |      1 |
| utf8     | UTF-8 Unicode                   | utf8_general_ci     |      3 |
| ucs2     | UCS-2 Unicode                   | ucs2_general_ci     |      2 |
| cp866    | DOS Russian                     | cp866_general_ci    |      1 |
| keybcs2  | DOS Kamenicky Czech-Slovak      | keybcs2_general_ci  |      1 |
| macce    | Mac Central European            | macce_general_ci    |      1 |
| macroman | Mac West European               | macroman_general_ci |      1 |
| cp852    | DOS Central European            | cp852_general_ci    |      1 |
| latin7   | ISO 8859-13 Baltic              | latin7_general_ci   |      1 |
| utf8mb4  | UTF-8 Unicode                   | utf8mb4_general_ci  |      4 |
| cp1251   | Windows Cyrillic                | cp1251_general_ci   |      1 |
| utf16    | UTF-16 Unicode                  | utf16_general_ci    |      4 |
| utf16le  | UTF-16LE Unicode                | utf16le_general_ci  |      4 |
| cp1256   | Windows Arabic                  | cp1256_general_ci   |      1 |
| cp1257   | Windows Baltic                  | cp1257_general_ci   |      1 |
| utf32    | UTF-32 Unicode                  | utf32_general_ci    |      4 |
| binary   | Binary pseudo charset           | binary              |      1 |
| geostd8  | GEOSTD8 Georgian                | geostd8_general_ci  |      1 |
| cp932    | SJIS for Windows Japanese       | cp932_japanese_ci   |      2 |
| eucjpms  | UJIS for Windows Japanese       | eucjpms_japanese_ci |      3 |
| gb18030  | China National Standard GB18030 | gb18030_chinese_ci  |      4 |
+----------+---------------------------------+---------------------+--------+
41 rows in set (0.00 sec)

```

查看MySQL支持的utf8字符集和utf8mb4的区别

```sh
mysql> show charset like 'utf8%';
+---------+---------------+--------------------+--------+
| Charset | Description   | Default collation  | Maxlen |
+---------+---------------+--------------------+--------+
| utf8    | UTF-8 Unicode | utf8_general_ci    |      3 |
| utf8mb4 | UTF-8 Unicode | utf8mb4_general_ci |      4 |
+---------+---------------+--------------------+--------+
2 rows in set (0.00 sec)
```
两条字符集数据 utf8 和 utf8mb4 ，前者单字符最长3个字节，后者单字符最长4个字节，前者就是俗称的“阉割版”utf8，是MySQL开发工程人员为了节省存储空间而推出的字符集（MySQL行存储时，对于变长字符串varchar类型计算存储空间是按其使用字符集的最长字节数计算的），后者单字符最长4个字节，就是我们标准版的utf8字符集。


查看utf-8有默认的排序规则：

```shell
mysql> show collation like 'utf8_%';
+--------------------------+---------+-----+---------+----------+---------+
| Collation                | Charset | Id  | Default | Compiled | Sortlen |
+--------------------------+---------+-----+---------+----------+---------+
| utf8_general_ci          | utf8    |  33 | Yes     | Yes      |       1 |
| utf8_bin                 | utf8    |  83 |         | Yes      |       1 |
| utf8_unicode_ci          | utf8    | 192 |         | Yes      |       8 |
| utf8_icelandic_ci        | utf8    | 193 |         | Yes      |       8 |
| utf8_latvian_ci          | utf8    | 194 |         | Yes      |       8 |
| utf8_romanian_ci         | utf8    | 195 |         | Yes      |       8 |
| utf8_slovenian_ci        | utf8    | 196 |         | Yes      |       8 |
| utf8_polish_ci           | utf8    | 197 |         | Yes      |       8 |
| utf8_estonian_ci         | utf8    | 198 |         | Yes      |       8 |
| utf8_spanish_ci          | utf8    | 199 |         | Yes      |       8 |
| utf8_swedish_ci          | utf8    | 200 |         | Yes      |       8 |
| utf8_turkish_ci          | utf8    | 201 |         | Yes      |       8 |
| utf8_czech_ci            | utf8    | 202 |         | Yes      |       8 |
| utf8_danish_ci           | utf8    | 203 |         | Yes      |       8 |
| utf8_lithuanian_ci       | utf8    | 204 |         | Yes      |       8 |
| utf8_slovak_ci           | utf8    | 205 |         | Yes      |       8 |
| utf8_spanish2_ci         | utf8    | 206 |         | Yes      |       8 |
| utf8_roman_ci            | utf8    | 207 |         | Yes      |       8 |
| utf8_persian_ci          | utf8    | 208 |         | Yes      |       8 |
| utf8_esperanto_ci        | utf8    | 209 |         | Yes      |       8 |
| utf8_hungarian_ci        | utf8    | 210 |         | Yes      |       8 |
| utf8_sinhala_ci          | utf8    | 211 |         | Yes      |       8 |
| utf8_german2_ci          | utf8    | 212 |         | Yes      |       8 |
| utf8_croatian_ci         | utf8    | 213 |         | Yes      |       8 |
| utf8_unicode_520_ci      | utf8    | 214 |         | Yes      |       8 |
| utf8_vietnamese_ci       | utf8    | 215 |         | Yes      |       8 |
| utf8_general_mysql500_ci | utf8    | 223 |         | Yes      |       1 |
| utf8mb4_general_ci       | utf8mb4 |  45 | Yes     | Yes      |       1 |
| utf8mb4_bin              | utf8mb4 |  46 |         | Yes      |       1 |
| utf8mb4_unicode_ci       | utf8mb4 | 224 |         | Yes      |       8 |
| utf8mb4_icelandic_ci     | utf8mb4 | 225 |         | Yes      |       8 |
| utf8mb4_latvian_ci       | utf8mb4 | 226 |         | Yes      |       8 |
| utf8mb4_romanian_ci      | utf8mb4 | 227 |         | Yes      |       8 |
| utf8mb4_slovenian_ci     | utf8mb4 | 228 |         | Yes      |       8 |
| utf8mb4_polish_ci        | utf8mb4 | 229 |         | Yes      |       8 |
| utf8mb4_estonian_ci      | utf8mb4 | 230 |         | Yes      |       8 |
| utf8mb4_spanish_ci       | utf8mb4 | 231 |         | Yes      |       8 |
| utf8mb4_swedish_ci       | utf8mb4 | 232 |         | Yes      |       8 |
| utf8mb4_turkish_ci       | utf8mb4 | 233 |         | Yes      |       8 |
| utf8mb4_czech_ci         | utf8mb4 | 234 |         | Yes      |       8 |
| utf8mb4_danish_ci        | utf8mb4 | 235 |         | Yes      |       8 |
| utf8mb4_lithuanian_ci    | utf8mb4 | 236 |         | Yes      |       8 |
| utf8mb4_slovak_ci        | utf8mb4 | 237 |         | Yes      |       8 |
| utf8mb4_spanish2_ci      | utf8mb4 | 238 |         | Yes      |       8 |
| utf8mb4_roman_ci         | utf8mb4 | 239 |         | Yes      |       8 |
| utf8mb4_persian_ci       | utf8mb4 | 240 |         | Yes      |       8 |
| utf8mb4_esperanto_ci     | utf8mb4 | 241 |         | Yes      |       8 |
| utf8mb4_hungarian_ci     | utf8mb4 | 242 |         | Yes      |       8 |
| utf8mb4_sinhala_ci       | utf8mb4 | 243 |         | Yes      |       8 |
| utf8mb4_german2_ci       | utf8mb4 | 244 |         | Yes      |       8 |
| utf8mb4_croatian_ci      | utf8mb4 | 245 |         | Yes      |       8 |
| utf8mb4_unicode_520_ci   | utf8mb4 | 246 |         | Yes      |       8 |
| utf8mb4_vietnamese_ci    | utf8mb4 | 247 |         | Yes      |       8 |
+--------------------------+---------+-----+---------+----------+---------+
53 rows in set (0.00 sec)
```

```tex
排序一般分为两种：utf_bin和utf_general_ci

bin 是二进制, a 和 A 会别区别对待.

例如你运行:

SELECT * FROM table WHERE txt = 'a'

那么在utf8_bin中你就找不到 txt = 'A' 的那一行, 而 utf8_general_ci 则可以.

utf8_general_ci 不区分大小写，这个你在注册用户名和邮箱的时候就要使用。
utf8_general_cs 区分大小写，如果用户名和邮箱用这个 就会照成不良后果。
utf8_bin:字符串每个字符串用二进制数据编译存储。 区分大小写，而且可以存二进制的内容。

utf8_general_ci 校对速度快，但准确度稍差。
utf8_unicode_ci准确度高，但校对速度稍慢。
```



## 后记
使用MySQL字符集时的建议

- 建立数据库/表和进行数据库操作时尽量显式指出使用的字符集，而不是依赖于MySQL的默认设置，否则MySQL升级时可能带来很大困扰；
- 数据库和连接字符集都使用latin1时，虽然大部分情况下都可以解决乱码问题，但缺点是无法以字符为单位来进行SQL操作，一般情况下将数据库和连接字符集都置为utf8是较好的选择；
      

@[TOC](这里写自定义目录标题)



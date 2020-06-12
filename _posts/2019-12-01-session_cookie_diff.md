---
layout:     post
title:      Different Bewteen Session And Cookie
subtitle:    "\"different bewteen session and cookie\""
date:       2019-12-01
author:     Stephen
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Session
    - Cookie
---
## 前言
本文讲述Session和Cookie的区别和联系。

你可能有留意到当你浏览网页时，网站会有一些推送消息，大多数是你最近留意过的同类东西，比如你想买镜子，上某宝搜了一下，结果连着几天会有各种各样镜子的链接。(是不是有被偷窥的感觉)

这是因为你浏览某个网页的时候，WEB 服务器会先送一些资料放在你的计算机上，类似于你打的文字，选的一些东西什么的，Cookie 会帮你都纪录下来。当下次你再光临同一个网站，WEB服务器会先看看有没有它上次留下的 Cookie 资料，有的话，就会依据 Cookie里的内容来判断使用者，送出特定的网页内容给你。 Cookie 的使用很普遍，许多有提供个人化服务的网站，都是利用 Cookie来辨认使用者，以方便送出使用者量身定做的内容。

然而，Cookie是什么呢？Session又是什么？

## 正文

#### 了解几个概念先

1、无状态的HTTP协议：

协议是指计算机通信网络中两台计算机之间进行通信所必须共同遵守的规定或规则**(这就是计算机的魅力－通信协议)**，超文本传输协议(HTTP)是一种通信协议，它允许将超文本标记语言(HTML)文档从Web服务器传送到客户端的浏览器。

HTTP协议是无状态的协议。一旦数据交换完毕，客户端与服务器端的连接就会关闭，再次交换数据需要建立新的连接。这就意味着服务器无法从连接上跟踪会话。

2、会话（Session）跟踪：

会话，指用户登录网站后的一系列动作，比如浏览商品添加到购物车并购买。会话（Session）跟踪是Web程序中常用的技术，用来跟踪用户的整个会话。

常用的会话跟踪技术是Cookie与Session。**Cookie通过在客户端记录信息确定用户身份，Session通过在服务器端记录信息确定用户身份。**

### 一. Cookie

由于HTTP是一种无状态的协议，服务器单从网络连接上无从知道客户身份。用户A购买了一件商品放入购物车内，当再次购买商品时服务器已经无法判断该购买行为是属于用户A的会话还是用户B的会话了。怎么办呢？就给客户端们颁发一个通行证吧，每人一个，无论谁访问都必须携带自己通行证。这样服务器就能从通行证上确认客户身份了。这就是Cookie的工作原理。

Cookie实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端会把Cookie保存起来。

当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie，以此来辨认用户状态。服务器还可以根据需要修改Cookie的内容。

##### 1、Cookie的内容主要包括：名字，值，过期时间，路径和域。路径与域一起构成Cookie的作用范围。

1）Name 和 Value 属性由程序设定,默认值都是空引用。

2）Domain属性的默认值为当前URL的域名部分，不管发出这个Cookie的页面在哪个目录下的。

3）Path属性的默认值是根目录，即 ”/” ，不管发出这个Cookie的页面在哪个目录下的。可以由程序设置为一定的路径来进一步限制此Cookie的作用范围。

4）Expires 属性，这个属性设置此Cookie 的过期日期和时间。

```java
HttpCookie cookie = new HttpCookie("MyPersonCook");//初使化并设置Cookie的名称
DateTime dt = DateTime.Now;
TimeSpan ts = new TimeSpan(0, 0, 2, 0, 0);//过期时间为2分钟
cookie.Expires = dt.Add(ts);//设置过期时间
cookie.Values.Add("userid", "value");
cookie.Values.Add("userid2", "value2");
Response.AppendCookie(cookie);
```

##### 2、Path和Domain属性

--path:

如果http://www.baidu.com/test/index.html 建立了一个Cookie，那么在http://www.baidu.com/test/目录里的所有页面，以及该目录下面任何子目录里的页面都可以访问这个Cookie。

这就是说，在http://www.baidu.com/test/test2/test3 里的任何页面都可以访问http://www.baidu.com/test/index.html建立的Cookie。

但是，如果http://www.baidu.com/test/ 需要访问http://www.baidu.com/test/index.html设置的Cookies，该怎么办？

这时，我们要把Cookies的path属性设置成“/”。在指定路径的时候，凡是来自同一服务器，URL里有相同路径的所有WEB页面都可以共享Cookies。

--Domain:

比如： http://www.baidu.com/xxx/login.aspx 页面中发出一个Cookie，Domain属性缺省就是www.baidu.com ，可以由程序设置此属性为需要的值。　　

值是域名，比如www.china.com。这是对path路径属性的一个延伸。如果我们想让 www.china.com能够访问bbs.china.com设置的Cookies，该怎么办? 我们可以把domain属性设置成“china.com”， 并把path属性设置成“/”。

##### 3、会话Cookie和持久Cookie

若不设置过期时间，则表示这个Cookie的生命期为浏览器会话期间，关闭浏览器窗口，Cookie就消失。这种生命期为浏览器会话期的Cookie被称为会话Cookie。会话Cookie一般不存储在硬盘上而是保存在内存里，当然这种行为并不是规范规定的。

若设置了过期时间，浏览器就会把Cookie保存到硬盘上，关闭后再次打开浏览器，这些Cookie仍然有效直到超过设定的过期时间。存储在硬盘上的Cookie可以在浏览器的不同进程间共享。这种称为持久Cookie。 

##### 4、Cookie具有不可跨域名性

就是说，浏览器访问百度不会带上谷歌的Cookie。

### 二. Session

Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。这就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。

每个用户访问服务器都会建立一个Session，那服务器是怎么标识用户的唯一身份呢？事实上，用户与服务器建立连接的同时，服务器会自动为其分配一个SessionId。

##### 1、两个问题：

1）什么东西可以让你每次请求都把SessionId自动带到服务器呢？显然就是Cookie了，如果你想为用户建立一次会话，可以在用户授权成功时给他一个唯一的Cookie。当一个用户提交了表单时，浏览器会将用户的SessionId自动附加在HTTP头信息中，（这是浏览器的自动功能，用户不会察觉到），当服务器处理完这个表单后，将结果返回给SessionId所对应的用户。试想，如果没有 SessionId，当有两个用户同时进行注册时，服务器怎样才能知道到底是哪个用户提交了哪个表单呢。

2）储存需要的信息。服务器通过SessionId作为key，读写到对应的value，这就达到了保持会话信息的目的。

##### 2、Session的创建：

当程序需要为某个客户端的请求创建一个Session时，服务器首先检查这个客户端的请求里是否已包含了SessionId，如果已包含则说明以前已经为此客户端创建过Session，服务器就按照SessionId把这个Session检索出来使用（检索不到，会新建一个），如果客户端请求不包含SessionId，则为此客户端创建一个Session并且生成一个与此Session相关联的SessionId，SessionId的值是一个既不会重复，又不容易被找到规律以仿造的字符串，这个SessionId将被在本次响应中返回给客户端保存。

##### 3、禁用Cookie：
如果客户端禁用了Cookie，通常有两种方法实现Session而不依赖Cookie。

1）URL重写，就是把SessionId直接附加在URL路径的后面。

2）表单隐藏字段。就是服务器会自动修改表单，添加一个隐藏字段，以便在表单提交时能够把Session id传递回服务器。比如： 

``` html
<form name="testform" action="/xxx"> 
<input type="hidden" name="jsessionid" value="ByOK3vjFD75aPnrF7C2HmdnV6QZcEbzWoWiBYEnLerjQ99zWpBng!-145788764"> 
<input type="text"> 
</form> 
```
##### 4、Session共享：

对于多网站(同一父域不同子域)单服务器，我们需要解决的就是来自不同网站之间SessionId的共享。由于域名不同(aaa.test.com和bbb.test.com)，而SessionId又分别储存在各自的Cookie中，因此服务器会认为对于两个子站的访问,是来自不同的会话。

解决的方法是通过修改Cookies的域名为父域名达到Cookie共享的目的,从而实现SessionId的共享。带来的弊端就是，子站间的Cookie信息也同时被共享了。

### 三、总结
1、Cookie数据存放在客户的浏览器上，Session数据放在服务器上。

2、单个Cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个Cookie。

3、可以考虑将登陆信息等重要信息存放为Session，其他信息如果需要保留，可以放在Cookie中。

4、Cookie不是很安全，别人可以分析存放在本地的Cookie并进行Cookie欺骗，考虑到安全应当使用Session。

5、Session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能，考虑到减轻服务器性能方面，应当使用Cookie。




## 后记


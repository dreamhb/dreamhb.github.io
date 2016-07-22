---
layout: post
title: Http Cookie
excerpt: "Http Cookie 是网站发送并存储在用户浏览器上的一小段数据"
modified: 2015-11-06
tags: [http]
comments: true
image:
    feature: sample-image-5.jpg
    credit: maomao 
---


节选自  <https://en.wikipedia.org/wiki/HTTP_cookie>

**Http Cookie** 又叫做**web cookie**、**浏览器cookie**等，是一小段服务器发送给用户浏览器的数据，当用户浏览网页的时候。

###用途###
用来帮助网站记住一些状态类的信息，比如购物车中的物品，或者记录用户的行为，比如记录按钮点击，登录，页面访问等。也可以用来保存用户密码和表单内容，比如信用卡号和用户地址。

Cookie在现代的web中扮演者十分重要的作用。**authentication cookies** 或许是最常用的来帮助服务器来判断用户是否登录的方式了。而其安全性取决于网站和用户的浏览器，还有cookie数据是否加密。

###一些术语


#####Session Cookie
是内存中的cookie或者是临时的cookie，仅当用户浏览网站时存在于用户的浏览器中。当用户关闭掉浏览器时，浏览器会删除掉这个。

#####持久Cookie
可以保存任何时间，随创建者而定，长用来统计用户数据，学习用户习惯然后推送广告。

#####安全Cookie
其只能通过加密过的连接传递(比如HTTPS)。较少的机会会被偷听到。


###使用

#####会话管理
Cookie最初引入是为了用户去记录他们买了啥当他们在浏览网站的时候。
然而，现在用户的购物卡通常存储在服务端的数据库中，而不是客户端的cookie了。为了追踪哪个用户对应哪个购物卡，web服务器发送一个cookie到客户端，这个cookie包含一个**唯一会话标志符**（典型的是一个长字符串，用随机的字符和数字组成）。

另外一个cookie的特殊用途就是用来登陆网站的。

因为会话cookie只包含一个唯一的会话标识符。

#####个性化

#####追踪

###实现
Cookies是任意的数据，通常是取决于web服务器的选择，存储在客户电脑的浏览器中。

#####设置Cookie
Cookies通过Http的 Set-Cookie 头来设置，并用HTTP 响应来发送。


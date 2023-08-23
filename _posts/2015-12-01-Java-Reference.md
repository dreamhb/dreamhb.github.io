---
layout: post
title:  Java Reference
excerpt: "Java中的各种引用"
modified: 2015-12-01
tags: [java]
comments: true
image:
feature: sample-image-6.jpg
---
在Java中有以下几种引用类别：

**强引用（默认使用new关键字创建的）**

**软引用**
软引用的对象在判定GC响应内存需求的时候被回收。常用来实现内存敏感的缓存

**弱引用**
弱引用的对象不会阻止他们的引用者被 ***finalzable***, ***finalized***, 然后回收掉。常用与规范化转换映射的实现。

**虚引用**
虚引用的对象，当GC判定出他们的引用者可能被回收时，就将它入队。其经常应用于计划生前的清理操作用一种更灵活的方式，比起Java finalization 机制。

**终引用**

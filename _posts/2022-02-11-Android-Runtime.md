---
layout: post
title:  "Android Runtime"
date:   2022-02-11 17:50:00
comments: true
categories: java
---

本文希望对Android运行时即虚拟机的垃圾回收机制,做一个梳理.介绍从标准的JVM,到Dalvik,再到ART,从而对Android Runtime的变迁有一个整体的了解.

因为最近的项目中使用可KMM(Kotlin Multiple Mobile)框架来实现代码跨平台复用,关于KMM后面会专门介绍。最初的版本的实现，在将Android的代码移植到iOS上，会出现内存管理上的问题，因为老的版本的内存管理和常见的很不一样，它要求如果对象要在线程间共享，那么必须是Frozen状态，并且它引用的对象都将被Freeze，处于Frozen状态的对象是不可以修改的。这对于熟悉Mobile开发的我们来说，是不太一样的，同时需要花时间去学习。当然也有性能方面的问题。

所以在最新的版本中，采用了新的内存管理方式，将原有的基于Reference-Counting的垃圾回收方式，改成了基于Tracing的CMS(Concurrent Mark Sweep)。

而Android的开发也是基于JVM的，只是实现不一样，所以就想把这一块的知识捋一捋。

本文主要涉及三个部分，第一部分是标准的JVM垃圾回收算法和实践，第二部分是Android 5.0 以前所采用Dalvik，以及第三部分4.4引入，5.0正式替换Dalvik的ART。

* TOC
{:toc}

## 常见的GC算法以及实现

### Reference Counting vs. Tracing

垃圾回收有两种基本的方式，一种是引用计数法（Reference Counting），另一种则是可达法（Tracing），目前JVM中的算法都是基于Tracing的，这是因为Reference Counting有很多缺陷，比如
*   不易处理循环引用
*   存储引用对空间的占用
*   对引用数值进行加减运算对性能的影响，以及原子化操作的成本
*   并不是实时的

它的优势在于实现相对简单，这是KMM最初选择此种方式的原因,并且主要引用于C++中的智能指针，Objective-C中的ARC等。

Tracing则是从Root开始遍历,如果reachable,说明该内存不能被回收,反之既可以回收.
Root一般包括:
*   方法变量

Tracing有两种基本算法:
*   Mark-Sweep  
  标记清除分两个阶段，一个是标记，一个是清除。
    * 标记指的是，将Root集合可达和间接可达的标记为“在使用”
    * 清除阶段是将上一步没有标记为使用的对象回收掉，并将已有的标记清除
    
    这种方法的缺点在于，如果是很简单的实现，在回收阶段，会暂停整个系统，无法做其他的事情。
*   Tri-color Marking  
    三色标记法，将内存中的对象分为三个集合，分别是White、Black和Gray,White集合中的对象或者说White对象是可以被回收的对象,Black对象从Root可达，但是没有到White对象的引用，Gray对象也可以从Root可达，但是并不清楚是否有引用White对象，扫描过后，挪动到Black集合中。
    *   过程
        *   初始状态  
            Black集合为空，Gray集合中存有Root直接引用的对象，White中保存其他的对象
        *   在Gray集合中选择一个对象放到Black集合中
        *   将该对象引用的White对象，移动到Gray集合中
        *   重复上述两步，直到Gray集合为空  

    这种方式有以下几个优势：
    *   不会暂停系统很长时间
    *   不需要在每次循环都把对象遍历一遍
    *   因为Black集合不会引用White集合中的对象，所以当Gray集合为空时，既可以回收White集合对象了
    *   通过检测Gray集合的容量，就可以间歇性的回收不再使用的对象，而不用等到内存分配失败时。

上面介绍了两种基本的内存回收算法，下面介绍实现中的一些策略：
*   Moving vs. non-Moving (Compacting vs. non-Compacting)  
    移动对象有几个优势:
    *   将存活的对象移动后,剩下的就是可以直接分配对象的内存,无需多余的处理
    *   新对象也可以很快的处理
    *   通过合适的遍历顺序,可以提高缓存的命中率,可以将即将用到的对象,放在一起
    *   可以减少内存碎片
*   Stop-the-World vs. Incremental vs. Concurrent  
    *   Stop-the-World会导致应用暂停,如果时间过长,则不适用于交互性的应用,比如移动应用,其优点在于实现相对简单
    *   Incremental的方式将GC循环分散开,以减少应用停顿的时间,比如按内存分代(Genenrational)就是这样一种方式
    *   Concurrent 不会暂停应用,只在某些必须阶段暂停引用,或用其他技术手段,比如Read-Barrier,避免暂停应用,非常适合交互性应用
*   Copying
    *   Copying 典型的是Copy-and-Stop，常见的是Semi-Space-Collector，将内存分为两部分，每一次都将存活的对象拷贝到另一部分，并将原来的部分当作新内存，用于分配新对象.  
        这种方式比较费内存,使用效率不高,但是可以减少内存碎片.
*   Generational GC  
    根据实际使用中的经验，大部分的对象都是很快不再使用，只有少部分对象会存活较长时间。据此可以调整GC的策略。有的内存，比如Android也是按照对象的存活时间，来对内存进行分区的，比如Young，Old和Permanent。

### JVM中常用的Garbage Collector
*   G1
*   CMS
*   Serial
*   Parallel
*   C4
## Dalvik  
在Android诞生的那个年代,无论是CPU、内存、闪存都和今天不能比，内存最低128M，相同点都是用电池。受限于上述条件，Dalvik的设计和标准的JVM是不一样的。它基于寄存器而标准的JVM都是基于栈的，同时Dalvik对Class文件进一步的优化，使用dex格式，并且使用Zygote用于VM的热启动和分享公共部分。这一部分的具体内容，后面再详细介绍。  

Dalvik使用的CMS（Concurrent Mark Sweep）。

### 过程
*   找出的Root集合,其中是所有的根节点,需要暂停应用
*   第一次Mark,将Root集合中的对象和Reachable的对象标记为‘使用中’,这个过程是并发的,不会阻塞应用
*   第二次Mark,因为上一次过程是并发的,应用可能有新分配的对象,需要重新标记,这个过程也是需要暂停的
*   回收不再使用的对象内存,此过程是并发的

### 特点
Dalvik使用的CMS垃圾回收器，它有以下几个特点：
*   单线程
*   没有内存压缩功能，意味着随着应用的运行，会有越来越多的内存碎片
*   一次循环，会有两次暂停，即Stop-the-World，一共会有10ms左右，会导致Frame drop
*   如果因为分配内存失败，而导致的GC，即GC_FOR_ALLOC,会将应用暂停50ms左右,对用户来说会明显的卡顿


因为上述的不足，而且随着硬件的改善，从Android 4.4开始，引入了下面会提到的ART，并在5.0正式替换了Dalvik。
## ART
ART是Android RunTime的缩写，从4.4开始，到现在的12，已经迭代了很多版本了，其支持的垃圾回收策略也不止一个。

### 4.4 - 6.0
ART继续使用CMS，但是对其做了很大的优化，主要有减少应用暂停，减少内存碎片，以及对内存分配的改进。
下面看一下其过程：

#### CMS过程
*   找出Root集合，不同于Dalvik，这一步是并发的，通过让各个应用自己标记各自的Stack
*   第一次Mark，并发标记
*   第二次Mark，会暂停，但是会短很多，3ms左右，用的技术是？
*   并发回收不再使用的对象内存

通过引入LOS，即Large Object Space，消除了Dalvik分配大内存触发GC_FOR_ALLOC导致的长时间暂停，同时减少了内存碎片。

并且在系统启动和应用在后台时，使用Semi-Space-Collector（Moving Collector）进行碎片整理，减少内存碎片。
### 7.0
这个版本，将这一部分用汇编语言重写。
### 8.0 - 9.0
Oreo版本引入了新的GC，并且是默认的，即CC（Concurrent Copying），另外一个是已有的CMS。

#### CC(Concurrent Heap Compacting Collector)
*   通过Read-Barrier当应用处于前台时,也可以去内存碎片
*   因为每个应用的内存使用量减少了,间接的节省了整个系统的内存
*   RegionTLAB是的内存的分配效率提高
*   I/O大会上说分代GC在这个版本没有了,有待查证具体细节
### 10+
从这个版本开始,CC默认开启Generational模式,这里的Generational模式,和Heap的根据对象的存活时间来分区还不一样,CC只有两种GC,一种是Young CC,另一种是Full Heap CC.  

当Young CC的Throughout比Full Heap CC要小时,下次GC会切换到Full Heap CC.

CMS则始终是Generational模式.


#### 引用


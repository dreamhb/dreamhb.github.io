---
layout: default
title:  "Android内存溢出总结"
date:   2018-04-04 13:53:23
comments: true
categories: main
---

![](../images/memory_view?raw=true)
![](../images/detail_view?raw=true)

1.
在开发稍微大型一点的Android App的时候，稍不小心，可能就会因为内存溢出
而碰到OutOfMemory异常，而使应用崩溃。

2.虽然Java有垃圾回收机制，大大减轻了我们在程序开发时的内存管理难度，
但是处理不好，仍然会有内存问题，典型的就是内存溢出。
Android App开发中的主要内存溢出，都是因为Activity或者Context在销毁时，
没有正确的被释放，仍然被某个引用所引用，导致垃圾回收时，没有办法将
Activity回收，从而导致内存一直被占用;而且随着使用时间的增加，应用所占的内存
会越来越大，最终在某个操作申请内存的时候，因为Heap的值到达最大数量，而爆出
OutOfMemory异常，应用崩溃。

3.这种问题，从表面上看起来，是最后一次内存请求导致的问题，实际上并不是。所以给我们的分析造成了困难，幸好，Android Studio给我们提供了一个很好用的内存分析工具Android Profiler。

4.打开Android Profiler界面，会有CPU、内存和网络，点击内存选项，会看到内存试图。
会随着时间的变化而变化。点击左上方的垃圾桶按钮，触发内存回收，然后点击垃圾桶旁边的按钮，获取到当前的app内存heap的情况。
其中会列出当前heap中有哪些实例，如果某个已经被销毁的Activity仍然在其中，那么可以断定这个Activity发生了内存泄漏。接下来就要分析，到底是那个实例或者变量导致这个问题。点击左边的某个Activity，会在右边的试图中看到这个Activity的实例，展开便可看到其中有哪些引用。

5。在分析具体哪个实例导致的内存溢出，我们采用了Square开源的LeakCanary框架，如果有内存泄漏，LeakCanary会直接显示出，是哪个变量导致的内存泄漏，方便我们快速的定位问题。

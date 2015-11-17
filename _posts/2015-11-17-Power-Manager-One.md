---
layout: post
title: Android Power Manager (一)
excerpt: WakeLock用来控制屏幕的亮或者是不亮，如果要让屏幕常亮，建议使用android.view.WindowManager.LayoutParams#FLAG_KEEP_SCREEN_ON
modified: 2015-11-17
tags: [Android]
comments: true
image:
    feature: sample-image-5.jpg
    credit: maomao 
---

####需要了解的几个概念

####Work Lock Level

######PARTIAL_WAKE_LOCK
CPU是一直运行的，但是屏幕和键盘灯可以关闭。如果用户按了**POWER**键，屏幕会关闭，但是CPU会持续运行知道所有的此类wake lock都被释放为止。

######SCREEN_DIM_WAKE_LOCK
确保屏幕是常亮的，但是可以关背光灯，键盘灯也允许关闭。

如果用户按了**POWER**键，那么此wake lock就会被系统自动释放了，现象就是屏幕和CPU都关闭。

######SCREEN_BRIGHT_WAKE_LOCK
保证了屏幕是最大亮度，键盘灯还是允许关闭的，这个应该就是支付宝等扫码支付时需要的。

如果用户按了**POWER**键，那么此wake lock就会被系统自动释放了，现象就是屏幕和CPU都关闭。

######FULL_WAKE_LOCK
屏幕和键盘等都是最大亮度的常亮。不过已经弃用了。

如果用户按了**POWER**键，那么此wake lock就会被系统自动释放了，现象就是屏幕和CPU都关闭。

**此外还有一些隐藏的LEVEL，就不一一介绍了**

####Wake Lock Flag

#####ACQUIRE_CAUSES_WAKEUP
将屏幕点亮，当wake lock获取时。

通常情况下，wake locks并不是真的唤醒设备，而是让屏幕保持打开一旦它已经打开时。

#####ON_AFTER_RELEASE
当wake lock释放时，它会戳一下用户的activity的timer，这样名目就会多亮一会儿。

如果屏幕本身没有亮，那么次flag也不会让屏幕点亮。
---
layout: post
title: Android Testing  
excerpt: "Android 测试"
modified: 2016-03-29
tags: [android]
comments: true
image:
    feature: sample-image-5.jpg
---

####测试结构
Android测试是基于[JUnit](http://junit.org/junit4/)的。

根据当前的测试环境，我们可以用一下俩种方式运行我们的测试：

- 在本地机器上
	
	这个直接在本地机器上编译、运行（通过JUnit test runner）
- 在设备上或者模拟器中
    
    安装测试App到真机或者模拟器中，然后使用Android特有的工具运行测试。
    
| 类别 | 子类 | 描述 |
| ----| ----| ------|
| 单元测试| 本地单元测试|只能跑在本地机器上。这些测试被编译并运行在本地的JVM上，最小化了运行的时间。**如果要运行的单元测试不依赖Android框架或者有依赖但是mock对象都可以满足，可以使用这一类单元测试**|
|		|	基于Instrument的单元测试	|运行于Android真机或者模拟器上。这些单元测试能够获取到 **Instrumentation**的信息，比如测试App的Context。如果有对Android 框架的依赖，而且mock对象不能满足，需要使用这个测试|
|集成测试|App内部的|用于验证目标App是否像预想的那样表现，比如当用户执行了一个特定的操作或者在Activity中输入了一个特定的值。UI 测试框架比如[Espresso](http://developer.android.com/tools/testing-support-library/index.html#Espresso)可以让你通过编程来模拟用户的行为以及更复杂的app内的用户交互|
||跨App组件的|用于验证验证不同的用户app或者在用户app和系统app之间的交互。UI测试框架支持跨app的交互，比如UI Automator|

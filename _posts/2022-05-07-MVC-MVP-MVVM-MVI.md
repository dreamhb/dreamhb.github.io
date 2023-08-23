---
layout: post
title:  "MVC vs. MVP vs. MVVM vs. MVI"
date:   2022-05-07 17:50:00
comments: true
categories: Android
---


在图形化软件开发中，除了需要和用户交互，还需要对数据进行存储读取以及逻辑处理。根据单一职责原理，每一个单一职责，都应该要隔离开，一方面便于维护，另一方面也方便测试。

如果将它们都揉在一起，代码将非常的混乱，随着业务的增加，后期基本无法维护。所以在图形化软件开发中，会有一些设计模式出现，尝试着去解决这些问题。

下面分别介绍下他们.

### MVC （Model View Controller）

- Model 是该模式中的核心组件，独立于UI。直接管理数据，以及应用的逻辑和规则
- View 用于展示从Model获取的数据
- Controller用于接收用户输入，并传递给Model

#### 特点：
- 数据是循环流动的
- 各个模块互相依赖
- 在Android开发中，
	- Activity充当Controller的角色，如果业务复杂，会导致代码很多，对后期的维护造成影响
	- 同时相互依赖，耦合严重


### MVP （Model View Presenter）

- Model和MVC模式的一样，但是不会直接与View交互了，而是通过Presenter
- View 展示从Presenter拿到的数据，并且将用户的操作传给Presenter，并以接口的形式出现
- Presenter处于View和Model中间，将View和Model隔离开

#### 特点
- 优点在于，解决了Activity中的代码臃肿的问题，将这些逻辑转移到Presenter中，同时将View和Model彻底解耦，这样Model更容易复用。
- 缺点:
	- View抽象成接口后，容易造成接口的不断膨胀，维护成本较高
	- 同时Presenter需要拿到View的实例，需要注意生命周期的异常处理，容易出错
		- View销毁时，Presenter需要同步销毁，否则可能会内存泄漏
		- Presenter在向View传输数据时，需要判断View是否已经销毁


### MVVM （Model View ViewModel）

目的在于消除或者减少，逻辑代码对View的操作。类似于react通过改变State，来更新View。
- ViewModel 和 Presenter最大的不同在于，它不持有View的引用，而是通过Data Binding的方式，来实现交互的，实际上是一种声明式的，可以将View和ViewModel进一步的解藕。将数据的变化，直接体现在UI上。
- ViewModel作为Model中数据的状态展现，暂存数据状态，并处理逻辑(即有业务逻辑,也有UI逻辑)
- Binder将UI和数据绑定
- 在Android的使用中，我们会让ViewModel暂存View的数据，在设备旋转后直接从内存返回，提升体验。  

Android开发中，目前主要用这种设计模式。

### MVI(Model View Intent)

随着设计模式的演进，MVI出现了，它有更清晰的状态管理。如果用过React-Redux，其实就知道，他们是一样的思路。
用户的行为会产生一个Action，Action处理之后才会体现在State上，同时State是不可修改的。

### 总结

目前前端的设计模式趋势，是往声明式UI和状态管理方向演进的。(无论是MVVM、MVI、Jetpack Compose 还是React-Redux).  
将UI展示和数据处理完全分开，做到更方便的测试，更少的bug，当然，更复杂的代码。


### 参考
https://developer.android.com/topic/architecture  
https://en.wikipedia.org/wiki/Model–view–viewmodel

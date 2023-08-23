---
layout: post
title:  "Android-Jetpack-Navigation"
date:   2022-03-28 17:50:00
comments: true
categories: Android
---

### 单Activity的优势
Activity主要用于应用的入口.  

使用过React-Native的同学都知道,所有的内容都在一个Activity中,也就是单Activity.那么单Activity的优势在哪里呢?  

*   首先Activity的资源消耗比Fragment大,启动自然比Fragment要慢
*   其次Fragment更方便复用
*   Activity之间是完全独立的,无法通过使用ViewModel来共享数据
*   Activity间传输数据要通过IPC,比如startActivity,对于应用内来说,overkill了


### 导航的原则
*   固定的起始点  
    起始点是启动应用后的第一个页面,同时也是按返回键退出应用前的最后一个.
*   导航器的状态由目标组成的栈表示  
    应用启动后,第一个页面在入栈,随着继续跳转,将新的页面入栈,最新的页面总是在最上层,退出页面即将其退栈.  
*   应用内的返回键应当和系统的返回键表现一致
*   应用内返回键不应该退出应用  
    在开始页面,不应当显示应用内返回键.用户应当用返回键退出App,而不是应用内的返回键.
*   Deeplink模拟用户的手动导航  
    无论是deeplink还是手动的导航到某个特定的目标,用户都应该能够使用应用内的返回键最终回到起始点.

### Navigation Component
Android Jetpack的该组件帮助开发者实现导航,并且确保一个稳定的、可预测的用户体验,其遵循上面说的导航的原则. 

其由三个关键部分组成:
*   Navigation graph
    是一个XML资源,它集中包含了所有导航相关的信息.包括所有的App中的独立内容,即destinations,同时包含所有的actions.
*   NavHost  
    一个空的容器,用于显示navigation graph中的destinations. Navigation组件包含一个默认的NavHost实现,即NavHostFragment,用于显示fragment destinations.
*   NavController 
    是NavHost中用于管理app导航的对象.  

使用NavController可以沿着navigation graph中的路径,也可以去到某个特定的destination.  

它还有这些好处:
*   处理fragment事务
*   默认就能正确的处理应用内返回和系统的返回键
*   提供标准化的资源用于动画和转场
*   实现并处理deep link
*   包含Navigation UI模式,很容易的实现比如导航抽屉和底部导航. 
*   内置一个gradle插件Safe Args保证不同destinations间数据传输的类型安全
*   支持ViewModel,可以使用ViewModel在一组destinations之间共享UI相关的数据

#### 开始使用  
首先得创建一个navigation graph,然后添加一个NavHost到Activity中,并指定启始destination,然后就可以使用navigation graph中定义的action,进行导航了.  

我们同时可以通过Studio自带的Naviation Editor进行图形化操作.  

需要注意的是,destination既可以是fragment,也可以是activity,还有自定义的类.  

这些操作,做过iOS开发的肯定很熟悉.  

解析来就是跳转了,跳转我们需要NavController,我们可以通过如下几个方式拿到:
*   Fragment.findNavController
*   View.findNavController
*   Activity.findNavController(viewId: Int)

#### 如何设计navigation graph
*   顶层的navigation graph,应当从用户打开App的第一个界面开始,而且应当包含简单浏览的页面.
*   使用nested graph和"include"的方式,模块化navigation graph.
*   “include”的方式,同样也适合多module的情况,可以将库的graph包含进来  

#### Nested navigation graphs
其主要用来封装它的destinations,适合组织和复用App UI中的某些部分,比如内敛的登陆流程.  
和root graph配合使用时,必须有一个开始destination. 外部的destinations进入,必须通过开始destination.

#### Global actions
设想这样一个场景,一个应用有4个Tab,每个Tab对应一个navigation graph,无论在哪个navigation graph, 无论在哪个fragment,当检测到用户token失效,都需要跳转到登陆页.  

使用Global action很容易实现,只要为LoginFragment增加一个Global action,这样无论你在哪里,都可以跳转过去.  
如果给Fragment添加跳转到LoginFragment的,那么会到处都是.

#### 跳转
跳转是通过NavController来实现的,每一个NavHost都有一个对应的NavController.获取NavController有三种方式:
*   Fragment.findNavController
*   View.findNavController
*   Activity.findNavController(viewId: Int)

拿到NavController之后,就可以调用navigate来跳转了.
跳转也有多种方式:
*   使用action或者destination的ID
*   使用DeepLinkRequest
*   与back stack的交互
    *   navigateUp 对应的UP操作
    *   popBackStack 对应的BACK操作
*   popUpTo 和 popUpToInclusive
    *   适用于使用action时,需要额外pop一些destination,比如login相关的页面
    *   popUpToInclusive如果为true,则需要把popUpTo指向的destination也pop掉
    *   默认情况下,destination可能存在多个实例,使用这种方式可以解决这种问题
*   popUpToSaveState & restoreSaveState
    *   如果希望在destination被pop之后,保存其状态,可以使用这两个属性

### 总结
Navigation组件试图统一和规范化Android App的跳转和交互模式, 从而让App的体验更加一致.

### 参考
https://developer.android.com/guide/navigation
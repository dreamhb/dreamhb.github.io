---
layout: post
title:  "Kotlin Series - Coroutine 1"
date:   2022-02-19 17:50:00
comments: true
categories: kotlin
---

使用Kotlin已经有一段时间了，之前是断断续续的用一点，现在的主要语言已经切过来了。但是一直对Kotlin没有一个整体的把握，所以准备通过这一系列的文章来边学边总结。

第一部分主要写的是协程（Coroutine）的基础部分，第二部分准备写一写其相关的底层实现。

因为是基础部分,所以这一篇主要讲Coroutine的概念,其优势和适用场景,组成部分以及如何使用.

### 概念
Coroutine是一个非抢占式多任务的程序组件,支持暂停和恢复.
Coroutine是一个并发设计模式,可以简化异步操作.而Kotlin Coroutine则是Jetbrains对coroutine的一套实现,是一个标准库.

#### 特点
*   轻量级 
*   更少的内存泄漏
*   良好的“取消”支持
*   从Android角度来说,Jetpack库中很多都支持

因为可以暂停,我们可以在一个线程中,运行很多的Coroutine,同时也不会阻塞线程.暂停比阻塞节约内存,也没有线程的上下文切换的消耗.实测跑10万个Coroutine很轻松,如果跑这么的线程,基本就OOM了.

暂停通过suspending function来实现,Kotlin中提供了suspend关键字, 

```kotlin
suspend fun name() {
    //empty
}
```

Coroutine通过结构化并发(Structure Concurrency)来减少内存泄漏,它保证了所有的子任务结束后,父任务才退出.

Coroutine通过CancellationExeption来处理取消,通常Cancel会逐级上抛,直至root.

#### 和其他类似技术的对比
因为Coroutine是用来实现异步多任务的,在日常开发中,我们也会接触到许多类似的技术,比如常见的线程、回调还有JS中的promise、async和await,Java中的Future,以及现在用的很多的React.之前Kotlin的网站上有对比这些技术的说明,不知道为什么现在没有了.

其他技术的不足,主要包括资源占用,异常处理,学习难度等方面
*   成本高,占用过多系统资源-线程
*   回调地狱,会导致代码可读性降低,同时不易处理异常
*   其他的也都存在不同的代码结构,异常处理困难等问题

总而言之,Coroutine是“最好”的异步处理技术.

### 核心组成部分     
主要包括CoroutineScope和CoroutineContext,CoroutineContext又包括Job和CoroutineDispatcher.

#### CoroutineScope 
其为新的coroutine提供一个作用域,这是结构化并发的基础,将所有的任务都限定在一个Scope中.构建CoroutineScope有多种方式,包括
*   coroutine builder
    *   runBlocking
    *   launch
    *   async etc.
*   scoping function
    *   withContext
    *   coroutineScope
    *   supervisorScope
*   factory function
    *   CoroutineScope()
    *   MainScope()

先说一下 factory function,他们主要用来创建独立的scope,并且需要自己在任务结束时,将scope主动取消.
CoroutineScope和MainScope唯一的差别在于Dispatcher,这个后面会说到.  

一般最常用的就是coroutine builder,有的是suspending function,只能在scope中调用,而runBlocking比较特殊,不是suspending function,所以可以在主线程中调用,用于连接coroutine和非coroutine. launch是返回一个Job,而async则返回一个Defered对象,需要和await配对使用.另外他们都是CoroutineScope的extension方法.

withContext则用于给定/切换Context.   
coroutineScope继承了父scope的context,但覆写了父scope的Job,它主要的用处在于并行处理子任务.如果某个任务失败了,所有的子任务都会被取消,当前scope也会失败.  
supervisorScope并不会因为子任务的失败而取消,其他子任务也不会相互干扰.

#### CoroutineContext
像Android程序的Context一样,Coroutine也有一个Context,表示的是Coroutine运行的环境,用CoroutineContext来表示的,其主要元素是Job和CoroutineDispatcher.

##### Job
Job是Coroutine中执行的任务, 它是可取消的,并且有生命周期的.  Job 可以被用在parent-child层级中,父Scope取消,会导致所有的子Job立即全部取消,与之对应的,子Job有一个失败(不是CancellationException)也会导致父Scope取消,以及所有其他的子Coroutine取消.supervisorJob提供了不同的形式,适用于其他情形.

最常用的是使用**launch**来创建一个Job,其随代码执行完成而终止;另外可以用构造方法Job(),来创建CompletableJob,它需要通过调用**complete**手动完成.

Job有以下几种状态,包括:
*   New
*   Active
*   Completed
*   Cancelled
*   Completing
*   Cancelling  

Active 是默认的初始状态.

Job作为一个接口,自然有很多方法,所有的方法都是线程安全的,下面介绍一下常用的方法:
*   start()
*   cancel()
*   join() suspend the coroutine until the job is complete.
*   cancelAndJoin()

##### CoroutineDispatcher
决定Coroutine执行在某个或某些线程中,并可以将限定在某个特殊的线程或不限定.
CoroutineDispatcher有多种类型,包括
*   Default, 共享一个线程池,如果CoroutineScope创建时未指定,则默认使用该选项
*   newSingleThreadContext, 新建一个thread来执行coroutine,但是当不用时,需要调用**cancel**回收,或者作为全局变量复用
*   newFixedThreadContext, 新建一个线程池,用来执行coroutine
*   Unconfined, 在调用者线程中开始创建,知道下一个suspension 方法调用,便转移到该方法的Dispatcher中.

默认情况下,dispatcher继承自外层CoroutineScope,而runBlocking创建的coroutine,限定于调用者线程.

withContext可以用来在同一个coroutine中切换context.


默认情况下,当在一个CoroutineScope中创建一个新的CoroutineScope,其继承了外层的CoroutineContext,它的Job也成了外层CoroutineContext的子Job.如果外层CoroutineScope取消了,则所有的子Job都会取消.  

有两种方式可以改变这种行为:
*   GlobalScope.launch 直接在顶层Scope创建CoroutineScope,从而不继承任何外层CoroutineScope
*   创建Coroutine时,传入特定的Job,见如下代码示例

```kotlin
launch(Job()) {

}
```

### 其他重要问题点

#### 异常处理  
我们已经知道,Coroutine被取消时,会抛出CancellationException,但是它会被忽略,被当作是正常情况.

这里主要关注两个问题:
1.  正在取消的过程中发生的异常,如何处理
2.  同一个Coroutine中的多个任务抛出的异常,是怎么处理的  

对于Corooutine builder来说,异常的上抛有两种类型,一种是自动处理的,比如**launch**和**actor**,
另外一种需要调用者处理的,包括**async**和**producer**.  

CoroutineExceptionHandler用来在Root Coroutine中处理uncaught异常,并且子Coroutine会将异常逐级上抛,最终到达Root,所以子Coroutine中的ExceptionHandler默认情况下,是用不到的.  
而且我们不在CoroutineExceptionHandler中处理异常,仅仅用于输入日志,重启程序等,和Java中的Thread.uncaughtExceptionHandler很像.

如果子Coroutine通过调用cancel()来抛出CancellationException,父Coroutine是不会被取消的,如果是其他异常,则会导致父Coroutine被取消.  

如果是在Android开发中,我们并不希望子Coroutine失败导致父Coroutine被取消,那么我们就需要Supervision,即只有当父Coroutine取消时,所有的子Coroutine才会取消.它可以通过两种方式来实现:
*   SupervisorJob 它和普通Job只有一点差异,就是它的异常只向下传递
*   supervisorScope 和上述类似

他们和普通的Coroutine还有一点差异在于,他们的异常是不会上抛的.

#### 并发
如果Coroutine的Dispatcher是多线程的,那么其中的Job也会并发执行,从而也会有Java多线程并发的各种问题.  
这里先提一下并发和并行的区别(Concurrency vs. Parallelism):
*   并发(Concurrency) 至少两个任务在推进,但并不一定同时发生,例如通过时间分片在单核机器上执行多任务
*   并行(Parallelism) 至少两个线程同时执行,这个必须在多核机器上才可以

Coroutine有些解决方案和传统的Thread方案一样,有一些则是专属的.
下面用一段Kotlin官方文档中的代码说明一下这个问题
```kotlin
suspend fun massiveRun(action: suspend () -> Unit) {
    val n = 100  // number of coroutines to launch
    val k = 1000 // times an action is repeated by each coroutine
    val time = measureTimeMillis {
        coroutineScope { // scope for coroutines
            repeat(n) {
                launch {
                    repeat(k) { action() }
                }
            }
        }
    }
    println("Completed ${n * k} actions in $time ms")
}
```

```kotlin
var counter = 0

fun main() = runBlocking {
    withContext(Dispatchers.Default) {
        massiveRun {
            counter++
        }
    }
    println("Counter = $counter")
}
```
通过运行结果可以知道,结果不是预期的.那么怎么做能够让结果等于10000呢?

可以通过给counter加**volatile**关键字吗?   
这是一个典型的并发方面的错误,我们需要的是原子化操作(Atomic),但是volatile能做的非常有限,在原子化操作方面,仅适用于读和写变量,包括long和double的变量.  
如果没有该关键字,原子化操作仅包含读写引用变量和基础数据类型(不包括long和double).
**volatile**还有一个作用,就是减少了内存不一致的错误,保证被**volatile**修饰的变量,如果发生变化,其他线程也可见.

而自增并不是一个原子化操作,所以并不能达到要求.

可以达到的要求的方式如下:
*   线程安全数据结构 AtomicInteger
*   线程限定
    *   精细的  
        仅对自增操作,限定在单线程中,此方案效率较低,主要是因为对每一次自增都需要有线程切换
    *   粗糙的  
    将massiveRun整个方法限定在单线程中,比上述方法要快
*   互斥  
    在Java中,我们会使用synchronized或者ReentrantLock,在Coroutine中我们使用Mutex,可以lock和unlock.
*   Actor  
    是一个集Coroutine和Channel的实体,它比加锁更高效,因为没有线程切换.


### 总结
这篇文章,主要是对官方文档的一些总结,在后续的实际使用过程中,总结的经验等,会逐步更新上来.

### 引用
https://en.wikipedia.org/wiki/Coroutine  
https://developer.android.com/kotlin/coroutines  
https://kotlinlang.org/docs/coroutines-overview.html
https://docs.oracle.com/javase/tutorial/essential/concurrency/atomic.html
https://en.wikipedia.org/wiki/Linearizability

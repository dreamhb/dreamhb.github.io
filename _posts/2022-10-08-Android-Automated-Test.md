---
layout: post
title:  "Android Automated Test"
date:   2022-10-08 17:50:00
comments: true
categories: Android
---

# Android Automated Test

主要是希望通过这篇文章，来梳理一下对自动化测试的整体理解，有一个大概的印象。
这篇文章要回答的问题：
*   自动化测试是什么
*   为什么要有自动化测试
*   自动化测试怎么做


## 什么是自动化测试
**测试是App开发过程中的必不可少的环节**。通过持续的测试，可以在应用发布之前确保应用的正确性，行为是否符合预期以及可用性达到要求。  

当然我们可以手动测试，模拟用户对应用操作，试图发现可能存在的问题。
但是手动测试很容易忽视过去功能的测试，并且扩展性是比较差的， 只能通过增加人力的方式。比如我上家公司，因为是以手动测试为主，所以仅测试人员就有几十人，而App的质量并不高。而且经常因为代码改动，导致过去的功能出问题，而测试很难发现。

而自动化测试，是使用工具帮助我们测试，它更快也更容易重复，当然更重要的是，可以在开发过程中，及时给我们反馈。尤其是在我们重构代码时，能很容易的通过测试知道，我们是否改出了问题。


### 测试的分类
#### 按测试的对象分类
*   **功能测试** 测试应用行为是否符合预期
*   **性能测试** 测试应用运行的效率和速度
*   **可用性测试** 
*   **兼容性测试** 是否兼容各个设备和API级别

#### 按测试的范围分类
*   **单元测试** 主要用来测试方法或者一个类
*   端到端测试 测试一整个页面，或者用户操作流程
*   集成测试？ 测试多个模块或单元的集成情况

#### 按测试的运行环境分类（主要针对App）
*   **本地测试** 主要运行在开发用的电脑上或者服务器上，相对来说规模较小, 代码位于```src/java/test```。
*   **Instrumented 测试**  运行在Android设备上，手机或者模拟器上，主要是运行UI测试。代码位于```src/java/androidTest```。

测试有很多种分类的方法，对App来说，运行环境是主要的不同。所以单元测试也可以运行在设备上，而UI测试，也可以通过Robolectirc运行在开发机器上。

### 测试中的术语/概念
#### Test Doubles  
当我们对系统中的某个module进行测试的时候，它必然存在很多的依赖，比如网络请求、数据库操作等。这些依赖存在不稳定性以及可能比较耗时，所以为了提高测试的效率和速度，需要对这些依赖进行替换和模拟，这就需要 Test Doubles。其有以下几种类型：
*   **Fake**  是可以正常工作的依赖，只是不适合生产环境。有点在于不需要Mocking框架的支持
*   **Mock** 用于模拟我们期待的结果
*   **Stub** 和它的交互不会符合预期，通常是mocking framework创建的
*   **Dummy** 主要用于传入的参数
*   **Spy** 将真实的对象封装起来，通常因为复杂而不用
*   **Shadow** Robolectirc中使用的Fake

从上面可以看出，通常我们使用**Fake** **Mock**以及**Dummy**。

#### 常用测试框架
*   **JUnit** Java平台的单元测试框架
    *   JUnitRunner
    *   Rules
*   **Mockito** Java平台使用的mocking框架
    *   APIs
        *   mock
        *   when
        *   thenReturn
    ```kotlin
    val context = Mockito.mock(Context::java.class)

    Mockito.mock(Context.class).when(context.resources).thenReturn(mockResources)
    ```

*   **Espresso** Android平台的UI测试框架
    *   APIs（完整的API列表,点[这里](https://developer.android.com/training/testing/espresso/cheat-sheet)）
        *   Core APIs
            *   onView  普通的view测试
            *   onData 适用于adapter的view测试
            *   intended 测试intent
            *   intending 测试intent
        *   View Matchers
            *   withId  使用view的id匹配view
            *   withText 使用view上的文字匹配view
            *   isDisplayed
            *   isRoot
            *   isDialog
            *   allOf/anyOf
            *   ...
        *   View Actions
            *   CLICK/PRESS
                *   click
                *   openLink
                *   ...
            *   GESTURES
                *   scrollTo
                *   ...
            *   TEXT
                *   clearText
                *   typeText(String)
                *   ...
        *   View Assertions
            *   matches
            *   ...
        *   Intent Matchers  
            *   hasAction
            *   hasKey
            *   ...

    ```java
    onView(withId(R.id.view))
        .perform(click())
        .check(matches(isDisplayed()))
    ```
#### 其他
*   测试类型
    *   UAT(User Acceptance Test)
    *   SIT(System Integration Test)
    *   Regression
*   代码覆盖率（Coverage）

## 如何做自动化测试  
有了上面的一些基本概念，我们就可以开始做自动化测试了。
#### 要测试哪些部分  
主要测试我们自己的代码，不需要测试Android Framework的API，比如Activity和Fragment的生命周期。
#### 易于测试的应用架构  
一个测试友好型的App架构，对于测试的效率来说必不可少。这里推荐两个主要的架构，一个是 Android官方的MVVM架构，另一个是Clean架构。下面的内容基于MVVM的架构来说，如果对Clean感兴趣，可以自行了解。

MVVM架构层次分明，基于数据驱动的原则，将整个App分为Data层，Domain层和UI层。因为Domain层是可选的，所以我们主要介绍Data层和UI层。

*   **Data层** 这一层主要处理App的数据以及核心的业务逻辑，这一部分的测试主要是local test。
*   **UI层** 主要负责展示数据，并和用户交互，所以这一部分的测试主要是UI测试，属于instrumented test。



#### 依赖注入（DI）
因为测试中，不可避免的要进行依赖替换，所以使用依赖注入这种方式来管理依赖，可以非常方便的让我们在测试时，使用Fake或者Mock进行替换，降低自动化测试的难度，提高效率。

#### 示例代码

```java
@RunWith(AndroidJunit4.class)
public class TestSuite {

    @Rule
    SomeRule rule;

    @Mock
    SomeMock mock;

    @Before
    public void setup() {
        //这里做一些初始化的工作，比如Activity初始化
    }

    @After
    public void cleanup() {
        //清理工作，比如Activity finish
    }


    @Test
    public void testLogin() {
        //real testing code here
    }
}
```

#### 如何运行测试
*   对于local test，使用```./gradlew test```
*   而instrumented test, 使用 ```./gradlew connectAndroidTest```


### 测试案例（MVVM）
todo

## 总结  
测试作为开发过程中的一部分，是我们输出高质量高可靠性产品的基础环节之一。所以做好测试是至关重要的。  

测试可以让我们提前发现问题，避免问题出现在用户端。

## 参考

---
layout: post
title:  Effective Java (六)
excerpt: "对象的常用方法 "
modified: 2015-12-14
tags: [java, reading note]
comments: true
image:
feature: sample-image-5.jpg
---
####Item7 复写equals需要遵循的通用规则####

1. 每一个类的实例都是唯一的
2. 你并不关心类是否提供逻辑相等测试
3. 父类已经复写了equals，而且从父类继承而来的行为对子类也是试用的
4. 类是私有或者包私有，你确定它的equals方法永远不会调用

何时应该复写Object的equals方法呢？

**当一个类有逻辑相等的概念而且它不同于仅仅的对象相同，同时父类也没有复写equals方法来实现这个功能**

在value 相关的类中则很常见，比如Integer或者Date.

typesafe enum是不需要覆写equals方法的，因为一个值最多只有一个对象。

当你要覆写equals方法时，必须符合通常的规范和条约，一下就是从JSE拷贝来的：

equals方法实现了**equivalence relation**:

1 反射的：对任意的引用x， x.equals(x) 必须返回true

2 对称的： x.equals(y) 应该和 y.equals(x) 相同

3 可传递的

4 稳定的

5 对任何非空的引用x， x.equals(null) 必须返回false



**未完待续**








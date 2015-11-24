---
layout: post
title: Effective Java Two
excerpt: "创建和销毁对象  使用私有构造函数实现单例特性"
modified: 2015-11-24
tags: [java, reading note]
comments: true
image:
    feature: sample-image-5.jpg
---

单例就是一个类只会被实例化一次，仅且一次。单例的典型用法是，代表了一些系统组件，他们在本质上是唯一的，比如视屏显示或者文件系统。

单例有俩种实现方式，都是基于私有化构造方法，并提供一个静态的公有成员，来让客户端获取到类的唯一的一个实例。在其中一种方式中，公有静态成员是一个final成员变量：

```
public class Elvis{
	public static final Elvis INSTANCE = new Elvis();
	private Elvis(){
	}
}
```
私有构造函数只调用一次，来初始化公有的静态成员变量。没有公有的或者受保护的构造函数确保了一个只有一个Elvis实例存在一旦Elvis类初始化-不多不少。客户端对此无能为力。

在第二种实现方式中，公用的静态方法替换了之前的公有的静态的不可改变的成员变量：

```
public class Elvis{
	private static final Elvis INSTANCE = new Elvis();
	private Elvis(){}
	public static Elvis getInstance(){
		return INSTANCE;
	}
}
```
所有的调用Elvis.getInstance的都返回了相同的一个对象引用，不会再创建新的Elvis实例了。

第一种实现方式的主要优点在于组成类的成员变量的声明，让这个类是一个单例这样的情况显而易见：公有静态域是final的，也就是不可修改的，因此它总是包含了同一个对象引用。第一种实现方式也有一些轻微的性能优势，但是一个很好实现的JVM能通过将第二种实现方式的静态工厂方法inline来抵消。

第二种实现方式的主要优势在于提供了便捷性，让你可以改变使用单例的主意而不需要改变API，静态工厂方法返回返回类的唯一实例，但是可以很容易的修改成不同的进程返回不同的唯一实例。

总的来说，如果你绝对的确定这个类将永远的保持单例，那么就是使用第一种实现方式，使用第二种方式，如果你想保留这种修改的可能。

让一个单例类可以实例化，仅仅实现 Serializable接口是不够的。为了保证单例，你必须提供readResolve方法。否则任何一个序列化的实例在反序列化的时候都会新创建一个实例，在这个例子中将导致虚假的单例。为了防止这种情况的发生，添加如下readResolve到Elvis方法中：

```
private Object readResolve() throws ObjectStreamException{
	return INSTANCE;
}
```

一个统一的主题成为这个例子和例子21的基础，它描述了一个**type-safe enum**模式。在俩种情形中私有的构造方法确保了没有相关类的新实例被创建，在初始化之后。在这个例子中，这个类只有一个实例被创建了。在下一个例子中，更进一步：公有构造函数的缺席被使用来确保类没有实例被创建。

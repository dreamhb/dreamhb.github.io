---
layout: post
title:  Effective Java (三)
excerpt: "避免创建重复的对象,如果可以复用，那就不要新创建，如果创建一些小的和简单的对象却是可以的"
modified: 2015-11-29
tags: [java, reading note]
comments: true
image:
feature: sample-image-5.jpg
---

复用一个简单地对象而不是在需要的时候总是创建一个新的，但是功能上完全相同的对象。重用即快速也很时髦，如果一个类是不可改变的，那么他总是可以被重用。下面是一个绝对不要尝试的例子：

<code>
String s = new String("silly"); //DONOT DO THIS!
</code>

上面的表达式每次调用的时候都创建一个新的String实例，但是这些对象的创建都是不必须的。String构造函数的参数本身就是一个String实例，在功能上与构造函数创建出来的实例完全一样。如果这个发生在循环中，或者频繁地被调用，那么成千上万的不必须的对象将被创建。（将造成内存泄露）
可以简单地修改如下：

<code>
String s = "No Longer silly";
</code>

这个版本使用了一个简单地String实例，而不是每次执行的时候都创建一个新的。更进一步，对包含同一个字符常量的JVM来说，所有的方法都会使用这个唯一的实例。

你也可以通过静态工厂方法来避免创建重复的对象。举例来说，Boolean.valueOf(String)总是在构造函数Boolean(String)中更好地一种方式。构造函数总是要创建一个新的对象，但是工厂方法却不需如此。

另外，除了可以重用不可变的对象，你也可以重用可变的对象，如果你知道它不会被修改。这里有个比较精妙的但是更常见的我们应当避免的例子，包括那些可变的对象，一旦他们的值被计算出来之后就不再改变的。

<code>
public class Person{
	private final Date birthDate;
	public Person(Date birthDate){
		this.birthDate = birthDate;
	}
	//DONOT DO THIS!
	public boolean isBabyBoomer(){
		Calender gmtCal = 
		Calendar.getInstance(TimeZone.getTimeZone("GMT"));
		gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
		Date boomStart = gmtCal.getTime();
		gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
		Date boomEnd = gmtCal.getTime();
		return birthDate.compareTo(boomStart) >= 0 &&
			birthDate.compareTo(boomEnd) < 0;	
	}
</code>
此方法在每一次调用时多余的创建了新的Calendar，TimeZone，和俩个Date实例。下面的例子用静态初始化器来提高效率：

<code>
class Person{
	private final Date birthDate;
	public Person(Date birthDate){
		this.birthDate = birthDate;
	}
	
	private static final Date = BOOM_START;
	private static final Date = BOOM_END;
	
	static{
		Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
		gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
		BOOM_START =  getCal.getTime();
		gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
		BOOM_END = getCal.getTime();
	}
	
	public boolean isBabyBoomer(){
		return birthDate.compareTo(BOOM_START) >= 0 &&
			birthDate.compareTo(BOOM_END) < 0;
	}
}
</code>

这个修改过的版本，在初始化的时候只创建了一次Calendar, TimeZone,和Date实例，而不是每一次调用方法的时候都创建他们。这个将导致很明显的性能提示当你调用的很频繁地时候。在我的机器上，原始版本调用一百万次需要36000秒，而改进过的版本只要370秒，这个是一百倍的速度喔。不仅仅是性能的提升，而且代码也很简介。
将boomStart和boomEnd从本地变量改成final static变量，使代码更清晰，可以将他们当做常量，这样的代码更容易理解。为了进行充分地披露，这种形式的优化并不会总是这么明显，这里主要是因为Calendar的实例的创建成本是很高的。

如果isBabyBoomer方法永远都没有被调用，那么升级版的Person类对BOOM_START和BOOM_END的初始化就是不必须的。可以通过延迟初始化（Lazily initializing)这些域到首次调用此方法的时候，这样可以消除不必要的初始化，但是不推荐这样做。就像延迟初始化的问题那样，这样导致实现的复杂化，而且也不一定有多少性能上的提升。

在此例之前的例子中，对象能够重用是因为他们在初始化后没有被修改，这是显而易见的。
考虑Adapter的例子，也就是views。一个adapter就是一个对象，它委托了其本后的对象，为其背后的对象提供可供选择的接口。因为adapter离开了其背后的对象，那么它就没有了状态，没有必要为一个对象创建多余一个的adapter。

举个例子来说，Map接口的keyset方法返回Map对象的Set，是由map中的所有key组成的。天真的假想一下，看起来每一次调用keyset方法，都会创建一个Set实例，但是一个给定的Map对象的keySet方法返回的是同一个Set实例。尽管返回的Set实例是可以修改的，但返回的所有对象却都是相同的：当其中一个改变时，所有其他的都跟着改变了，因为他们都基于同一个Map实例。

这个例子不要误解为对象的创建是代价高的和应该避免的。相反，创建和回收一些构造函数只做一点显示的工作是廉价的，特别是在现代的JVM上。创建额外的对象来加强简洁、简单、或者程序的功能，通常情况下是个好事情。

相反的，使用对象池来避免对象的创建是一个坏主意，除非对象池中的对象是极度的重量级。一个满足对象池的原型例子就是数据库连接，创建一个连接非常的昂贵，而且复用这些对象是有意义的。一般来说，维护一个自己的对象池会混乱你的代码、增加内存消耗和损害性能。现代的JVM实现拥有高度优化的GC，它很容易的就超过轻量级对象的对象池。

和此例对应的是Item 24 **Defensive Copying**，当前的例子说：“如果你应当复用已存在的对象那么就不要创建新对象”。注意一下，当防御拷贝调用时，复用一个对象的代价就远大于创建一个重复的对象了。创建防御拷贝失败会导致潜在的bug和安全漏洞；创建不必须的对象仅仅影响样式和性能。

***未完待续***



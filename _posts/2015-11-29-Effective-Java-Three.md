---
layout: post
title:  Effective Java (三)
excerpt: "避免创建重复的对象"
modified: 2015-11-29
tags: [java, reading note]
comments: true
image:
feature: sample-image-5.jpg
---

复用一个简单地对象而不是在需要的时候总是创建一个新的，但是功能上完全相同的对象。重用即快速也很时髦，如果一个类是不可改变的，那么他总是可以被重用。下面是一个绝对不要尝试的例子：

```
String s = new String("silly"); //DONOT DO THIS!
```
上面的表达式每次调用的时候都创建一个新的String实例，但是这些对象的创建都是不必须的。String构造函数的参数本身就是一个String实例，在功能上与构造函数创建出来的实例完全一样。如果这个发生在循环中，或者频繁地被调用，那么成千上万的不必须的对象将被创建。（将造成内存泄露）
可以简单地修改如下：

```
String s = "No Longer silly";
```
这个版本使用了一个简单地String实例，而不是每次执行的时候都创建一个新的。更进一步，对包含同一个字符常量的JVM来说，所有的方法都会使用这个唯一的实例。

你也可以通过静态工厂方法来避免创建重复的对象。举例来说，Boolean.valueOf(String)总是在构造函数Boolean(String)中更好地一种方式。构造函数总是要创建一个新的对象，但是工厂方法却不需如此。

另外，除了可以重用不可变的对象，你也可以重用可变的对象，如果你知道它不会被修改。这里有个比较精妙的但是更常见的我们应当避免的例子，包括那些可变的对象，一旦他们的值被计算出来之后就不再改变的。

```
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
```
此方法在每一次调用时多余的创建了新的Calendar，TimeZone，和俩个Date实例。下面的例子用静态初始化器来提高效率：

```
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
```

这个修改过的版本，在初始化的时候只创建了一次Calendar, TimeZone,和Date实例，而不是每一次调用方法的时候都创建他们。这个将导致很明显的性能提示当你调用的很频繁地时候。在我的机器上，原始版本调用一百万次需要36000秒，而改进过的版本只要370秒，这个是一百倍的速度喔。不仅仅是性能的提升，而且代码也很简介。
将boomStart和boomEnd从本地变量改成final static变量，使代码更清晰，可以将他们当做常量，这样的代码更容易理解。为了进行充分地披露，这种形式的优化并不会总是这么明显，这里主要是因为Calendar的实例的创建成本是很高的。

***未完待续***



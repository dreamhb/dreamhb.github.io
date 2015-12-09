---
layout: post
title:  Effective Java (四)
excerpt: "消除过期的不再使用的对象引用"
modified: 2015-12-02
tags: [java, reading note]
comments: true
image:
feature: sample-image-3.jpg
---

当你从一种手动内存管理的语言，比如C或者C++切换到有垃圾收集的语言，你作为一个程序员的工作就变得轻松多了，这是因为你的对象不用时会被自动回收。当你一开始体验时，就像个魔术一样。它很容易的导致一种印象，那就是你不用去想内存的管理，但是这并不完全正确。
考虑下面这个例子：

<pre>
<code>
//Can you spot the "memory leak"
public class Stack{
    private Object[] elements;
    private int size = 0;

    public Stack(int size){
	elements = new Object[size];
    }

    public void push(Object e){
	ensureCapacity();
	elements[size++] = e;
    }

    public Object pop(){
	if(size == 0){
	    throw new EmptyStackException();
	}
	return elements[--size];
    }

    private void ensureCapacity(){
	if(elements.length == size){
	    Object[] oldElements = elements;
	    elements = new Object[2 * elements.length + 1];
	    System.arrayCopy(oldElements, 0, elements, 0, size);
	}
    }
}
</code>
</pre>

这个程序没有明显的错误。你可以全面的测试它，而且会出色完成每次测试，但这里有个潜伏的错误。笼统的讲，这个程序有**内存溢出**，这个静静的显现出来随着性能的降低，原因是不断增加的垃圾回收活动或者内存占用。在极端情况下，这样的内存溢出会导致磁盘分页甚至是程序因为OOM而失败，但是这样的错误相对少见。

那么到底哪里存在内存溢出呢？如果stack增加容量然后缩小，被推出的对象不会被垃圾回收，就算是使用stack的程序没有指向他们的引用。这是因为stack保持了不用的引用，他们指向了这些对象。过期的对象简单来说就是永远不会被反引用的引用。在本例中，任何一个不在array活动部分内的都是过期对象。活动部分就是那些下标小于size的部分。

内存溢出在支持垃圾回收的语言中是很隐蔽的（以非有意的对象保留而著称）。如果一个对象无意识的被保留，不仅仅是那个对象被排除在GC之外，被这个对象引用的也会被保留，等等。即使只有很少的一部分对象被无意的保留，但是很多对象可能因此导致不能被垃圾回收，将导致潜在的巨大性能问题。

解决这个问题的方法很简单：将过期的对象赋值为NULL。在我们这个Stack例子中，其中元素的引用在一出栈就变成过期的了，正确的出栈版本是这个样子的：

<pre>
<code>
public Object pop(){
	if(size == 0){
		throw new EmptyStackException();
	}
	Object result = elements[--size];
	elements[size] = null;
	return result;
}
</code>
</pre>
将弃用的引用置为NULL的额外的好处是,如果他们随后被错误的去引用，程序会立马因为空指针异常而终止，，而不是安静的做着错得事情。尽快的发现程序的错误总是有利的。

当程序员被这样的问题蜇了之后，他们倾向于过度补偿，通过在程序结束时，将所有的引用都置空。这既不是必须得也不是合适的，因为它会让程序变得凌乱而且会降低性能。将引用置空应当是个例外而不是常态。最好的消除过期引用的方法是重用被包含的变量，或者是让它挣脱当前的域。如果你在尽可能窄的域来定义变量这些就很自然的发生了。需要注意的是，在现在的JVM中，仅仅退出变量定义的代码块是不够的，必须退出包含的方法。

那么何时应当将引用置为NULL呢？Stack类的哪个因素让它这么容易内存溢出呢？简单来说，就是Stack类是自己管理内存的。***存储池***由元素数组中得元素组成（对象引用单元，并不是对象本身）。在数组中的对象活跃部分是被分配的，那些剩下的则是没有使用的。GC没有办法知道这个情况，对GC来说，所有在数组中得元素都是同样的合法有效。只有程序员才知道数组的不用部分是不重要的。程序员通过将不用的元素迅速的置NULL来与GC高效的沟通。

一般来说，无论何时一个类自己管理内存的时候，那么就要当心内存溢出。任何时候，当一个元素不再使用，这个元素中得所有对象引用都要置空。

另一个经常出现内存溢出的是缓存。一旦你将一个对象引用放到缓存中，很容易将忘记了它的存在，直到它变得不相关之后很久。对于这种问题，有俩种办法：如果你足够幸运去实现一个缓存，在那里一个记录是确切的相关只要有指向key的引用超出了缓存，使用**WeakHashMap**来代表这个缓存；记录会在没有作用后被自动的移除。更常见的是，一个缓存记录的相关时间并没有确切的规定，记录随着时间推移而变得不再重要。在这些情况下，缓存应当偶尔清理那些废弃的记录。这种清理工作可以让后台进程来做，或者在添加新的记录的时候来做，**Java.util.LinkedHashMap** Java 1.4中添加的类，通过它的removeEldestEntry方法让后一种方法便利的实现了。

因为内存溢出不像错误那样的明显，他们有可能在系统中存在很多年。典型的情况，他们被发现是因为一个仔细的代码审查或者是调试工具的帮助比如**heap profiler**.然而学习避免这样的问题和阻止它发生是非常值得的。


---
layout: post
title:  "Generics: Java and Kotlin"
date:   2019-12-05 09:50:00
comments: true
---

Generis were added to Java in release 1.5.

Before generics, we have to cast object from collections but may cause cast exception if the object is the wrong type.

We discuss Arrays first.

Arrays are covariant and reified:
*   **covariant** means if type A is subtype of type B, A[] is the subtype of B[]. 
*   **Reified** type is a runtime representation of its component type which means arrays know and enforece their elements at runtime.

```java
Integer[] ints = new Integer[] {1,2,3};
Number[] nums = ints;
nums[2] = 3.14 // array store exception at runtime occurs since array know nums are int array while 3.14 is double, they are not the same type
```

Because of those two properties of Arrays, the above code compiled but causes exception at runtime.


By contrast, generics are different in those two ways:
*   generics are **invariant**, for any two distinct types T1 and T2, whatever their relations, List\<T1\> is neither a subtype nor a supertype of List\<T2\>.
*   generics are implemented by **erasure**, it means List\<T1\> and List\<T2> are different types only at compile time, there type infomations will be erased at runtime, then they are equal at runtime.


By introducint generics, the code are becoming safer and cleaner but come with complications.


This article will dicsuss how to make the maximize the benifits and minimize the complications and how Kotlin make it simpler further.


Let's consider <code>addAll()</code> for the Collection interface.

```java
public interface Collection<E> {
    void addAll(Collection<E> c)
}
```

It works but not efficient, since the following code is pretty safe but can't be compiled.
```java
void copyAll(Collection<Object> to, Collection<String> from) {
    to.addAll(from)
}
```

That is why the actual one is the following:
```java
public interface Collection<E> {
    void addAll(Collection<? extends E> c)
}
```

<code>? extends E</code> is bounded wildcard type which means the parameter can now accept **E** and all its subtypes. It is actually make the **invariant** parameter **covariant**, <code>Collection<? extends E></code> now is the subtype of <code>Collection&lt;E&gt;</code>.  For now, we can safely **read** <code>E</code> from it, but can't write to since we don't know the exact type **E** is.  

Another bounded wildcard type is <code>? super E</code>, it means we could only **write** to it. For example we can take Collection of Object and put Collection of String to it.

<code>Collection<? super E></code> is the subtype of <code>Collection&lt;E&gt;</code> while <code>? super E</code> is the supertype of E. This is called **Contravariant**.

As Joshua Bloch puts it in Effective Java, we could use **PECS** for maximum flexibility, it stands for **Producer Extends, Consumer Super**.

The copy method in the Collections is a good example:
```java
//Collections.java
public static <T> void copy(List<? super T> dst, List<? extends T> src) {
    for (int i = 0; i < src.size(); i++) {
        dst.set(i, src.get(i));
    }
}

//The Kotlin equivalence will be provided later
```

It is a little hard to understand and to use them correctly. In Kotlin, we have **in** and **out** instead.

### Kotlin's declaration-site variance
Suppose we have a generic interface Source&lt;T&gt; that doesn't have any methods that take T as a parameter but only methods return T. It means the interface only produces T, will not consume T.
```java
interface Source<T> {
    T next();
}
```

So it would be safe to store a reference to an instance of Source&lt;String&gt; in a variable of Source&lt;&gt;. But Java know nothing about this and because Java generics are invariant, it is not allowed.
```java
void demo(Source<String> strs) {
    Source<Object> objs = strs; //Not allowd here because of Invariant
}
```

To make it work, we should declare objs like this:
```java
Source<? extends Object> objs = strs
```
The above declaration is meaningless. In Kotlin we could use **out** variance annotation to tell the compiler.
```kotlin
interface Source<out T> {
    fun next(): T
}

fun demo(strs: Source<String>) {
    val objs: Source<Any> = strs
}
```

The **out** modifier makes T a covariant parameter and it is provided at the type parameter declaration site, it provides **declaration-site variance**. This is in contrast with Java's **use-site variance**.

There is another variance annotation **in**, which makes a type parameter **contravariant**, which means it can only be consumed and never produced. Let's take <code>Comparable</code> as an example:
```kotlin
interface Comparable<in T> {
    operator fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0)

    val y : Comparable<Double> = x // contravariant 
}
```


### Use-site variance: type projections
Let's consider the following example:
```kotlin
class Array<T>(val size: Int) {
    operator fun get(index: Int): T
    operator fun set(index: Int, value: T)
}
```
This class can be neither covariant nor contravariant in T since it takes T as paramter and return T as well. That is to say the T can be both consumed and produced.  
Consider we have a <code>copy</code> function:
```kotlin
fun copy(from: Array<Any>, to: Array<Any>) {
    for(i in from.indices) {
        to[i] = from[i]
    }
}
```

But when use it, the following code will not be compiled.
```kotlin
val ds: Array<Double> = arrayOf(1.0, 2.0, 3.0)
val any = Array<Any>(3){ "" }
copy(from = ds, to = any) //Not OK, because Array<Double> and Array<Any> have no relations due to Invariant
```

Because Java Generics are introduced to prevent unexpected behavior like writing a <code>String</code> to <code>from</code> in the copy function, it may cause a <code>ClassCastException</code>.

In Kotlin, to prohibit the copy function from writing to <code>from</code>, we could use **out** like the following:
```kotlin
fun copy(from: Array<out Any>, to: Array<Any>)
```

Using **out** is Kotlin's **type projection** which means the array is projected, we can only call methods return the type parameter <code>T</code>. And we can use **in** to make a type only can be consumed as well.
```kotlin
fun save(to: Array<in String>, value: String)
```
This is equal to Java's <code>? super String</code> which means we can pass <code>Array&lt;CharSequence&gt;</code> or <code>Array&lt;Any&gt; to the function.


### Type erasure
Same as Java, Kotlin performs generic declaration checking at compile time. At runtime the all type information of generic types are erased.

So to check whether an instance of a generic type with certain type arguments at runtime is prohibited by compiler, and it is true for generic functions.
But there is an exception, when using **reified type parameters** in an <code>inline function</code>, T could be used for type checks. This is because their actual types are inlined at each call site.

### References
*   [https://kotlinlang.org/docs/generics.html](https://kotlinlang.org/docs/generics.html)
*   [https://stackoverflow.com/questions/45949584/how-does-the-reified-keyword-in-kotlin-work](https://stackoverflow.com/questions/45949584/how-does-the-reified-keyword-in-kotlin-work)
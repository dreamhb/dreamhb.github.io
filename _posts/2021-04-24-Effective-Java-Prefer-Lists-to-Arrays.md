---
layout: post
title: Effective Java（Item 26）Prefer List to Arrays
excerpt: "Array is covariant and reified, but list is invariant and erasure"
modified: 2021-04-24
tags: [java]
comments: true
image:
    feature: sample-image-5.jpg
---

**Generics** **Covariant** **Invariant** **Reification** **Erasure** **Contravariant**

# Prefer List to Arrays

这里总结以下Java范型的一些重要概念，并尝试通过分析语言设计者的意图，加深对Java语言类型系统的理解，并对比下Kotlin。

Java 从1.5版本开始引入范型的概念。在引入范型之前，从集合中读出的对象，都要手动cast，如果类型不一致，就会导致运行时的异常。而通过范型，我们可以告诉编译器集合中允许的类型。编译器自动插入cast，并在编译时报错，如果你插入了错误的类型。有范型加持的程序，**更安全更简洁**，当然也更复杂。

## Covariant vs Invariant vs Contravariant
### 概念

```java
public static <T> void copy(List<? super T> dest, List<? extends T> src) {
      for (int i = 0; i < src.size(); i++) {
          dst.set(i, src.get(i));  
      }
}
```
#### Covariant  
type S[] is a subtype of T[] whenever S is a subtype of T
#### Invariant  
type List<"S"> is not a subtype of List<"T"> when S is a subtype of T
#### Contravariant  
type List<"S"> is a subtype of List<? super T> when S is a supertype of T
#### Wildcards
* The Get and Put Principle  
use **extend** wildcard when you only get values out of a structure, use a **super** wildcard when you only put values into a structure, and don't use a wildcard when you both get and put.
*   wildcard capture  
    When a generic method is invoked, the type parameter may be chosen to match the unknown type represented by a wildcard.
    ```java
    public static void reverse(List<?> list) {  List<Object> tmp = new ArrayList<Object>(list);  for (int i = 0; i < list.size(); i++) {    
        list.set(i, tmp.get(list.size()-i-1));  //compile-time error  
        }}
    ```
*   in & out in Kotlin
    *   in for contravariant
    ```kotlin
    interface Comparable<in T> {
        operator fun compareTo(other: T): Int
    }

    fun demo(x: Comparable<Number>) {
        x.compareTo(1.0) // 1.0 has type Double, which is a subtype of Number
        // Thus, you can assign x to a variable of type Comparable<Double>
        val y: Comparable<Double> = x // OK!
    }
    ```
    *   out for covariant
    ```kotlin
    interface Source<out T> {
        fun nextT(): T
    }

    fun demo(strs: Source<String>) {
        val objects: Source<Any> = strs // This is OK, since T is an out-parameter
        // ...
    }
    ```
## Reification vs Erasure
Java中， 数组reify内容的信息，而范型不会reify他们的类型参数。
我们说一个类型reifiable，如果它在运行时和编译时完全一样，也就是**erasure**没有去除任何有用的信息。

*   下面的类型都reifiable
    *   基础类型 （int）
    *   非参数化的类或者接口 （Number， String， Runnable）
    *   参数类型，但所有的类型参数是无边界匹配 （List<?>）
    *   raw 类型 （List， ArrayList， Map）
    *   内容类型都是reifiable的数组（int[], Number[], List<?>[], List[], int[][]）

*   以下类型都是不能reifiable的
    *   类型变量 （T）
    *   有具体参数的参数化类型 (List<'String'>)

## Arrays vs Generics
数组和范型是非常不同的类型规则。数组是covariant和reified；范型是invariant和erased。

---
layout: default
title:  "Generics: Invariant vs Covariant vs Contravariant"
date:   2019-12-05 09:50:00
comments: true
---

Java generics is implemented by **erasure**. So at runtime, List<String> and List<Integer> are the same.
## Invariant  

Generics are invariant and erasure.

### wildcards  
Sometimes we would like lists to behave more like arrays, in that we want to accept not only a list with elements of a given type, but also a list with elements of any subtype of a given type. For this purpose, we use wildcards.  


### wildcards  
Sometimes we would like lists to behave more like arrays, in that we want to accept not only a list with elements of a given type, but also a list with elements of any subtype of a given type. For this purpose, we use wildcards.  
1.  Wildcards with extends: <? extends E>  
    In general, if a structure contains elements with a type of the form ? extends E, we can get elements out of the structure, but we cannot put lements into it. Because we donot know what the specific type is.

2.  Wildcards with super: <? super E>    
```
public static <T> void copy(List<? super T> dst, List<? extends T> src) {
    for (int i = 0; i < src.size(); i++) {
        dst.set(i, src.get(i));
    }
}
```

### The Get and Put Principle  
use an **extends** wildcard when you only **get** values out of a structure, use a **super** wildcard when you only **put** values into a structure, and don't use a wildcard when you both get and put.


## Covariant  

Arrays are covariant and reified, covariant means if type A is subtype of type B, A[] is the subtype of B[]. Reified type is a runtime representation of its component type.  
```java
Integer[] ints = new Integer[] {1,2,3};
Number[] nums = ints;
nums[2] = 3.14 // array store exception at runtime
```

## Contravariant  
List<S> is considered to be subtype of List<? super T> when S is a supertype of T.

## What is the difference  
Generics can detect problems at compile time rather than at run time. It brings two advantages, one is that it is more efficient. The system does not need to carry around a description of the element type at run time, and the system does need to check against this description every time an assignment into an array is performed. The other one is that a common family of errors is detected by the compiler.



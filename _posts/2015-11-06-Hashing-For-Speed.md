---
layout: post
title: Hashing For Speed
excerpt: "为什么HashMap会快呢？"
modified: 2015-11-06
tags: [intro, java, reading-note]
comments: true
image:
    feature: sample-image-2.jpg
    credit: true
---
# Hash Map

普通map的瓶颈在于查找，因为是使用线性查找的，这种方式是最慢的查找方式。
  
哈希的全部就在于速度：哈希能让检索很快的发生。因为检索key的瓶颈在于key查找，一个解决办法是，首先让key排序，然后使用Collections.binarySearch() 来执行查找。
  
哈希则更进一步，它使用一种能够让key能够被快速发现的方法，存储key。
最快的结构是将一组数据，存在在数组中，以便于可以用来代表key信息（这里的key不是指key自己）。但是数组的大小是固定的，不能够动态增加，我们有个问题：我们需要存储不确定数目的数据到MAP中，但是如果数据的大小因为array的固定大小而固定的话，应该怎么做呢？

答案是array不会直接存储key。一个数字会从key对象产生并且加入到数组中，这个数字就是 **hash code**，这个方法定义在Object对象中，也可能被你的class复写。

为了解决固定大小的数组的问题，即不止一个key对象会产生相同的index，这就是哈希碰撞。因此数组的大小并没有关系，任何一个key对象的哈希值会落在数组的任何一个地方。

因此查找一个value的过程从**计算哈希值，并使用它来作为一个数组下标**开始。如果你能确保没有哈希碰撞（如果有固定数目的值，这个就是可能的），然后你得有一个完美的哈希方法，但这是一个特例。在其他的情况中，碰撞是通过**外部链接**来解决的：数组并不是直接指向值，而是一个值的列表。这些值是通过**equals()** 这个方法来做线性搜索。当然，这种search是很慢的，但是如果这个哈希函数是足够好，这样每一个slot就只有几个值。因此不用搜索整个表，而直接跳到一个slot，在比较几个值就好了。这是更快的，这就是**HashMap**如此快的原因。

因为 哈希表中的"Slot"通常叫做 ***buckets***，代表实际表的数组叫做 ***bucket***。
⌊

#### 2021-06-27 补充

#### Hash碰撞的解决方式
* Chaining
  * Hash functions
    * The division method  
      ``` 
      h(k) = k mod m 
      ```
      使用该方法时，通常我们需要避免使用确定的m值。m不应当是2的次方，如果m=2的p次方，那么h(k)则是k的p低位字节。  
      一个不是很接近2的次方的质数，通常是一个好选择。
    * The multiplication method  
      ```
      h(k) = ⌊m(kA mod 1)⌋
      ```
      这种Hashing方法分两步。
      * 第一步：  用常量A和key相乘，常量A的范围在0-1之间，然后取出kA的小数部分。
      * 第二步：  然后再乘以m，并取结果值的floor值  （注：floor(x) 值指的是小于或等于x的最大整数，用**⌊⌋**表示而ceiling(x) 指的是大于或者等于x的最小整数，用⌈⌉表示）。  
      这么做的一个好处是，m的值不再关键，通常可以选2的n次方。
      kA mod 1 的意思是取kA的小数部分，也就是 kA - ⌊kA⌋
    * Universal hashing  
      随机选择hash方法，并与key相独立。
* Open address  
  所有的元素都存在hash表中，所以这种实现的hash表会被填满。它的load factor一定不会超过1。
  它相对于chaining的优势在于：
  * 不使用指针，当容量很大时，会节约内存
  * 更少的碰撞
  * 更快的查询  

  **插入的流程：**  
  不断的检查，直到找到一个空的slot，并插入。
* Perfect hashing  


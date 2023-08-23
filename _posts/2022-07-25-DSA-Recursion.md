---
layout: post
title:  "DSA: Recursion"
date:   2022-07-25 17:50:00
comments: true
categories: DSA
---

* TOC
{:toc}


### 递归的特点  
递归是一种通过调用自己来解决问题的办法。
因为是调用自己，所以必须满足两个条件，才不会导致无限循环。
1.  base case，这是一个不需要继续使用递归来获取结果的场景
2.  递归关系，可以让所有的case简化到base case，这种关系，可以用一个递归方程来表示，比如：
```
f(i) = f(i-1) + f(i-2)
``` 

如果能够写出递归方程，并有base case，那么就可以写出代码，解决问题。

*   练习：
    *   [1. Swap Notes in Pairs](https://leetcode.com/problems/swap-nodes-in-pairs/)
    *   [2. Reverse String](https://leetcode.com/problems/reverse-string/) 
    *   [3. Reverse LinkedList](https://leetcode.com/problems/reverse-linked-list/)
    *   [4. Search in a Binary Seach Treee](https://leetcode.com/problems/search-in-a-binary-search-tree/)
    *   [5. Pascal's Triangle II](https://leetcode.com/problems/pascals-triangle-ii/)


### 递归中的重复问题
当我们观察递归方程时，很容易的发现有很多中间的结果，重复计算了。这导致了很多无谓的计算，提高了算法复杂度。那么怎么解决这个问题呢？我们可以采取空间换时间的方法，将已经计算过的存储起来，当再碰到时，直接返回结果即可。

* 练习
    * [6. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/)
    * [7. Climbing Stairs](https://leetcode.com/problems/climbing-stairs/)


### 复杂度分析
#### 时间复杂度  
典型的是递归执行的次数与方法的复杂度的乘积。

但是对于递归方法来说，方法的调用次数和输入的数量成正比是比较少见的。一般情况下，我们可以考虑**执行树**。

*  **执行树（Execution tree）** 
    常用于表示递归方法的执行流，每一个节点表示一次执行。所以所有的节点数，即所有的递归方法执行次数总和。  

    根据递归方程中，递归的次数，会有对应的次数的执行树。比如Fibonacci的执行树就是一个二叉树。因为是一个二叉树，所以可以估计其时间复杂度为O(2^n)。

    使用memoization，可以将复杂度降低到O(n)。因为f(n)依赖之前的n-1次计算，所以需要执行n-1次方法，即O(n)。

#### 空间复杂度  
这里包含两部分，一部分是递归部分，另一个非递归部分。

*   递归部分  
递归方法调用时，系统会将三部分信息存储在系统的方法调用栈上：
    *   返回地址
    *   传递给方法调用的参数
    *   本地变量    
这些存储占用会在方法返回时释放，也就是说他们是会累积到最后一次方法调用，从而就有可能超过了程序栈的最大空间限制而出现**stack overflow**。
*   非递归部分  
主要会是一些全局变量，比如memoization时所用的map。他们大部分会存储在heap上。  

#### 尾递归  
尾递归是递归中的特殊情况，即方法中仅存在一个递归调用，而且是方法中的最后一个指令。  
因为递归调用结束时，即直接返回，所以就不需要使用stack记录caller的信息。Kotlin中可以使用**tailrec**来获取尾递归的优化，会将其转换为循环调用。

#### 练习
*   [8. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)
*   [9. Pow(x, n)](https://leetcode.com/problems/powx-n/)
*   [10. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)
*   [11. K-th Symbol in Grammar](https://leetcode.com/problems/k-th-symbol-in-grammar/)
*   [12. Unique Binary Search Trees II](https://leetcode.com/problems/unique-binary-search-trees-ii/)


### 应用范式(算法设计策略)
#### Divide & Conquer 
D&C是通过递归的方式，将问题拆成更小的类似的子问题，直到子问题不需要继续拆分而直接拿到结果，然后将各个子问题的结果合并。这里的子问题，至少有两个，如果像二分查找那样只有一个子问题，可以叫做 **decrease & conquer**。
*   一般步骤
    1.  Divide
    2.  Conquer
    3.  Combine
*   经典案例
    *   归并排序(Merge Sort)  
        归并排序是一种高效的，一般用途的排序算法。它有两种实现方式：**top down**和**bottom up**。  

        * top down  
        **top down**是一个典型的D&C，可以分为三个步骤：
            1.  Divide. 将未排序的list分割成多个sublist
            2.  Conquer. 将每个sublist排序
            3.  Combine. 将已经排好序的sublist合并为新的排好序的list 

          [参考代码](https://github.com/dreamhb/practice/blob/master/sort/merge_sort/Mergesort.java)
        * bottom up  
        Bottom up则是一开始就将list分割为只有一个元素的多个子表，然后每次合并两个，直到最终完成排序。

    *   快速排序(Quick Sort)  
        我们可以用三个步骤来解决：
        1.  选择一个pivot，将list分为两个sublist，并且将小于pivot值的放到左边，大于的放到右边
        2.  继续递归排序产生的sublist
        3.  将排好序的sublist合并成最终结果

        *   sample code
        ```java
        private void quickSort(int[] list, int lo, int hi) {
            if (lo < hi) {
                int mid = partition(list, lo, hi);
                quickSort(list, lo, mid-1);
                quickSort(list, mid+1, hi);
            }
        }
        ``` 
        *   [full code](https://github.com/dreamhb/practice/blob/master/sort/quick_sort/QuickSort.java)
    
*   模版代码  
```java
void divideConquer() {
    S subproblems = divide();
    List results = divideConquer(s in S);
    combine(results)
}
```
*   [Master theorem](https://en.wikipedia.org/wiki/Master_theorem_(analysis_of_algorithms))
*   练习
    *   [13. Sort an Array](https://leetcode.com/problems/sort-an-array/)
    *   [14. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
    *   [15. Search a 2D Matrix II](https://leetcode.com/problems/search-a-2d-matrix-ii/)  


#### Backtracking  
适合于解决一些计算型问题，通过不断的获取新的候选结果，并果断放弃（backtrack）不合要求的部分结果。  通常情况下backtracking要比brute-force要快很多，因为会舍弃一些不符合要求的计算。  

基本流程是，当我们发现某一个节点不满足我们的要求时，返回到上一个节点，并尝试其他的可能选项。
*   模版  
```java
void backtrack(candidate) {
    if (isSolution(candidate)) {
        put2Solutions(candidate)
        return
    }

    for (nextCandidate in candidateList) {
        if (isValid(nextCandidate)) {
            //将当前的candidate加入不完整的结果中
            put(nextCandidate)
            //基于当前的candidate，继续尝试其他的
            backtrack(nextCandidate)
            //1. 当遇到不符合要求的结果时，回到上一步，继续尝试其他情况
            //2. 当得到一个最终结果时，因为是要获取所有的结果，所以还是需要继续backtracking，看是否还有其他solution
            remove(nextCandidate)
        }
    }
}
```

*   练习
    *   [16. N-Queens II](https://leetcode.com/problems/n-queens-ii/)
    *   [17. Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)
    *   [18. Combinations](https://leetcode.com/problems/combinations/)
    *   [19. Robot Room Cleaner](https://leetcode.com/problems/robot-room-cleaner/)

#### Divide & Conquer(D&C) vs Backtracking  
这两种常见的范式，有各自适用的场景，并且也常用递归的方式来表示。
*   D&C 只会有一个结果，而Backtracking的结果是不确定的
*   D&C 的每一步都是必不可少的，而Backtracking的中间过程有些是没有作用的
*   D&C 的步骤是已知的，即我们知道每一步要做什么，而Backtracking是要多种尝试，并不是提前就知道每一步会做什么的。

*   练习
    *   [20. Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)
    *   [21. Permutations](https://leetcode.com/problems/permutations/)
    *   [22. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)
    *   [23. The Skyline Problem](https://leetcode.com/problems/the-skyline-problem/)  


### 其他
#### Recursion to Iteration  
递归虽然很直观，但有时会导致stackoverflow，同时也可能存在效率的问题，比如重复计算，还有可能增加代码的理解难度，比如嵌套的递归。所以必要时，我们可以将递归转化成迭代的形式。

*   思路  
递归实际上是我们利用了系统的stack，所以我们可以将系统的stack，用Stack或者Queue这些数据结构来替换。

*   练习
    *   [24. Same Tree](https://leetcode.com/problems/same-tree/)
    *   [25. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)
    *   [26. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)
    *   [27. Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/)

### 总结
递归在编程中是非常的常见的，同时Divide&Conquer和Backtracking这两个非常常用的算法设计策略，也通常用递归来来实现的。同时还需要多加练习，提高对知识的理解。
### 参考
*   [Recursion I](https://leetcode.com/explore/learn/card/recursion-i)
*   [Recursion II](https://leetcode.com/explore/learn/card/recursion-ii)
*   [Divide & Conquer](https://en.wikipedia.org/wiki/Divide-and-conquer_algorithm)

#### 练习参考代码

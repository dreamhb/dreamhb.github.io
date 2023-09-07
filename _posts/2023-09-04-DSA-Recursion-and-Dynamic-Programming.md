---
layout: post
title:  "Recursion and Related Algorithm Paradigms"
date:   2023-09-03 10:00:00
comments: true
categories: DSA
---


* TOC
{:toc}

### Recursion in general
In computer science, **recursion** is a method of solving computational problems where the solutions depends on subproblems' solutions. Recursion uses functions that call themselves within their own code.  
The following code illustrate recursive function call:  

```java
f(n) = f(n - 1) + f(n - 2)
```

A common algorithm design tactic is to **divide** the original problem into smaller subproblems and **combine** their solutions.  

This is ofen called **Divide and Conquer**. When there are duplicated work during the process, we can use *Table* to cache the previously calculated results.  

This can be referred to as **Dynamic Programming** or **Memoization**.


#### Properties
*   **Base case(s)**  
When we divide the problem into subproblems further and further, at some point we no need to divide any further, then the **base case(s)** is reached. If you forget to write the base case, an infinite loop will occur.

*   **Recurrence Relation** 
A set of rules reduce all other cases toward base case. It is the relation between results of a problem and results of it's subproblems. 

The complexity analysis of recursion is [here]({% link _posts/2023-08-20-DSA-Big-O.md%}).

#### How to Approach  
There are many ways we might divide a problem into subproblems.  The following three ways are the most common approaches to develop an algorithm.

*   **Bottom-Up Approach**   
From base case to more complex cases and finally reachs the result.
*   **Top-Down Approach**   
This approach we think about the problem from case N, and how to divide case N into subproblems until base case reached.  
Here if we have duplicate calculations, we could cache the results of them to improve runtime complexity.
*   **Half-and-Half Approach**  
Like Binary Search, we might divide the problem or dataset in half.  

#### Recursion vs. Iteration
Recursion and Iteration are equally expressive, in some cases recursion might be more intuitive.  
Because recursion uses system stack, we can replace recursion by iteration with explicit call stack. 
Recursion might have some overhead since its implicit system stack.

As we might see in some occasions, stackoverflow exception will be through.


#### Tail Recursion
Tail Recursion is a recursion where the recursive call the last instruction in the function, and there will be only one recursive function.

The benefit of having tail recursion is that it could avoid the accumulation of stack overheads during the recursive calls since the system could reuse a fixed amount space of stack in each recursive call.  


### Algorithm Design Techniques

**Divide and Conquer**, **Backtracking** and **Dynamic Programming** are important and common paradigms in algorithm design.  
And they all use recursion, for Dynamic Programming it is top down apporach.

#### Divide and Conquer
A divide and conquer algorithm works by **recursively** breaking the problem down into two or more subproblems of the same or related type, then combine the results of subproblems to form the final solution.  

It is naturally implemented in the form of recursion.

The major difference between **Divide and Conquer** and other recursive algorithms is that it divides problem into **two or more** simillar parts.

The typical applications of divide and conqure are **Quick Sort** and **Merge Sort**, we will cover them in more detailed article later.

#### Backtracking  
It is another paradigm implemented in the form of recursion.  

According to *Wikipedia*, <code> backtracking is a class of algorithms for finding solutions to some computational problems, notably [constraint satisfaction problems](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem), that incremently builds candidates to the solutions, and abandons("backtrack") a candidate as soon as it determines that the candidate cannot be a valid solution.</code>  

When it is applicable, backtracking is often faster than brute-force solution since it will eliminate many candidates early.

Eight queens puzzle is a typical problem can be solved by backtracking.

Further explaination about the details will come later.

#### Dynamic Programming
Dynamic Programming is an algorithmic paradigm that can simplify a complicated problem by breaking it down into simpler subproblems in a recursive manner and systematically and efficiently exlpore all possible solutions to a problem.

Problems are applicable must have two properties:
*   **Optimal substructure** means the solution can be obtains by combining of optimal solutions of subproblems
*   **Overlapping subproblems** means some subproblems could be solved over and over again

Problems are **Divide and Conquer** applicable also have optimal substructures but they don't have overlapping subproblems, such as *Quick Sort* and *Merge Sort*, we just combine the solutions of subproblems into the final one.

There are two ways to implement a DP algorithm:
*   **Top-down approach(memoize)** has a recursive form while caching overlapping subproblems
*   **Bottom-up approach** forms solution from subproblems, usually done in a tabular form

There will be a more detailed article about this.


### Conclusion
Recursion is a powerful tool and many algorithm paradigms are based on it.  
The base case or cases are important since there will be infinite loop without them.

### References
*   [https://en.wikipedia.org/wiki/Recursion_(computer_science)](https://en.wikipedia.org/wiki/Recursion_(computer_science))
*   [LeetCode Recursion Topic](https://leetcode.com/explore/learn/card/recursion-ii/470/divide-and-conquer)
*   [https://en.wikipedia.org/wiki/Dynamic_programming](https://en.wikipedia.org/wiki/Dynamic_programming)

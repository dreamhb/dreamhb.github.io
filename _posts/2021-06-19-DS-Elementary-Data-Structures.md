---
layout: post
title:  "Elementary data structures: Stack Queue LinkedList"
date:   2021-06-19 17:50:00
comments: true
categories: ds
---

* TOC
{:toc}

## Stacks & Queues  
**Stack** is **last in, first out**, **queue** is **first in, first out**.  

### Stacks
#### Representation 
  Using a simple array.

#### Basic Operations  
* Empty

```java
public empty(Stack s) {
  if (s.top == 0) {
    return true;
  }
  return false;
}
```

* Push

```java
public void push(Stack s, int x) {
  s.top = s.top + 1;
  s[s.top] = x;
}

```

* Pop

```java
public int pop(Stack s) {
  if (empty(s)) {
    error()
  } else {
    s.top = s.top - 1;
    return s[s.top+1];
  }
}
```

### Queues
#### Representation  
Queue has a **head** and a **tail**.
* Circular ring  
  Implement a queue of at most n -1 elements using an array Q[1..n]. The elements in the queue reside in locations Q.head, Q.head + 1, ..., Q.tail - 1. When Q.head = Q.tail, the queue is empty. When Q.head = Q.tail + 1, the queue is full.
  It has some advantages over normal array.
  * No need to move items after operations
* LinkedList  

#### Basic Operations  
* enqueue

```java
public void enqueue(Queue q, int x) {
  q[q.tail] = x;
  if (q.tail == q.length) {
    q.tail = 1;
  } else {
    q.tail = q.tail + 1;
  }
}
``` 

* dequeue  

```java
public int dequeue(Queue q) {
  int x = q[q.head];
  if (q.head == q.length) {
    q.head = 1;
  } else {
    q.head = q.head + 1;
  }
  return x;
}
```


## LinkedList  
* doubly linked list
* singly linked list
* sorted
* circular list

### Basic operations

####  Search  

```java
public int search(List l, int k) {
  List x = l.head;
  while (x != null && x.key != k) {
    x = x.next;
  }
  return x;
}
```

####  Insertion  

```java
public void insert(Node head, Node x) {
  x.next = head;
  if (head != null) {
    head.prev = x;
  }
  head = x;
  x.prev = null;
}
```

####  Deletion  
```java
public void delete(Node root, Node x) {
  //handle prev
  if (x.prev != null) {
    x.prev.next = x.next;
  } else {
    root = x.next; // x is the head
  }

  //handle next
  if (x.next != null) {
    x.next.prev = x.prev;
  }
}
```
We can use **sentinel** to simplify boundary conditions.

## Representing rooted trees

### Binary trees  
We can represente a tree using **p**, **left**, **right** properties.  

### Rooted trees with unbounded branching  
* bounded  
  We replace left right with child1, child2, ..., childk.

* unbounded  
  We use **left-child, right-sibling representation**. So eacho node x has only two pointers:
  * x.left-child poinits to the leftmost child of node x
  * x.right-sibling poinits to the sibling of x to its right




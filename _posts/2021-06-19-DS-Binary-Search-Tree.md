---
layout: post
title:  "DS-Binary-Search-Tree"
date:   2021-06-14 17:50:00
comments: true
categories: data structures & algorithms
---

# Binary Search Tree
## Definition
Binary search tree satisfies the **binary-search-tree property**:
Let x be a node in a binary search tree. If y is a node in the left subtree of x, then x.key >= y.key. If y is a node in the right subtree of x, then y.key >= x.key.

## Traverse  
*   In-order
```java
void inOrder(Tree node) {
    inOrder(node.left);
    print(node.key);
    inOrder(node.right);
}
```
*   Pre-order
```java
void preOrder(Tree node) {
    print(node.key);
    preOrder(node.left);
    preOrder(node.right);
}
```
*   Post-order
```java
void postOrder(Tree node) {
    postOrder(node.left);
    postOrder(node.right);
    print(node.key);
}
```
## Basic Operations
*   Search
```java
public Tree search(Tree node, int key) {
    if (key == node.key || node == null) {
        return node;
    }
    if (key > node.key) {
        return search(node.right, key);
    } else {
        return search(node.left, key);
    }
}
```
```java
public int search(Tree node, int key) {
    while(node != null && node.key != key) {
        if (node.key > key) {
            node = node.left;
        } else {
            node = node.right;
        }
    }
    return node.key;
}
```
*   Minimum
```java
public int min(Tree node) {
    while(node != null && node.left != null) {
        node = node.left;
    }
    return node.key;
}
```
*   Maximum
```java
public int max(Tree node) {
    while(node != null && node.right != null) {
        node = node.right;
    }
    return node;
}

```
*   Successor
    *   definition  
        the smallest key larger than current node
    *   code
    ```java
    public int successor(Tree node) {
        //if has right subtree, find the minimun in it
        if (node.right != null) {
            return minimum(node.right);
        }
        //otherwise it must be current node's parent and current is the left subtree of it
        Tree y = node.parent;
        while(y != null && node == y.right) {
            y = y.parent;
        }
        return y.key;
    }
    ```
*   Predecessor
*   Insertion
*   Deletion
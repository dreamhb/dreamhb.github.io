---
layout: post
title:  "DS-Binary-Search-Tree"
date:   2021-06-14 17:50:00
comments: true
categories: ds
---

# Binary Search Tree

* TOC
{:toc}

## Definition
Binary search tree satisfies the **binary-search-tree property**:
Let x be a node in a binary search tree. If y is a node in the left subtree of x, then x.key >= y.key. If y is a node in the right subtree of x, then y.key >= x.key.

## Traverse  

####   In-order
```java
    void inOrder(Tree node) {
        inOrder(node.left);
        print(node.key);
        inOrder(node.right);
    }
```  

####  Pre-order
```java
void preOrder(Tree node) {
    print(node.key);
    preOrder(node.left);
    preOrder(node.right);
}
```  

####   Post-order
```java
void postOrder(Tree node) {
    postOrder(node.left);
    postOrder(node.right);
    print(node.key);
}
```
## Basic Operations  

####   Search
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

####  Minimum
```java
public int min(Tree node) {
    while(node != null && node.left != null) {
        node = node.left;
    }
    return node.key;
}
```  

####   Maximum

```java
public int max(Tree node) {
    while(node != null && node.right != null) {
        node = node.right;
    }
    return node;
}
```

####   Successor
*   definition  
        the smallest key larger than current node.
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

####  Predecessor  
*   definition  
        the largest key smaller than current node.
*   code
    ```java
    public int predecessor(Tree node) {
        if (node.left != null) {
            return maximum(node.left);
        }
        //otherwise it must be the current node's parent and current is the right subtree of it
        Tree y = node.parent;
        while(y != null && node == y.left) {
            y = y.parent;
        }
        return y.key;
    }
    ```  

####  Insertion  
find the position and insert it, it always will be the leaf node.
```java
    public void insert(Tree root, Tree x) {
        Tree parent = null;
        while (root != null) {
            parent = root;
            if (x.key < root.key) {
                root = root.left;
            } else {
                root = root.right;
            }
        }

        if (parent == null) {
            //tree is empty
            root = x;
        }else if (z.key < parent.key) {
            parent.left = x;
        } else {
            parent.right = x;
        }
    }
```  

####   Deletion  
Deletion of binary search tree has three conditions.
*   the node to delete has no child
*   the node to delete has one child only
*   the node to delete has two children （left & right）
    *   the minimum of the right subtree is the right child
    *   the minimum of the right subtree is not the right child 

    
    ```java
    //replace subtree u of root with v
    public void transplant(Tree root, Tree u, Tree v) {
        if (u.p == null) {
            root = v;
        } else if (u == u.p.left) {
            u.p.left = v;
        } else {
            u.p.right = v;
        }
        if (v != null) {
            v.p = u.p;
        }
    }
    ```
    ```java
    public void delete(Tree root, Tree x) {
        if (x.left == null) {
            //has only right child
            transplant(root, x, x.right);
        } else if (x.right == null) {
            //has only left child
            transplant(root, x, x.left);
        } else {
            Tree min = minimum(x.right);//find min in right tree
            if (min.p != x) {
                transplant(root, y, y.right);
                y.right = x.right;
                y.right.p = y;
            }
            transplant(root, x, y);
            y.left = x.left;
            y.left.p = y;
        }
    }

    ```
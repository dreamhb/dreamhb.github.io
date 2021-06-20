---
layout: post
title:  "Design Patterns: Adapter & Facade"
date:   2021-06-20 17:50:00
comments: true
categories: design-patterns
---

# Adapter Pattern & Facade Pattern  

* TOC
{:toc}

## Adapter Pattern  
The Adapter Pattern converts the interface of a class into another interface the clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.

### Object and class adpaters  
The only difference is that with class adapter we subclass the Target and the Adaptee, while with object adapter we use composition to pass requests to an Adaptee. And class adapter needs multiple inheritance which java not support.


## Facade Pattern  
The Facade Pattern provides a unified interface to a set of interfaces in a subsystem. Facade defines a higher-level interface that makes the subsystem easier to use.

### The Principle of Least Knowledge 
It guides us to reduce the interactions between objects to just a few close "friends". Talk only to your immediate friends. 
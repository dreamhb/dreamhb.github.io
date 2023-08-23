---
layout: post
title:  "Design Patterns Decorator"
date:   2021-06-20 19:50:00
comments: true
categories: design-patterns
---

# The Decorator Pattern: Decorating Objects

*   TOC
{:toc}

## Definition  
The Decorator Pattern attaches additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

## The Open-Closed Principle  
Classes should be open for extension, but closed for modification.  
* **Open**  
Feel free to extend our classes with any new behavior you like. If you needs or requirements change, just go ahead and make your own extensions.  
* **Closed**
We spent a lot of time getting this code correct and bug free, so we can't let you alter the exiting code. It must remain closed to modification.

## Inheritance vs. Composition  
*   Our behavior can only be determined statically at compile time when rely on inheritance, in other words, we get only whatever behavior the superclass gives us or that we override. If we want to add new behavior, we have to go in and change existing code. That contradicts **Open-Closed Principle**.
*   With composition, we can mix and match decorators any way we like at runtime.

So decorator uses inheritance to achieve the __type_matching__, but do not use inheritance to get behavior.

## Applications
### Java I/O

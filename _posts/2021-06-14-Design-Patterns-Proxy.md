---
layout: post
title:  "Design-Patterns-Proxy"
date:   2021-06-14 17:50:00
comments: true
categories: main
---

# Proxy Pattern: Controlling Object Access  
* TOC
{:toc}

**Definition**  
Use the Proxy Pattern to create a representative object that controls access to another object, which may be remote, expensive to create, or in need of securing.

#### Remote Proxy  
controls access to remote object.
##### Applications
*   Java RMI
*   Android AIDL/Binder

#### Virtual Proxy  
controls access to a resource that is expensive to create.  
The virtual proxy often defers the creation of the object until it is needed (like lazy initilization); the Virtual proxy also acts as a surrogate for the object before and while it is being created. After that, the proxy delegates requests directly to the real subject.
#### Protection Proxy  
controls access to a resource based on access rights.

### Implementation
#### Static Proxy  
Pre-generation proxy class is created in compile-time.  

#### Dynamic Proxy  
A dynamic proxy class is a class that implements a list of interfaces specified at **runtime**, such that a method invocation through one of the interfaces on an instance of the class will be encoded and dispatched to another object through a **uniform interface**. A dynamic proxy class can be used to create a **type-safe** proxy object for a list of interfaces without requiring **pre-generation** of the proxy class, such as with compile-time tools.
##### Application
*   Retrofit

## vs. Decorator Pattern
Proxy pattern controls access to objects.  
Decorator pattern adds features to objects.
---
layout: default
title:  "Android-IPC-Binder"
date:   2019-04-04 17:50:00
comments: true
categories: android
---

Binder is an IPC method using in Android platform.  

### Question:  
Q1. Android bases on Linux, and Linux has many IPC methods such as **Socket** **Pipe**, why Android using the Binder ?  
A1. support transfer file descriptor across processes  
    support object mapping  
    support client-server model  
Q2. How does Binder work ?  


Q3. What is the architecture of Binder ?  


Q4. How the data flows from client process to and from server process using Binder ?  


#### Below is the procedure of Client comminicates with Service using AIDL Binder IPC  

![Android IPC Procedure](/images/IPC.svg)

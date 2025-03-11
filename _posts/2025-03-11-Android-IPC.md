---
layout: post
title:  "Android IPC"
date:   2025-03-10 17:50:00
comments: true
categories: Android
---

# Android IPC

* TOC
{:toc}

IPC is the core component of Android OS.

Applications need to communicate with system servers like ActivityManagerService to open activities and services, but they are in different processes.  
How to communicate with them?

The Android OS uses Binder as its main method for IPC.

In the following series of blogs, I will take a deep dive into the procedure of IPC from Application to the binder driver in the kernel.
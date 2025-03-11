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

How to write this serial blogs about Android IPC from Application to kernel?
And the tools to analyze the AOSP code. I think they are also useful.
Also the kernel knowledge.

1> the procedure of data and command flows from app to kernel and vice vesa.
2> the tools used to analyze the AOSP code.
    a> gdb
3> the remaining questions to be answered.
    a> the thread communications between Client and Binder
    b> the service itself handles the threads for IPC operations, so when and how the threads are created and how the binder knows about them?
4> where to publish the content to maximum the influence of my blog.

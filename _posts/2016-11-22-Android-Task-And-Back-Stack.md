---
layout: default
title:  "Android Task & Back Stack"
date:   2016-11-22 10:54:45
categories: main
---

### Task

means a collection of Activities which do a certain job.

### Back Stack

The Activities in collection are routed in a stack, when press **back button**, the top will pop up, and finished.


### Activity Launch mode

We can define Activity launch mode in Two ways:

- define launchMode in AndroidManifest.xml
- start activity using intent ,and set flags


#### launchMode
- ***can have multi instances***
1. stardand: default behavior
2. singleTop:same as above, but when the start activity in the Target task and on top of it stack, the onNewIntent of it will be called.

- ***can have only one instances***
1. singleTask: other activity can be routed in the task.
2. singleInstance: only this activity can be in this task.

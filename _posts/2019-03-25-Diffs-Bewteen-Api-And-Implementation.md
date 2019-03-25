---
layout: default
title:  "Difference between Android gradle build dependencies"
date:   2019-03-25 11:00:00
comments: true
categories: android
---

Inside a **dependency** block, we can declare a library dependency using one of several dependency configurations. The following table shows the differences between them.  

| configuration | behavior | advantage |
| ---- | ------ | ---- |
| implementation | exist in compile time classpath and build output but only visible to other modules at runtime |  significantly reduce build time |
| api | just like **implementation**, but transtively export to other modules | if using too much, it will sigificantly increase build time |
| compileOnly | only exists in compile time | reduce the final apk size by not adding transient dependencies that aren't critical |
| runtimeOnly | only exists in runtime, in build output |  |
| annotationProcessor | if the dependency is an annotation processor | |


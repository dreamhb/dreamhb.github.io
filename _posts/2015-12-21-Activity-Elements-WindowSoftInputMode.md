---
layout: post
title:  windowSoftInputMode详解
excerpt: "主要用来设置activity的主window如何和包含屏幕软键盘的window交互问题，其影响俩个事情：1.软键盘的状态-隐藏或者显示-当activity获取到用户的焦点。2.activity主window的调整-是变的更小来为软键盘腾出空间还是内容容器让当前焦点可见当window的其他部分被键盘覆盖"
modified: 2015-12-21
tags: [android]
comments: true
image:
feature: sample-image-3.jpg
---

**这里设置的值（除了"stateUnspecified" 和 "adjuextUnsoecified")会覆盖主题中的设置**

####各个属性的解释####
|| *属性* || *解释* ||
||一二三||四五六||


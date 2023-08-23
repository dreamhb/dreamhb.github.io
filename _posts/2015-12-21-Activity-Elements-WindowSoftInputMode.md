---
layout: post
title:  windowSoftInputMode详解
excerpt: "主要用来设置activity的主window如何和包含屏幕软键盘的window交互问题，其影响俩个事情：1.软键盘的状态-隐藏或者显示-当activity获取到用户的焦点。2.activity主window的调整-是变的更小来为软键盘腾出空间还是内容容器让当前焦点可见当window的其他部分被键盘覆盖"
modified: 2015-12-21
tags: [android]
comments: true
image:
    feature: default_header.jpg
---

**这里设置的值（除了"stateUnspecified" 和 "adjuextUnsoecified")会覆盖主题中的设置**

#### 各个属性的解释

##### stateUnspecified
软键盘的状态（无论是隐藏还是显示）没有明确指定。系统会选择一个合适的状态或者取决于主题中的设置。
这个是软键盘行为的默认设置。

##### stateUnchanged
软键盘保持上一次的状态，无论是显示或者隐藏，当activity来到前台时。

##### stateHidden
软键盘是隐藏的，当用户选择这个activity时-当用户被导向到此activity，而不是因为离开其他activity而回退到这个activity。
简单点说就是，当用户通过主动点击到达的activity，而不是从其他activity回退到当前的activity，此时，软键盘是隐藏的。

##### stateAlwaysHidden
当activity的主window拥有输入焦点时，软键盘总是隐藏的
 
##### stateVisible
在常见合适的场合，软件盘是可见的（当用户主动选择了当前的activity）

##### stateAlwaysVisible
无论activity是主动选择还是从上一个activity回退，软键盘都是显示的

##### adjustUnspecified
主窗口行为的默认设置。
activity的主window是调整大小来为软键盘腾出空间还是window 空间
让当前的焦点在屏幕上可见是不确定的。系统会自动选择其中一个状态，规则是window的内容是否包含可以滑动布局视图。如果有，window就会被改变大小，假设在滑动能够让window中所有内容在更小的空间中可见。

##### adjectResize
activity的主window总是会改变大小来给输入法窗口腾出空间

##### adjectPan
activity会改变大小。然而，window中的内容会自动的**panned**（此处不知道怎么解释），这样的话当前的焦点不会模糊让人困惑，而且用户总是能够看到他们输入的内容。通常来说，这个不如改变大小好，因为用户需要去关闭软键盘去与其他部分进行交互。



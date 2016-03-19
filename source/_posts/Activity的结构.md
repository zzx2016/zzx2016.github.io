---
title: Activity的结构
date: 2016-03-19 15:22:57
tags: Android
---

[节选出处](http://www.imooc.com/article/3411)

每个Activity包含一个`PhoneWindow`对象，`PhoneWindow`设置`DecorView`为应用窗口的根视图。在里面就是熟悉的`TitleView`和`ContentView`，没错，平时使用的`setContentView()`就是设置的`ContentView`。

![结构图](http://7q5ctm.com1.z0.glb.clouddn.com/1425690-02aca4cf429df16d.png)

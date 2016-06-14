---
title: Android中文本的属性
date: 2016-06-01 11:39:13
tags: Android
---

[原文地址](http://gold.xitu.io/entry/574d53d1128fe10055f1eccd)

![picture](http://7q5ctm.com1.z0.glb.clouddn.com/Android%E4%B8%AD%E6%96%87%E6%9C%AC%E7%9A%84%E5%B1%9E%E6%80%A7.png)

getFontMetrics()，getFontMetricsInt()用于返回字符串的测量，而两个方法的区别就是返回值得类型。返回值一共有五个属性:

1. Top : baseline到文本顶部的最大的距离
2. Ascent : baseline到文本顶部的推荐距离
3. Descent : baseline到文本底部的推荐距离
4. Bottom : baseline到文本底部的最大距离
5. Leading : 两行文本之间推荐的额外距离，一般为0

在Android的坐标系之中，向下为Y轴正方向，向上位Y轴负方向，所以baseline之上的Top与Ascent都是负数，而baselin之下的Descent、Bottom都是正数。如果要让字符串在垂直方向上居中，则需要在纵坐标上增加Asecent绝对值与descent的差。
leading值是一行文本的底部与下一行文本的顶部之间的距离，图中就是line1的橙色线条与line2的紫色线条之间测距离，一般情况下是0。根据stackoverflow中的说法，是一行的bottom与下一行的top已经为字符串提供了足够的空间，所以leading的值在一般情况下都为0。需要注意的是，在获取FontMetrics之前请完成Paint的设置，这样才能准确的获取测量值。

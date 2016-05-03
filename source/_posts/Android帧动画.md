---
title: Android帧动画
date: 2016-03-25 11:27:09
tags:
- Android
- 动画
---

[原文地址](http://blog.csdn.net/yanbober/article/details/46481171)

### 帧动画概述

帧动画允许你实现像播放幻灯片一样的效果，这种动画的实质其实是Drawable，所以这种动画的XML定义方式文件一般放在res/drawable/目录下。

如下图就是帧动画的源码文件：

<!--more-->

![源码文件](http://7q5ctm.com1.z0.glb.clouddn.com/%E5%B8%A7%E5%8A%A8%E7%94%BB-1.png)

可以看见实际的真实父类就是Drawable。

### 帧动画详细说明

我们依旧可以使用xml或者java方式实现帧动画。但是依旧推荐使用xml，具体如下：

`<animation-list>` 必须是根节点，包含一个或者多个`<item>`元素，属性有：

- android:oneshot true代表只执行一次，false循环执行。

`<item>` animation-list的子项，包含属性如下：

- android:drawable 一个frame的Drawable资源。
- android:duration 一个frame显示多长时间。

### 帧动画使用

关于帧动画相对来说比较简单，这里给出一个常规使用框架，如下：

```xml
<!-- 注意：rocket.xml文件位于res/drawable/目录下 -->
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot=["true" | "false"] >
    <item
        android:drawable="@[package:]drawable/drawable_resource_name"
        android:duration="integer" />
</animation-list>
```

```java
ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
rocketImage.setBackgroundResource(R.drawable.rocket_thrust);

rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
rocketAnimation.start();
```

**特别注意，AnimationDrawable的start()方法不能在Activity的onCreate方法中调运，因为AnimationDrawable还未完全附着到window上，所以最好的调运时机是onWindowFocusChanged()方法中。**

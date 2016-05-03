---
title: Android视图动画
date: 2016-03-31 16:20:26
tags:
- Android
- 动画
---

[原文地址](http://blog.csdn.net/yanbober/article/details/46481171)
### 视图动画概述

视图动画，也叫Tween（补间）动画可以在一个视图容器内执行一系列简单变换（位置、大小、旋转、透明度）。譬如，如果你有一个TextView对象，您可以移动、旋转、缩放、透明度设置其文本，当然，如果它有一个背景图像，背景图像会随着文本变化。补间动画通过XML或Android代码定义，建议使用XML文件定义，因为它更具可读性、可重用性。

如下是视图动画相关的类继承关系：

<!--more-->

![视图动画相关的类继承关系](http://7q5ctm.com1.z0.glb.clouddn.com/%E8%A7%86%E5%9B%BE%E5%8A%A8%E7%94%BB-1.png)

|java类名|xml关键字|描述信息|
|-|-|-|
|AlphaAnimation|<alpha>放置在res/anim/目录下|渐变透明度动画效果
|RotateAnimation|<rotate>放置在res/anim/目录下|画面转移旋转动画效果
|ScaleAnimation|<scale>放置在res/anim/目录下|渐变尺寸伸缩动画效果
|TranslateAnimation|<translate>放置在res/anim/目录下|画面转换位置移动动画效果
|AnimationSet|<set>放置在res/anim/目录下|一个持有其它动画元素alpha、scale、translate、rotate或者其它set元素的容器

通过上图和上表可以直观的看出来补间动画的关系及种类了吧，接下来我们就详细一个一个的介绍一下各种补间动画。

### 视图动画详细说明

可以看出来Animation抽象类是所有补间动画类的基类，所以基类会提供一些通用的动画属性方法，如下我们就来详细看看这些属性。

#### Animation属性详解

|xml属性|java方法|解释|
|-|-|-|
|android:detachWallpaper|setDetachWallpaper(boolean)|是否在壁纸上运行
|android:duration|setDuration(long)|动画持续时间，毫秒为单位
|android:fillAfter|setFillAfter(boolean)|控件动画结束时是否保持动画最后的状态
|android:fillBefore|setFillBefore(boolean)|控件动画结束时是否还原到开始动画前的状态
|android:fillEnabled|setFillEnabled(boolean)|与android:fillBefore效果相同
|android:interpolator|setInterpolator(Interpolator)|设定插值器（指定的动画效果，譬如回弹等）
|android:repeatCount|setRepeatCount(int)|重复次数
|android:repeatMode|setRepeatMode(int)|重复类型有两个值，reverse表示倒序回放，restart表示从头播放
|android:startOffset|setStartOffset(long)|调用start函数之后等待开始运行的时间，单位为毫秒
|android:zAdjustment|setZAdjustment(int)|表示被设置动画的内容运行时在Z轴上的位置（top/bottom/normal），默认为normal

也就是说，无论我们补间动画的哪一种都已经具备了这种属性，也都可以设置使用这些属性中的一个或多个。

那接下来我们就看看每种补间动画特有的一些属性说明吧。

#### Alpha属性详解

|xml属性|java方法|解释|
|-|-|-|
|android:fromAlpha|AlphaAnimation(float fromAlpha, …)|动画开始的透明度（0.0到1.0，0.0是全透明，1.0是不透明）
|android:toAlpha|AlphaAnimation(…, float toAlpha)|动画结束的透明度，同上

#### Rotate属性详解

|xml属性|java方法|解释|
|-|-|-|
|android:fromDegrees|RotateAnimation(float fromDegrees, …)|旋转开始角度，正代表顺时针度数，负代表逆时针度数
|android:toDegrees|RotateAnimation(…, float toDegrees, …)|旋转结束角度，正代表顺时针度数，负代表逆时针度数
|android:pivotX|RotateAnimation(…, float pivotX, …)|缩放起点X坐标（数值、百分数、百分数p，譬如50表示以当前View左上角坐标加50px为初始点、50%表示以当前View的左上角加上当前View宽高的50%做为初始点、50%p表示以当前View的左上角加上父控件宽高的50%做为初始点）
|android:pivotY|RotateAnimation(…, float pivotY)|缩放起点Y坐标，同上规律

#### Scale属性详解

|xml属性|java方法|解释|
|-|-|-|
|android:fromXScale|ScaleAnimation(float fromX, …)|初始X轴缩放比例，1.0表示无变化
|android:toXScale|ScaleAnimation(…, float toX, …)|结束X轴缩放比例
|android:fromYScale|ScaleAnimation(…, float fromY, …)|初始Y轴缩放比例
|android:toYScale|ScaleAnimation(…, float toY, …)|结束Y轴缩放比例
|android:pivotX|ScaleAnimation(…, float pivotX, …)|缩放起点X轴坐标（数值、百分数、百分数p，譬如50表示以当前View左上角坐标加50px为初始点、50%表示以当前View的左上角加上当前View宽高的50%做为初始点、50%p表示以当前View的左上角加上父控件宽高的50%做为初始点）
|android:pivotY|ScaleAnimation(…, float pivotY)|缩放起点Y轴坐标，同上规律

#### Translate属性详解

|xml属性|java方法|解释|
|-|-|-|
|android:fromXDelta|TranslateAnimation(float fromXDelta, …)|起始点X轴坐标（数值、百分数、百分数p，譬如50表示以当前View左上角坐标加50px为初始点、50%表示以当前View的左上角加上当前View宽高的50%做为初始点、50%p表示以当前View的左上角加上父控件宽高的50%做为初始点）
|android:fromYDelta|TranslateAnimation(…, float fromYDelta, …)|起始点Y轴从标，同上规律
|android:toXDelta|TranslateAnimation(…, float toXDelta, …)|结束点X轴坐标，同上规律
|android:toYDelta|TranslateAnimation(…, float toYDelta)|结束点Y轴坐标，同上规律

#### AnimationSet详解

AnimationSet继承自Animation，是上面四种的组合容器管理类，没有自己特有的属性，他的属性继承自Animation，所以特别注意，当我们对set标签使用Animation的属性时会对该标签下的所有子控件都产生影响。

### 视图动画使用方法

通过上面对于动画的属性介绍之后我们来看看在Android中这些动画如何使用（PS：这里直接演示xml方式，至于java方式太简单了就不说了），如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@[package:]anim/interpolator_resource"
    android:shareInterpolator=["true" | "false"] >
    <alpha
        android:fromAlpha="float"
        android:toAlpha="float" />
    <scale
        android:fromXScale="float"
        android:toXScale="float"
        android:fromYScale="float"
        android:toYScale="float"
        android:pivotX="float"
        android:pivotY="float" />
    <translate
        android:fromXDelta="float"
        android:toXDelta="float"
        android:fromYDelta="float"
        android:toYDelta="float" />
    <rotate
        android:fromDegrees="float"
        android:toDegrees="float"
        android:pivotX="float"
        android:pivotY="float" />
    <set>
        ...
    </set>
</set>
```

```java
ImageView spaceshipImage = (ImageView) findViewById(R.id.spaceshipImage);
Animation hyperspaceJumpAnimation = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
spaceshipImage.startAnimation(hyperspaceJumpAnimation);
```

### 视图动画注意事项

补间动画执行之后并未改变View的真实布局属性值。切记这一点，譬如我们在Activity中有一个Button在屏幕上方，我们设置了平移动画移动到屏幕下方然后保持动画最后执行状态呆在屏幕下方，这时如果点击屏幕下方动画执行之后的Button是没有任何反应的，而点击原来屏幕上方没有Button的地方却响应的是点击Button的事件。至于为什么，会在[属性动画](http://www.in-droid.com/2016/03/31/Android%E5%B1%9E%E6%80%A7%E5%8A%A8%E7%94%BB/)中介绍。

### 视图动画Interpolator插值器详解

#### 插值器简介

介绍补间动画插值器之前我们先来看一幅图，如下：

![补间动画插值器](http://7q5ctm.com1.z0.glb.clouddn.com/%E8%A7%86%E5%9B%BE%E5%8A%A8%E7%94%BB-2.png)

可以看见其实各种插值器都是实现了Interpolator接口而已，同时可以看见系统提供了许多已经实现OK的插值器，具体如下：

|java类|xml id值|描述|
|-|-|-|
|AccelerateDecelerateInterpolator|@android:anim/accelerate_decelerate_interpolator|动画始末速率较慢，中间加速
|AccelerateInterpolator|@android:anim/accelerate_interpolator|动画开始速率较慢，之后慢慢加速
|AnticipateInterpolator|@android:anim/anticipate_interpolator|开始的时候从后向前甩
|AnticipateOvershootInterpolator|@android:anim/anticipate_overshoot_interpolator|类似上面AnticipateInterpolator
|BounceInterpolator|@android:anim/bounce_interpolator|动画结束时弹起
|CycleInterpolator|@android:anim/cycle_interpolator|循环播放速率改变为正弦曲线
|DecelerateInterpolator|@android:anim/decelerate_interpolator|动画开始快然后慢
|LinearInterpolator|@android:anim/linear_interpolator|动画匀速改变
|OvershootInterpolator|@android:anim/overshoot_interpolator|向前弹出一定值之后回到原来位置
|PathInterpolator	| |新增，定义路径坐标后按照路径坐标来跑。

#### 插值器使用方法

插值器的使用比较简答，如下：

```xml
<set android:interpolator="@android:anim/accelerate_interpolator">
    ...
</set>
```

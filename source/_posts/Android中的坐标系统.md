---
title: Android中的坐标系统
date: 2016-03-19 15:33:34
tags: Android
---

Android中的坐标系统分为两种：一种是相对整个屏幕而言的，我们称之为Android坐标系；另一种是相对父布局而言的，我们称之为视图坐标系。
<!--more-->

#### Android坐标系
系统提供了`getLocationOnScreen()`这样的方法来获取Android坐标系中点的位置，`getRawX()`，`getRawY()`同样是获取Android坐标系中的坐标。

#### 视图坐标系
通过`getX()`，`getY()`获取的坐标就是相对父布局的坐标。

#### Canvas坐标系 
Canvas坐标系指的是Canvas本身的坐标系，Canvas坐标系有且只有一个，且是唯一不变的，其坐标原点在View的左上角，从坐标原点向右为x轴的正半轴，从坐标原点向下为y轴的正半轴。

#### 绘图坐标系 
Canvas的drawXXX方法中传入的各种坐标指的都是绘图坐标系中的坐标，而非Canvas坐标系中的坐标。默认情况下，绘图坐标系与Canvas坐标系完全重合，即初始状况下，绘图坐标系的坐标原点也在View的左上角，从原点向右为x轴正半轴，从原点向下为y轴正半轴。但不同于Canvas坐标系，绘图坐标系并不是一成不变的，可以通过调用Canvas的translate方法平移坐标系，可以通过Canvas的rotate方法旋转坐标系，还可以通过Canvas的scale方法缩放坐标系，而且需要注意的是，translate、rotate、scale的操作都是基于当前绘图坐标系的，而不是基于Canvas坐标系，一旦通过以上方法对坐标系进行了操作之后，当前绘图坐标系就变化了，以后绘图都是基于更新的绘图坐标系了。也就是说，真正对我们绘图有用的是绘图坐标系而非Canvas坐标系。

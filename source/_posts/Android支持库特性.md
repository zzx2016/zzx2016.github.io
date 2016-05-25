---
title: Android支持库特性
date: 2016-05-23 10:47:07
tags:
- Android
- Support Library
---
[英文地址](https://developer.android.com/topic/libraries/support-library/features.html)

Android Support Library包中包含了很多可以被我们app使用的类库，每个类库支持特定的Android版本和一些特性。

本文将介绍Support Library的重要特性和支持的系统版本，来帮助开发者决定在app中导入哪种Support Library。通常，我们建议导入v4和v7的Support Library，因为他们支持了大部分的系统版本以及推荐UI的API。

为了使用这些Support Library，必须使用Android SDK installation来下载这些库文件。具体下载方法请自行Google，下载完库文件后还要在项目中引用，下面将详细介绍每个库的用途，以及导入方法。

<!--more-->

### v4 Support Library

v4包支持Android 1.6（API level 4）及以上，相对其他库，v4包提供的API更多，包括应用程序组件，用户界面，无障碍功能，数据处理，网络连接和编程工具。下面列举一下v4包中比较关键的类。

- 应用组件（App Components）
  - Fragment -封装了Fragment的UI和功能，使应用程序能够根据小屏设备和大屏设备进行布局调整
  - NotificationCompat -提供更丰富的Notification。注：主要是兼容
  - LocalBroadcastManager -支持应用内的注册和接收广播，而不是使用全局的广播

- UI（User Interface）
  - ViewPager -管理子view，支持用户滑动
  - PagerTitleStrip -非交互式标题条，可以添加为ViewPager的子view
  - PagerTabStrip -增加了对页面访问之间进行切换的导航部件，也可以与ViewPager一起使用
  - DrawerLayout -平时用的侧滑菜单控件
  - SlidingPaneLayout -多层显示控件，左侧显示摘要（可导航），右侧显示详情

- 无障碍（Accessibility）
  - ExploreByTouchHelper -工具类，帮助自定义view实现无障碍访问
  - AccessibilityEventCompat -兼容AccessibilityEvent
  - AccessibilityNodeInfoCompat -兼容AccessibilityNodeInfo
  - AccessibilityNodeProviderCompat -兼容AccessibilityNodeProvider
  - AccessibilityDelegateCompat -兼容View.AccessibilityDelegate

- 内容（Content）
  - Loader -支持异步加载数据，提供两个实现类CursorLoader和AsyncTaskLoader
  - FileProvider -不同程序之间的文件共享

在Gradle中引用：

```com.android.support:support-v4:23.3.0```

### Multidex Support Library

解决方法数超过65536的问题，实现dex分包。

在Gradle中引用：

```com.android.support:multidex:1.0.0```

### v7 Support Libraries

提供了很多支持Android 2.1 (API level 7)及以上的库。这些库提供了很多特性并且这些库是相互独立的。

#### v7 appcompat library

提供了对ActionBar的支持，同时还包括对material design的支持。

> 这个库依赖v4 Support Library。

下面列举一些v7 appcompat library中的关键类:

- ActionBar -提供了ActionBar的用户界面的实现
- AppCompatActivity -支持ActionBar的兼容Activity
- AppCompatDialog -支持AppCompat themed的兼容Dialog
- ShareActionProvider -支持ActionBar中标准的分享动作(邮件或者社交应用)

在Gradle中引用：

```com.android.support:appcompat-v7:23.3.0```

#### v7 cardview library

符合material design的卡片组件，广泛的在TV app中应用

在Gradle中引用：

```com.android.support:cardview-v7:23.3.0```

#### 未完待续...

#### v7 gridlayout library

#### v7 mediarouter library
#### v7 palette library
#### v7 recyclerview library
#### v7 preference library
### v8 Support Library
### v13 Support Library
### v14 Preference Support Library
### v17 Leanback Library
### v17 Preference Library for TV
### Annotations Support Library
### Design Support Library
### Custom Tabs Support Library
### Percent Support Library
### Recommendation Support Library for TV

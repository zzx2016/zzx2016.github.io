---
title: 导入ApiDemos的正确姿势
date: 2016-05-18 11:04:27
tags:
- Android
- ApiDemos
---

#### 准备

ApiDemos可谓是很好的学习Android开发的源码资料，本来想着跑下源码看看，但是在导入之后报了很多错误，试着Google了下，终于找到一篇英文原文，很好的解决了各种报错，英文好的可以看下[原文](http://apidemos22.blogspot.com/2015/08/how-to-import-and-generate-apk-from.html)，本文是根据原文从头到尾操作了一遍，特此记录分享一下。

<!--more-->

导入源码会用到以下资源：

1. ApiDemos源码 [下载地址](http://pan.baidu.com/s/1gfJKOnx)
2. Android Files [下载地址](http://pan.baidu.com/s/1c2iDno8)

ApiDemos的源码也可以在`android-sdk\samples\android-23\legacy\ApiDemos`目录下找到，上面提供的源码版本为API23

#### 正文

打开Android Studio直接选择导入源码即可，然后编译，会发现报了错：

![错误](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos1.png)

这个错误是因为xml目录下的文件必须以`.xml`作为后缀，手动给这个报错的文件加个后缀即可。重新编译后，发现错误更多了：

![错误](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos2.png)

这时候Android Files中的文件就派上用场了，首先将`PrintHelper.java`和`PrintHelperKitkat.java`文件拷贝到app这个包下，即

![错误](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos3.png)

重新编译之后发现只剩下mms这个错误了

![错误](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos4.png)

下面就要将Android Files下的mms作为项目的module导入了，右键app选择红框中的选项

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos11.png)

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos12.png)

点击上图的加号，导入module，添加完如2所示，多了个mms的module，最后将mms添加为app依赖的module

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos13.png)

重新编译之后发现仍然有错

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos6.png)

这个错误是因为变量中有以下划线命名的变量，手动改掉就行了，改掉之后再编译，握草，还报错

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos7.png)

这时需要在Manifest文件中添加相应内容：

1. 为manifest标签添加如下属性

  ```xml
  android:versionCode="1"
  android:versionName="1.0"
  xmlns:tools="http://schemas.android.com/tools"
  ```

2. 在`<uses-permission... >`标签下添加如下标签

  ```xml
  <uses-sdk tools:overrideLibrary="com.google.android" />
  ```

3. 最后在application标签中添加如下属性

  ```xml
  tools:replace="android:label,android:icon"
  ```

添加完之后是这个样子

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos9.png)

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos8.png)

![module](http://7q5ctm.com1.z0.glb.clouddn.com/ApiDemos10.png)

这时候再编译，终于没有错误了，下面就跑demo，看源码，好好云雨一番吧！

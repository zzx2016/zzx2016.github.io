---
title: Android术语
date: 2016-03-19 15:18:52
tags: Android
---

[原文地址](http://blog.qiji.tech/archives/1296)
#### 前言
接触了这么久的Android，发现有些术语的理解还是模模糊糊，所以今天就来理清一下这些概念。
<!--more-->
#### apk扩展名
apk是Android包的扩展名，一个Android包包含了与某个Android应用程序相关的所有文件，apk文件将AndroidManifest.xml文件、应用程序代码(dex文件)、资源文件和其他文件组成一个压缩包，一个项目只能打包压缩成一个apk文件。
#### API
Application Programming Interface，简称：API，又称为应用编程接口，就是软件系统不同组成部分衔接的约定。良好的接口设计可以降低系统各部分的相互依赖，提高组成单元的内聚性，降低组成单元间的耦合程度，从而提高系统的维护性和扩展性。（关于Android开发中API的详细解释请看[[Android Studio]API level](http://blog.qiji.tech/archives/2193)）
#### .dex扩展名
Android的程序被编译成.dex(Dalvik Executable)格式文件, 然后再进行打包生成可被直接安装的apk文件。
#### 应用程序(APP)
一个或多个Activity、服务、监听和Intent接收器的集合，一个应用程序有一个文件清单，并且打包成一个apk文件。
#### Action
对Intent发送器意图的描述，一个活动是一个指派给Intent的字符串值。活动字符串可以由Android定义，也可以由第三方开发者定义。例如，在网页URL中使用的android.intent.action.VIEW或者在用户应用程序中使用的 com.example.rumbler.SHAKE_PHONE来使电话震动。
#### ADB（ Android Debug Bridge ）
SDK自带的一个基于命令行的调试程序。它提供了设备浏览工具、设备上的拷贝工具和为调试转寄端口的功能。更多信息请参考附录三（Android的ADB工具使用）。
#### AIDL
(Android接口描述语言)：是一种接口描述语言; 编译器可以通过aidl文件生成一段代码，通过预先定义的接口达到两个进程内部通信进程的目的.
内容源
内容源是建立在类ContentProvider之上的用于处理指定格式的内容请求字符串，并返回指定格式的数据的类。关于内容源的使用信息请参考本书第7章内容。
#### Dalvik Android
虚拟机的名字,Dalvik虚拟机是一个只能解释执行dex文件的虚拟机，dex文件针对存储性能和内存管理进行了优化。 Dalvik虚拟机是基于寄存器的虚拟机，并且能够运行经过Dalvik自带的“dx”工具转换过的Java类。 虚拟机运行在兼容Posix的操作系统上，依赖于底层的功能(如线程和低级内存管理)。Dalvik的核心类库有意做得与Java标准版非常类似，但它明显更适合小型移动设备。
#### DDMS
调试监视服务(Dalvik Debug Monitor ServiceDalvik)是SDK自带的一个可视的调试工具。它提供了屏幕捕捉、日志存储和进程检测能力。
#### Drawable
编译过的可视化资源，可以用来做背景、标题或屏幕的其他部分。它被编译在android.graphics.drawable子类中。
#### 意图(Intent)
意图是一个Intent类，它包含很多描述调用者意图做什么的字段。调用者发送意图到Android意图处理器，意图处理器会遍历所有应用程序的意图过滤器来查找与该意图最匹配的Activity。意图字段包括渴望的动作、种类、数据、数据的MIME类型、一个处理类和其他约束。
#### 意图过滤器(intent-filter)
Activity和意图接收器(Receiver)在它们的文件清单中包含一个或多个过滤器，用来描述什么类型的意图或者信息是它们能处理或想接收的。一个意图过滤器列出了一系列要求，例如，意图或信息必须满足的数据类型、被请求的动作和URI的格式。 对于Activity，Android搜索意图和Activity过滤器匹配程度最高的Activity；对于消息，Android会将消息转发给所有匹配意图过滤器的接收器。
#### Intent接收器(Receiver)
一个监听是由Context.broadcastIntent()发出的信息广播的类,详细信息请参考本书第9章。
#### JNI
JNI是Java Native Interface的缩写，中文为JAVA本地调用。从Java1.1开始，Java Native Interface(JNI)标准成为java平台的一部分，它允许Java代码和其他语言写的代码进行交互。JNI一开始是为了本地已编译语言，尤其是C和C++而设计的，但是它并不妨碍你使用其他语言，只要调用约定受支持就可以了。
#### Native 代码
Native代码主要是C或者C++的。代码编写者可以使用JNI从Java的程序中调用Native代码。
#### NDK
Native Development Kit,NDK提供了一系列的工具，帮助开发者快速开发C（或C++）的动态库，并能自动将so和java应用一起打包成apk。
#### 布局资源
一个描述Activity屏幕布局的XML文件。
#### 文件清单
应用程序中的一个XML文件，用于描述包中多个Activity、Intent过滤器、服务和其他内容。可以打开AndroidManifest.xml查看其包含的内容。
#### 资源
用户提供的XML、位图或其他文件，构建程序时会导入进来，稍后会被代码加载，Android支持多种类型的资源，请参考Resources中的详细描述，程序定义的资源文件应当保存在res/ 子目录下。
#### 服务(Service)
运行在后台执行多种固定任务的类，如播放音乐或检测网络活动。
#### 主题(Theme)
一系列定义多种默认显示设置的参数(文字大小、背景颜色等)。Android在R.style中提供了几个标准的主题(以”Theme_”开头)。
#### URIs
Android使用URI字符串请求数据(如通信录列表)和动作(如在浏览器中打开网页)。URI字符串可以具有不同的格式。所有请求数据的URI必须以“content://” 开头。有效的动作URI字符串会被设备上的适当的程序处理，例如，以“ http://” 开头的URI字符串会被浏览器处理。

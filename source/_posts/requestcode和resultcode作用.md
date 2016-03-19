---
title: requestcode和resultcode作用
date: 2016-03-19 15:32:14
tags: Android
---

requestCode请求码：`startActivityForResult()`的参数。

resultCode结果码：`setResut()`的参数。
<!--more-->
#### requestCode 请求码作用：
使用`startActivityForResult(Intent intent, int requestCode)`方法打开新的Activity，我们需要为`startActivityForResult()`方法传入一个请求码(第二个参数)。请求码的值是根据业务需要由自已设定，用于标识请求来源。例如：一个Activity有两个按钮，点击这两个按钮都会打开同一个新Activity，不管是哪个按钮打开新Activity，当这个新Activity关闭后，系统都会调用前面Activity的`onActivityResult(int requestCode, int resultCode, Intent data)`方法。在`onActivityResult()`方法如果需要知道新Activity是由那个按钮打开的，并且要做出相应的业务处理，就可以用`requestCode`来区分。

#### resultCode 结果码作用：
在一个Activity中，可能会使用`startActivityForResult()`方法打开多个不同的新Activity处理不同的业务，当这些新Activity关闭后，系统都会调用前面Activity的`onActivityResult(int requestCode, int resultCode, Intent data)`方法。为了知道返回的数据来自于哪个新Activity，在`onActivityResult()`方法中可以根据`resultCode`来区分。

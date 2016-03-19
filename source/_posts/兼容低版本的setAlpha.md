---
title: 兼容低版本的setAlpha
date: 2016-03-19 15:03:50
tags: Android
---
setAlpha方法在API 11之上才开始支持。
<!--more-->

```java
/**
* 兼容低版本
*
* @param view
* @param alpha
*/
@SuppressLint("NewApi")
public static void setAlpha(View view, float alpha) {
    if (Build.VERSION.SDK_INT < 11) {
        final AlphaAnimation animation = new AlphaAnimation(alpha, alpha);
        animation.setDuration(0);
        animation.setFillAfter(true);
        view.startAnimation(animation);
    } else {
        view.setAlpha(alpha);
    }
}
```

---
title: 禁止ViewPager滑动
date: 2016-03-19 14:54:37
tags: Android
---
项目实际需求中可能会有设置ViewPager禁止滑动的需求，此时需要重写ViewPager处理触屏事件
<!--more-->

```Java
public classCustomViewPager extends ViewPager {
    private booleanisPagingEnabled=true;
    publicCustomViewPager(Context context) {
        super(context);
    }
    publicCustomViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
    }
    @Override
    public booleanonTouchEvent(MotionEvent event) {
        return this.isPagingEnabled&&super.onTouchEvent(event);
    }
    @Override
    public booleanonInterceptTouchEvent(MotionEvent event) {
        return this.isPagingEnabled&&super.onInterceptTouchEvent(event);
    }
    public voidsetPagingEnabled(booleanb) {
        this.isPagingEnabled= b;
    }
}
```

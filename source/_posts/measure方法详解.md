---
title: measure方法详解
date: 2016-03-19 15:26:07
tags: Android
---

[原文地址](http://blog.csdn.net/jjwwmlp456/article/details/43964785)

measure方法用来测量控件的大小，此方法不能重载，系统提供onMeasure方法来供我们重写。
<!--more-->

#### 在View.java中的定义：
```Java
public final void measure(int widthMeasureSpec,int heightMeasureSpec){
    ... 
    onMeasure();
    ...
}

protected void onMeasure(int widthMeasureSpec,int heightMeasureSpec) {
    setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
}

public static int getDefaultSize(int size, int measureSpec) {  
    int result = size;  
    int specMode = MeasureSpec.getMode(measureSpec);  
    int specSize = MeasureSpec.getSize(measureSpec);  
    switch (specMode) {  
        case MeasureSpec.UNSPECIFIED: //未指定  
            result = size;  
            break;  
        case MeasureSpec.AT_MOST: //至多  
        case MeasureSpec.EXACTLY: //精确  
            result = specSize;  
            break;  
    } 
    return result;  
} 
```

#### 在ViewGroup中：
```Java
protected void measureChildren(int widthMeasureSpec, int heightMeasureSpec) {  
     final int size = mChildrenCount;  
     final View[] children = mChildren;  
     for (int i = 0; i < size; ++i) {  
         final View child = children[i];  
         if ((child.mViewFlags & VISIBILITY_MASK) != GONE) {  
             measureChild(child, widthMeasureSpec, heightMeasureSpec);  
         }  
     }  
 }  

protected void measureChild(View child, int parentWidthMeasureSpec, int parentHeightMeasureSpec) {  
    final LayoutParams lp = child.getLayoutParams();  
    final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec, mPaddingLeft + mPaddingRight, lp.width);  
    final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec, mPaddingTop + mPaddingBottom, lp.height);  
    child.measure(childWidthMeasureSpec, childHeightMeasureSpec);  
}

protected void measureChildWithMargins(View child, int parentWidthMeasureSpec, int widthUsed, int parentHeightMeasureSpec, int heightUsed) {  
    final MarginLayoutParams lp = (MarginLayoutParams) child.getLayoutParams();  
    final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec, mPaddingLeft + mPaddingRight + lp.leftMargin + lp.rightMargin + widthUsed, lp.width);  
    final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec, mPaddingTop + mPaddingBottom + lp.topMargin + lp.bottomMargin + heightUsed, lp.height);  
    child.measure(childWidthMeasureSpec, childHeightMeasureSpec);  
} 
```

#### 说明：
1. measure是final修饰的方法，不可被重写。
    在外部调用时，直接调用view.measure(int wSpec, int hSpec)。
    measure中调用了onMeasure。
    自定义view时，重写onMeasure即可。

2. MeasureSpec 这是一个含mode和size的结合体，不需要我们来具体的关心。当在测量时，可以调用MeasureSpec.getSize|getMode 得到相应的size和mode。然后使用MeasureSpec.makeMeasureSpec(size,mode); 来创建MeasureSpec对象。那么mode是怎么来的呢？是根据使用该自定义view时的layoutWith|height参数决定的，所以不能自己随便new一个。而size可以自己指定，也可以直接使用 measureSpec.getSize。

3. 如果是一个View，重写onMeasure时要注意：如果在使用自定义view时，用了wrap_content。那么在onMeasure中就要调用setMeasuredDimension，来指定view的宽高。如果使用的fill_parent或者一个具体的dp值。那么直接使用super.onMeasure即可。

4. 如果是一个ViewGroup，重写onMeasure时要注意：首先，结合上面两条，来测量自身的宽高。然后，需要测量子View的宽高。测量子view的方式有：
    - getChildAt(int index).可以拿到index上的子view。通过getChildCount得到子view的数目，再循环遍历出子view。接着，subView.measure(int wSpec, int hSpec); //使用子view自身的测量方法
    - 或者调用viewGroup的测量子view的方法：
       //某一个子view，多宽，多高, 内部加上了viewGroup的padding值
       measureChild(subView, int wSpec, int hSpec); 
       //所有子view 都是 多宽，多高, 内部调用了measureChild方法
       measureChildren(int wSpec, int hSpec);
       //某一个子view，多宽，多高, 内部加上了viewGroup的padding值、margin值和传入的宽高wUsed、hUsed  
       measureChildWithMargins(subView, intwSpec, int wUsed, int hSpec, int hUsed);

---
title: Android Transition Animation介绍
date: 2016-04-28 16:19:21
tags:
- Android
- 动画
---

### 什么是Transition

安卓5.0中Activity和Fragment 变换是建立在名叫Transitions的安卓新特性之上的。这个诞生于4.4的transition框架为在不同的UI状态之间产生动画效果提供了非常方便的API。该框架主要基于两个概念：场景（scenes）和变换（transitions）。场景（scenes）定义了当前的UI状态，变换（transitions）则定义了在不同场景之间动画变化的过程。虽然transition翻译为变换似乎很确切，但是总觉得还是没有直接使用transition直观，为了更好的理解下面个别地方直接用transition代表变换。

当一个场景改变的时候，transition主要负责：

1. 捕捉每个View在开始场景和结束场景时的状态。
2. 根据两个场景（开始和结束）之间的区别创建一个Animator。

<!--more-->

考虑这样一个例子，当用户点击屏幕，让activity中的view逐渐消失。使用安卓的transition框架，我们只需几行代码就可完成，如下：

```java
public class ExampleActivity extends Activity implements View.OnClickListener {
    private ViewGroup mRootView;
    private View mRedBox, mGreenBox, mBlueBox, mBlackBox;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mRootView = (ViewGroup) findViewById(R.id.layout_root_view);
        mRootView.setOnClickListener(this);
        mRedBox = findViewById(R.id.red_box);
        mGreenBox = findViewById(R.id.green_box);
        mBlueBox = findViewById(R.id.blue_box);
        mBlackBox = findViewById(R.id.black_box);
    }

    @Override
    public void onClick(View v) {
        TransitionManager.beginDelayedTransition(mRootView, new Fade());
        toggleVisibility(mRedBox, mGreenBox, mBlueBox, mBlackBox);
    }

    private static void toggleVisibility(View... views) {
        for (View view : views) {
            boolean isVisible = view.getVisibility() == View.VISIBLE;
            view.setVisibility(isVisible ? View.INVISIBLE : View.VISIBLE);
        }
    }
}
```

为了更好的理解幕后发生的事情，让我们来一步一步的分析，假设最开始每个view都是可见的：

1. 当点击事件发生之后调用TransitionManager的beginDelayedTransition()方法，并且传递了mRootView和一个Fade对象最为参数。之后，framework会立即调用transition类的captureStartValues()方法为每个view保存其当前的可见状态(visibility)。
2. 当beginDelayedTransition返回之后，在上面的代码中将每个view设置为不可见。
3. 在接下来的显示中framework会调用transition类的captureEndValues()方法，记录每个view最新的可见状态。
4. 接着，framework调用transition的createAnimator()方法。transition会分析每个view的开始和结束时的数据发现view在开始时是可见的，结束时是不可见的。Fade（transition的子类）会利用这些信息创建一个用于把view的alpha属性变为0的AnimatorSet，并且将此AnimatorSet对象返回。
5. framework会运行返回的Animator，导致所有的View都渐渐消失。

> 编者注：读者可以在这里回想假如不使用transition框架，我们自己使用属性动画（Animator）来实现是不是复杂很多，其实transition框架的作用就是封装了属性动画的操作。

这个简单的例子强调了transition框架的两个主要优点。第一、Transitions抽象和封装了属性动画，Animator的概念对开发者来说是透明的，因此它极大的精简了代码量。开发者所做的所有事情只是改变一下view前后的状态数据，Transition就会自动的根据状态的区别去生成动画效果。第二、不同场景之间变换的动画效果可以简单的通过使用不同的Transition类来改变，本例中用的是Fade。

![效果图](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-9.gif)

实现上图中的那两个不同的动画效果可以将Fade替换成Slide或者Explode即可。在接下来的文章中你将会发现，这些优点将使得我们只用少量代码就可以创建复杂的Activity 和Fragment切换动画。在接下来的小节中，将看到是如何使用Lollipop的Activity 和Fragment transition API来实现这种变换的。

### Android Transition应用场景

Android Transition框架主要运用在以下三种情况：

1. activity之间或者Fragment之间的过渡动画。
2. activity之间或者Fragment之间的共享元素（又叫hero view）的过渡动画。
3. activity中布局元素的过渡动画。

其实1和2都是用于activity（或者）切换的时候。而且1和2是可以同时使用的。ps ：虽然overridePendingTransition也可以实现activity的切换，但是overridePendingTransition 没有共享元素的切换效果。而第三种其实是最简单的，主要调用几个已经定义好的动画类就可以了。

#### activity之间的过度动画

##### exitTransition和enterTransition

![图1](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-1.png)

你可以在xml中定义这种动画

res/transition/activity_explode.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<transitionSet xmlns:android="http://schemas.android.com/apk/res/android">
    <explode android:duration="2000"/>
</transitionSet>
```

res/values/style.xml

```xml
<item name="android:windowEnterTransition">@transition/activity_explode.xml</item>
```

也可以通过代码的方式：

MainActivity.java

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    setupWindowAnimations();
}

private void setupWindowAnimations() {
    Explode explode = new Explode();
    explode.setDuration(2000);
    getWindow().setExitTransition(explode);
}
```

DetailActivity.java

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    setupWindowAnimations();
}

private void setupWindowAnimations() {
    Explode explode = new Explode();
    explode.setDuration(2000);
    getWindow().setEnterTransition(explode);
}
```

不管是代码还是xml的方式都会得到如下结果：

![图2](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-2.gif)

一步步解释上面的过程:

1. Activity A调用Activity B
2. Transition框架发现A的Exit Transition（这里是explode效果的过渡动画）将其应用到所有的可见view。
3. Transition框架发现B的Enter Transition (explode)，将其应用到所有的可见view。
4. 点击返回的时候Transition 框架分别执行各自Enter与Exit的反向动画（因为它没有找到returnTransition和reenterTransition）。

##### ReturnTransition和ReenterTransition

这两个方法定义A的exit动画和B的enter动画的相反方向的动画，即A的进入与B的离开。我们就成为返回动画吧。

![图3](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-3.png)

在本例中，我们这样：

MainActivity.java

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    setupWindowAnimations();
}

private void setupWindowAnimations() {
    Explode explode = new Explode();
    explode.setDuration(2000);
    getWindow().setExitTransition(explode);

    Fade fade = new Fade();
    fade.setDuration(2000);
    getWindow().setReenterTransition(fade);
}
```

DetailActivity.java

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    setupWindowAnimations();
}

private void setupWindowAnimations() {
    Explode explode = new Explode();
    expl.setDuration(2000);
    getWindow().setEnterTransition(explode);

    Fade fade = new Fade();
    fade.setDuration(2000);
    getWindow().setReturnTransition(fade);        
}
```

设置了ReenterTransition和ReturnTransition之后，返回的两个动画就不是默认的了，而是设置的渐变动画。

![图4](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-4.gif)

#### Activity间的共享元素

共 享元素过度动画的背后是通过过度动画将两个不同布局中的不同view关联起来。Transition框架知道用适当的动画向用户展示从一个view向另外 一个view过度。请记住：共享元素过度的过程中，view并没有真正从一个布局跑到另外一个布局，整个过程基本都是在后一个布局中完成的。

![图5](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-5.png)

可以看到，这里有两个id分别为smallSquare和bigSquare的view，但是他们有相同的transitionName。这样Transition就知道此时需要创建一个从一个view到另外一个view的动画。

MainActivity.java

```java
squareBlue.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Intent i = new Intent(MainActivity.this, DetailActivity2.class);

        View sharedView = squareBlue;
        String transitionName = getString(R.string.square_blue_name);

        ActivityOptions transitionActivityOptions = ActivityOptions.makeSceneTransitionAnimation(MainActivity.this, sharedView, transitionName);
        startActivity(i, transitionActivityOptions.toBundle());
    }
});
```

layout/main_activity.xml

```xml
<View
    android:layout_margin="10dp"
    android:id="@+id/square_blue"
    android:layout_width="50dp"
    android:background="@android:color/holo_blue_light"
    android:transitionName="@string/square_blue_name"
    android:layout_height="50dp"/>
```

layou/details_activity2.xml

```xml

<View
    android:layout_width="150dp"
    android:id="@+id/big_square_blue"
    android:layout_margin="10dp"
    android:transitionName="@string/square_blue_name"
    android:layout_centerInParent="true"
    android:background="@android:color/holo_blue_light"
    android:layout_height="150dp" />
```

这段代码将产生如下的动画效果：

![图6](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-6.gif)

可以看到Transition框架创建了一个让你感觉view在移动与变大的动画效果。

为了证明blue square这个view并没有真正移动，我们可以做一个这样的实现：将transitioName赋予Big Blue Square上面的TextView。

```xml
<TextView
    android:layout_width="wrap_content"
    android:text="Activity Detail 2"
    style="@style/Base.TextAppearance.AppCompat.Large"
    android:layout_centerHorizontal="true"
    android:transitionName="@string/square_blue_name"
    android:layout_above="@+id/big_square_blue"
    android:layout_height="wrap_content" />
```

运行之后你可以看到这次有相同的行为，但是作用在了一个不同的view上：

![图7](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-7.gif)

#### 布局元素的过渡动画

Transition框架还可以用于activity布局中view从一种状态到另一种状态的过渡动画。

如果你想知道关于过渡动画的更多知识，请观看这个视频[this video by Chet Hasse](https://www.youtube.com/watch?v=S3H7nJ4QaD8)。

本文尽量采用简单的例子

```java
TransitionManager.beginDelayedTransition(sceneRoot);
```

这行代码告诉framework，我们将要做一些UI上的变化，这些变化需要相应的动画效果。

对UI元素进行变更：

```java
setViewWidth(squareRed, 500);
setViewWidth(squareBlue, 500);
setViewWidth(squareGreen, 500);
setViewWidth(squareYellow, 500);
```

这会改变相应view的属性，让其变大。Transition框架将记录开始和结束的的值，然后创建一个过渡动画。

```java
squareGreen.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            TransitionManager.beginDelayedTransition(sceneRoot);
            setViewWidth(squareRed, 500);
            setViewWidth(squareBlue, 500);
            setViewWidth(squareGreen, 500);
            setViewWidth(squareYellow, 500);
        }
    });
}

private void setViewWidth(View view, int x) {
    ViewGroup.LayoutParams params = view.getLayoutParams();
    params.width = x;
    view.setLayoutParams(params);
}
```

![图8](http://7q5ctm.com1.z0.glb.clouddn.com/Android-Transition-8.gif)

### 参考链接
[原文地址1](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0322/2631.html)
[译文地址](https://github.com/lgvalle/Material-Animations)
[原文地址2](http://jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0113/2310.html)

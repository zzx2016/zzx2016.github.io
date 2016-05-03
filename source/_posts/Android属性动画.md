---
title: Android属性动画
date: 2016-04-15 16:43:41
tags:
- Android
- 动画
---

### 概述

上一篇已经介绍过[视图动画](http://zzx2016.github.io/2016/03/31/Android%E8%A7%86%E5%9B%BE%E5%8A%A8%E7%94%BB/)，只支持简单的缩放、平移、旋转、透明度基本的动画，且有一定的局限性。比如：你希望View有一个颜色的切换动画；你希望可以使用3D旋转动画；你希望当动画停止时，View的位置就是当前的位置；只能作用在View；这些需求视图动画都无法做到。这就是属性动画产生的原因。

<!--more-->

### 属性动画

动画从创建到执行总共可以分成以下两步

#### 1. 计算属性值

![计算属性值](http://7q5ctm.com1.z0.glb.clouddn.com/%E5%B1%9E%E6%80%A7%E5%8A%A8%E7%94%BB-1.png)

计算过程分为三步：

- 计算已完成动画分数。为了执行一个动画，你需要创建一个 ValueAnimator，并且指定目标对象属性的开始、结束值和持续时间。在调用 start 后的整个动画过程中， ValueAnimator 会根据已经完成的动画时间计算得到一个 0 到 1 之间的分数，代表该动画的已完成动画百分比。0 表示 0%，1 表示 100%。
- 计算插值（动画变化率）。 当 ValueAnimator 计算完已完成动画分数后，它会调用当前设置的 TimeInterpolator，去计算得到一个 interpolated（插值）分数，在计算过程中，已完成动画百分比会被加入到新的插值计算中。
- 计算属性值。当插值分数计算完成后，ValueAnimator 会根据插值分数调用合适的 TypeEvaluator 去计算运动中的属性值。

>以上分析引入了两个概念：已完成动画分数（elapsed fraction）、插值分数( interpolated fraction )。

#### 2. 设置值

将步骤1中计算的值设置给目标对象，即应用和刷新动画

### 动画类继承关系

![核心类图](http://7q5ctm.com1.z0.glb.clouddn.com/%E5%B1%9E%E6%80%A7%E5%8A%A8%E7%94%BB-2.png)

### 相关类介绍

#### ValueAnimator

属性动画中的主要的时序引擎，如动画时间，开始、结束属性值，相应时间属性值计算方法等。包含了所有计算动画值的核心函数。也包含了每一个动画时间上的细节，信息，一个动画是否重复，是否监听更新事件等，并且还可以设置自定义的计算类型。使用 ValueAnimator 实现动画需要手动更新：

```Java
ValueAnimator animation = ValueAnimator.ofFloat(0f, 1f);
animation.setDuration(1000);
animation.addUpdateListener(new AnimatorUpdateListener() {
    @Override
    public void onAnimationUpdate(ValueAnimator animation) {
        Log.i("update", ((Float) animation.getAnimatedValue()).toString());
    }
});
animation.setInterpolator(new CycleInterpolator(3));
animation.start();
```

#### ObjectAnimator

继承自ValueAnimator，允许你指定要进行动画的对象以及该对象的一个属性。该类会根据计算得到的新值自动更新属性。也就是说上 Property Animation 的两个步骤都实现了。大多数的情况，你使用ObjectAnimator就足够了，因为它使得目标对象动画值的处理过程变得简单，不用再向ValueAnimator那样自己写动画更新的逻辑。但ObjectAnimator有一定的限制，比如它需要目标对象的属性提供指定的处理方法，这个时候你需要根据自己的需求在ObjectAnimator和ValueAnimator中做个选择了，看哪种实现更简便。

ObjectAnimator的自动更新功能，依赖于属性身上的setter和getter方法，所以为了让ObjectAnimator能够正确的更新属性值，你必须遵从以下规范:

1. 该对象的属性必须有get和set方法（方法的格式必须是驼峰式），方法格式为 set()，因为 ObjectAnimator 会自动更新属性，它必须能够访问到属性的setter方法，比如属性名为foo,你就需要一个setFoo()方法，如果 setter 方法不存在，你有三种选择：
  - 添加 setter 方法
  - 使用包装类。通过该包装类通过一个有效的 setter 方法获取或者改变属性值的方法，然后应用于原始对象，比如 NOA 的AnimatorProxy。
  - 使用 ValueAnimator 代替

  >这3点的意思总结起来就是一定要有一个setter方法，让ObjectAnimator能够访问到

2. 注意，属性的getter方法和setter方法必须必须是相对应的，比如你构造了一个如下的ObjectAnimator
  ```Java
  ObjectAnimator.ofFloat(targetObject, "propName", 1f);
  ```
  那么targetObject中就要有setPropName(float)和getPropName(float)方法。

3. 根据动画的目标属性或者对象不同，你可能需要调用某一个 View 的invalidate方法，根据新的动画值去强制屏幕重绘该 View。可以在onAnimateonUpdate()回调方法中去做。比如，对一个 Drawable 的颜色属性进行动画，只有当对象重绘自身的时候，才会导致该属性的更新，（不像平移或者缩放那样是实时的）。一个 View 的所有 setter 属性方法，比如setAlpha()和setTranslationX()都可以适当的更新 View。因此你不需要在重绘的时候为这些方法传递新的值。更多关于 Listener 的信息，可以参考第四部分 Animation Listeners。

  >当你不希望向外暴露Setter方法的时候，或者希望获取到动画值统一做处理的话，亦或只需要一个简单的时序机制(拥有动画的各种值)的话，那么你可以选择使用ValueAnimator，它更简单。如果你就是希望更新动画，为了简便，可以使用ObjectAnimator，但自定义的属性必须有setter和getter方法，并且它们必须都是标准的驼峰式（确保内部能够调用），必须有结束值。你可以实现Animator.AnimatorListener接口根据自己的需求去更新 View。

#### AnimatorSet

提供组合动画能力的类。并可设置组中动画的时序关系，如同时播放、有序播放或延迟播放。Elevator会告诉属性动画系统如何计算一个属性的值，它们会从Animator类中获取时序数据，比如开始和结束值，并依据这些数据计算动画的属性值。

#### Interpolators

插值器：时间的函数，定义了动画的变化律。插值器只需实现一个方法：getInterpolation(float input),其作用就是把0到1的elapsed fraction变化映射到另一个interpolated fraction。 Interpolator接口的直接继承自TimeInterpolator，内部没有任何方法，而TimeInterpolator只有一个getInterpolation方法，所以所有的插值器只需实现getInterpolation方法即可。传入参数是正常执行动画的时间点，返回值是调用者真正想要它执行的时间点。传入参数是{0,1}，返回值一般也是{0,1}。{0,1}表示整段动画的过程。中间的 0.2、0.3 等小数表示在整个动画（原本是匀速的）中的位置，其实就是一个比值。如果返回值是负数，会沿着相反的方向执行。如果返回的是大于 1，会超出正方向执行。也就是说，动画可能在你指定的值上下波动，大多数情况下是在指定值的范围内。getInterpolation(float input)改变了默认动画的时间点elapsed fraction，根据时间点interpolated fraction得到的是与默认时间点不同的属性值，插值器的原理就是通过改变实际执行动画的时间点，提前或延迟默认动画的时间点来达到加速/减速的效果。动画插值器目前都只是对动画执行过程的时间进行修饰，并没有对轨迹进行修饰。

简单点解释这个方法，就是当要执行 input 的时间时，通过 Interpolator 计算返回另外一个时间点，让系统执行另外一个时间的动画效果。

#### Evaluators

Evaluators 告诉属性动画系统如何去计算一个属性值。它们通过Animator提供的动画的起始和结束值去计算一个动画的属性值。 属性系统提供了以下几种 Evaluators：IntEvaluator、FloatEvaluator、ArgbEvaluator这三个由系统提供，分别用于计算int，float，color型（十六进制）属性的计算器；TypeEvaluator是一个用于用户自定义计算器的接口，如果你的对象属性值类型，不是 int，float，或者 color 类型，你必须实现这个接口，去定义自己的数据类型。TypeEvaluator接口只有一个方法，就是evaluate()方法，它允许你使用的animator返回一个当前动画点的属性值。

### 为什么视图动画不会改变View的实际位置

先看下View的draw方法

![draw](http://7q5ctm.com1.z0.glb.clouddn.com/%E5%B1%9E%E6%80%A7%E5%8A%A8%E7%94%BB-3.png)

可以看到，在draw方法中，会获取当前view的animation，系统会根据动画的属性去配置Matrix，来改变view的绘制效果；属性动画改变的是view实际的属性值，这也就是为什么视图动画只是改变了view的绘制位置，而没有改变view的实际位置。

### 参考链接：
[链接1](http://a.codekk.com/detail/Android/lightSky/%E5%85%AC%E5%85%B1%E6%8A%80%E6%9C%AF%E7%82%B9%E4%B9%8B%20Android%20%E5%8A%A8%E7%94%BB%E5%9F%BA%E7%A1%80)

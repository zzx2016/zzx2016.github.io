---
title: 设计模式(结构型)之适配器模式(Adapter Pattern)
date: 2016-05-28 16:24:57
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

将一个类的接口转换成客户希望的另外一个接口。Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以在一起工作。即Adapter模式使得原本由于接口不兼容而不能一起工作的那些类可以在一起工作。

<!--more-->

### 核心

**概念：** 将一个接口转换成客户希望的另一个接口，使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)。适配器模式既可以作为类结构型模式，也可以作为对象结构型模式。

**分类：** 根据适配器类与适配者类的关系不同，适配器模式可分为对象适配器和类适配器两种。在对象适配器模式中，适配器与适配者之间是关联关系；在类适配器模式中，适配器与适配者之间是继承（或实现）关系。

**重点：**

- 对象适配器模式结构重要核心模块：

  1. 目标接口（Target）
  2. 客户所期待的接口。目标可以是具体的或抽象的类，也可以是接口。
  3. 需要适配的类（Adaptee）
  4. 需要适配的类或适配者类。
  5. 适配器（Adapter）

  通过包装一个需要适配的对象，把原接口转换成目标接口。

- 类适配器模式结构重要核心模块：

  1. 目标接口（Target）
  2. 客户所期待的接口。目标是接口。
  3. 需要适配的类（Adaptee）
  4. 需要适配的类或适配者类。
  5. 适配器（Adapter）
  6. 适配器类实现了抽象目标类接口Target，并继承了适配者类Adaptee。

  类适配器模式中如果适配者Adapter为最终(Final)类，也无法使用类适配器。在Java等面向对象编程语言中，大部分情况下我们使用的是对象适配器，类适配器较少使用。

### 使用场景

系统需要使用现有的类，而这些类的接口不符合系统的接口。

想要建立一个可以重用的类，用于与一些彼此之间没有太大关联的一些类，包括一些可能在将来引进的类一起工作。

两个类所做的事情相同或相似，但是具有不同接口的时候。

旧的系统开发的类已经实现了一些功能，但是客户端却只能以另外接口的形式访问，但我们不希望手动更改原有类的时候。

使用第三方组件，组件接口定义和自己定义的不同，不希望修改自己的接口，但是要使用第三方组件接口的功能。

### 程序猿实例

#### 对象适配器模式 的代码实例：

该例假设了原来有一个程序猿MonkeyAndroidAdaptee精通（只会）Android开发，后来有一天这个程序猿走了，写的代码还在，公司重新招来一个牛逼的全栈程序猿，但是这个牛逼的全栈程序猿首要任务就是维护精通原来离职程序猿的技能，准么办？全栈程序猿只能适配原来Android高手的代码，不能将自己其他的IOS、PHP等技能展示，所以MonkeyHoleTarget的codingAll技能需要适配为专攻的”Can code Android!”技能。这就是一个典型的对象适配器模式（对象在SkillAdapter适配器类中作为成员属性）。

```Java
//目标接口（Target）
class MonkeyHoleTarget {
    public void codingAll() {
        System.out.println("Can code all Language!");
    }
}
//需要适配的类（Adaptee）
class MonkeyAndroidAdaptee {
    public void codingAndroid() {
        System.out.println("Can code Android!");
    }
}
//适配器（Adapter）
class SkillAdapter extends MonkeyHoleTarget {
    private MonkeyAndroidAdaptee mMonkeyAndroidAdaptee;

    public SkillAdapter(MonkeyAndroidAdaptee target) {
        mMonkeyAndroidAdaptee = target;
    }

    public void codingAll() {
        mMonkeyAndroidAdaptee.codingAndroid();
    }
}

public class Main {
    public static void main(String[] args) {
        MonkeyHoleTarget monkeyHoleTarget = new SkillAdapter(new MonkeyAndroidAdaptee());
        monkeyHoleTarget.codingAll();
    }
}
```

#### 类适配器模式 的代码实例：

具体代码含义和上面类似。

```Java
//目标接口（Target）
interface MonkeyHoleTarget {
    void codingAll();
}
//需要适配的类（Adaptee）
class MonkeyAndroidAdaptee {
    public void codingAndroid() {
        System.out.println("Can code Android!");
    }
}
//适配器（Adapter）
class SkillAdapter implements MonkeyHoleTarget {
    private MonkeyAndroidAdaptee mMonkeyAndroidAdaptee;

    public SkillAdapter(MonkeyAndroidAdaptee mMonkeyAndroidAdaptee) {
        this.mMonkeyAndroidAdaptee = mMonkeyAndroidAdaptee;
    }

    @Override
    public void codingAll() {
        mMonkeyAndroidAdaptee.codingAndroid();
    }
}

public class Main {
    public static void main(String[] args) {
        MonkeyHoleTarget monkeyHoleTarget = new SkillAdapter(new MonkeyAndroidAdaptee());
        monkeyHoleTarget.codingAll();
    }
}
```

### 升级一把

适配器模式除了上面介绍的以外还有一个更加牛逼常用的变体（基因突变啊）！如下所述：

#### 缺省适配器模式(Default Adapter Pattern)

当不需要实现一个接口所提供的所有方法时，可先设计一个抽象类实现该接口，并为接口中每个方法提供一个默认实现（空方法），那么该抽象类的子类可以选择性地覆盖父类的某些方法来实现需求，它适用于不想使用一个接口中的所有方法的情况，又称为单接口适配器模式。

#### 缺省适配器模式重要核心模块：

1. ServiceInterface（适配者接口）

  它是一个接口，通常在该接口中声明了大量的方法。

2. AbstractServiceClass（缺省适配器类）

  它是缺省适配器模式的核心类，使用空方法的形式实现了在ServiceInterface接口中声明的方法。通常将它定义为抽象类，因为对它进行实例化没有任何意义。

3. ConcreteServiceClass（具体业务类）

  它是缺省适配器类的子类，在没有引入适配器之前，它需要实现适配者接口，因此需要实现在适配者接口中定义的所有方法，而对于一些无须使用的方法也不得不提供空实现。在有了缺省适配器之后，可以直接继承该适配器类，根据需要有选择性地覆盖在适配器类中定义的方法。

#### 代码实战一把

如下演示了缺省适配器的代码示例，技能太多，所以只关心自己要的技能，具体不说了。

```Java
//目标接口（Target）
interface MonkeyHoleTarget {
    void codingAll();
    void codingAndroid();
    void codingIOS();
    void codingPHP();
    void codingCLanguage();
    void codingScriptShell();
}
//默认适配器
class BaseAdapter implements MonkeyHoleTarget {
    @Override
    public void codingAll() {
    }

    @Override
    public void codingAndroid() {
    }

    @Override
    public void codingIOS() {
    }

    @Override
    public void codingPHP() {
    }

    @Override
    public void codingCLanguage() {
    }

    @Override
    public void codingScriptShell() {
    }
}

//需要适配的类（Adaptee）
class MonkeyAndroidAdaptee {
    public void codingAndroid() {
        System.out.println("Can code Android!");
    }
}
//适配器（Adapter）只关心自己感兴趣的部分方法
class SkillAdapter extends BaseAdapter {
    private MonkeyAndroidAdaptee mMonkeyAndroidAdaptee;

    public SkillAdapter(MonkeyAndroidAdaptee mMonkeyAndroidAdaptee) {
        this.mMonkeyAndroidAdaptee = mMonkeyAndroidAdaptee;
    }

    @Override
    public void codingAll() {
        mMonkeyAndroidAdaptee.codingAndroid();
    }
}

public class Main {
    public static void main(String[] args) {
        MonkeyHoleTarget monkeyHoleTarget = new SkillAdapter(new MonkeyAndroidAdaptee());
        monkeyHoleTarget.codingAll();
    }
}
```

### 总结一把

#### 适配器模式优点：

- 将目标类和适配者类解耦，通过引入一个适配器类来重用现有的适配者类，无须修改原有结构。
- 增加了类的透明性和复用性，将具体的业务实现过程封装在适配者类中，对于客户端类而言是透明的，而且提高了适配者的复用性，同一个适配者类可以在多个不同的系统中复用。
- 灵活性和扩展性都非常好，可以很方便地更换适配器。
- 一个对象适配器可以把多个不同的适配者适配到同一个目标。
- 可以适配一个适配者的子类，由于适配器和适配者之间是关联关系，根据“里氏代换原则”，适配者的子类也可通过该适配器进行适配。

#### 适配器模式缺点：

- 类适配器模式对于不支持多重类继承的语言，一次最多只能适配一个适配者类，不能同时适配多个适配者。
- 适配者类不能为最终类，如在Java中不能为final类。
- 在Java中，类适配器模式中的目标抽象类只能为接口，不能为类，其使用有一定的局限性。
- 对象适配器要在适配器中置换适配者类的某些方法比较麻烦。

---
title: 设计模式(行为型)之策略模式(Strategy Pattern)
date: 2016-05-28 17:20:50
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

使用策略模式可以定义一些独立的类来封装不同的算法，每一个类封装一种具体的算法，在这里，每一个封装算法的类我们都可以称之为一种策略(Strategy)，为了保证这些策略在使用时具有一致性，一般会提供一个抽象的策略类来做规则的定义，而每种算法则对应于一个具体策略类。策略模式的主要目的是将算法的定义与使用分开，将算法的定义放在专门的策略类中，每一个策略类封装了一种实现算法，使用算法的环境类针对抽象策略类进行编程，符合“依赖倒转原则”。在出现新的算法时，只需要增加一个新的实现了抽象策略类的具体策略类即可。

<!--more-->

### 核心

**概念：** 定义一系列算法类，将每一个算法封装起来，并让它们可以相互替换，策略模式让算法独立于使用它的客户而变化，也称为政策模式(Policy)。策略模式是一种对象行为型模式。

**策略模式结构重要核心模块：**

1. Context（环境类）

  环境类是使用算法的角色，它在解决某个问题（即实现某个方法）时可以采用多种策略。在环境类中维持一个对抽象策略类的引用实例，用于定义所采用的策略。

2. Strategy（抽象策略类）

  它为所支持的算法声明了抽象方法，是所有策略类的父类，它可以是抽象类或具体类，也可以是接口。环境类通过抽象策略类中声明的方法在运行时调用具体策略类中实现的算法。

3. ConcreteStrategy（具体策略类）

  它实现了在抽象策略类中声明的算法，在运行时，具体策略类将覆盖在环境类中定义的抽象策略类对象，使用一种具体的算法实现某个业务处理。

### 使用场景

一个系统需要动态地在几种算法中选择一种，那么可以将这些算法封装到一个个的具体算法类中，而这些具体算法类都是一个抽象算法类的子类。换言之，这些具体算法类均有统一的接口，根据“里氏代换原则”和面向对象的多态性，客户端可以选择使用任何一个具体算法类，并只需要维持一个数据类型是抽象算法类的对象。

一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重条件选择语句来实现。此时，使用策略模式，把这些行为转移到相应的具体策略类里面，就可以避免使用难以维护的多重条件选择语句。

不希望客户端知道复杂的、与算法相关的数据结构，在具体策略类中封装算法与相关的数据结构，可以提高算法的保密性与安全性。

### 程序猿实例

如下示例是一个简单的策略模式实例程序（模拟一个Android程序猿到底选择使用Java、Web还是C语言来开发这款应用），不做过多解释：

```Java
//Strategy（抽象策略类）
interface Strategy {
    void programLanguage();
}
//ConcreteStrategy（具体策略类）
class ConcreteStrategyJava implements Strategy {
    @Override
    public void programLanguage() {
        System.out.println("Use Java to Program this App!");
    }
}

class ConcreteStrategyWeb implements Strategy {
    @Override
    public void programLanguage() {
        System.out.println("Use Web to Program this App!");
    }
}

class ConcreteStrategyC implements Strategy {
    @Override
    public void programLanguage() {
        System.out.println("Use C Language to Program this App!");
    }
}
//Context（环境类）
class Context {
    private Strategy strategy;

    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }

    public void programLanguage() {
        strategy.programLanguage();
    }
}

//客户端
public class Main {
    public static void main(String[] args) {
        Context context = new Context();
        context.setStrategy(new ConcreteStrategyJava());
        context.programLanguage();

        context.setStrategy(new ConcreteStrategyC());
        context.programLanguage();

        context.setStrategy(new ConcreteStrategyWeb());
        context.programLanguage();
    }
}
```

### 总结一把

#### 策略模式优点：

- 策略模式完全符合“开闭原则”。
- 策略模式提供了管理相关算法族的办法。恰当使用继承可以把公共的代码移到抽象策略类中，从而避免重复的代码。
- 策略模式提供了一种可以替换继承关系的办法。如果不使用策略模式，那么使用算法的环境类就可能会有一些子类，每一个子类提供一种不同的算法。但是，这样一来算法的使用就和算法本身混在一起，不符合“单一职责原则”，而且使用继承无法实现算法或行为在程序运行时的动态切换。

使用策略模式可以避免多重条件选择语句。

#### 策略模式缺点：

- 客户端必须知道所有的策略类，并自行决定使用哪一个策略类。
- 策略模式将造成系统产生很多具体策略类，任何细小的变化都将导致系统要增加一个新的具体策略类。
- 无法同时在客户端使用多个策略类，也就是说，在使用策略模式时，客户端每次只能使用一个策略类，不支持使用一个策略类完成部分功能后再使用另一个策略类来完成剩余功能的情况。

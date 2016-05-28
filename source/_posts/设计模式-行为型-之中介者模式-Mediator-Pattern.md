---
title: 设计模式(行为型)之中介者模式(Mediator Pattern)
date: 2016-05-28 17:47:36
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

如果对象之间的联系呈现为网状结构，存在大量的多对多联系，在网状结构中，几乎每个对象都需要与其他对象发生相互作用，而这种相互作用表现为一个对象与另外一个对象的直接耦合，这将导致一个过度耦合的系统。如果在一个系统中对象之间存在多对多的相互关系，我们可以将对象之间的一些交互行为从各个对象中分离出来，并集中封装在一个中介者对象中，并由该中介者进行统一协调，这样对象之间多对多的复杂关系就转化为相对简单的一对多关系。通过引入中介者来简化对象之间的复杂交互，中介者模式是“迪米特法则”的一个典型应用。

<!--more-->

### 核心

**概念：** 用一个中介对象（中介者）来封装一系列的对象交互，中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。中介者模式又称为调停者模式，它是一种对象行为型模式。

**中介者模式结构重要核心模块：**

1. Mediator（抽象中介者）

  它定义一个接口，该接口用于与各同事对象之间进行通信。

2. ConcreteMediator（具体中介者）

  它是抽象中介者的子类，通过协调各个同事对象来实现协作行为，它维持了对各个同事对象的引用。

3. Colleague（抽象同事类）

  它定义各个同事类公有的方法，并声明了一些抽象方法来供子类实现，同时它维持了一个对抽象中介者类的引用，其子类可以通过该引用来与中介者通信。

4. ConcreteColleague（具体同事类）

  它是抽象同事类的子类；每一个同事对象在需要和其他同事对象通信时，先与中介者通信，通过中介者来间接完成与其他同事类的通信；在具体同事类中实现了在抽象同事类中声明的抽象方法。

### 使用场景

系统中对象之间存在复杂的引用关系，系统结构混乱且难以理解。

一个对象由于引用了其他很多对象并且直接和这些对象通信，导致难以复用该对象。

想通过一个中间类来封装多个类中的行为，而又不想生成太多的子类。可以通过引入中介者类来实现，在中介者中定义对象交互的公共行为，如果需要改变行为则可以增加新的具体中介者类。

### 程序猿实例

这里展示一个中介者模式的例子（模拟猎头与程序员的对话），严格遵守模式几大核心模板，不多解释：

```Java
//Colleague（抽象同事类）
abstract class Colleague {
    protected  String name;
    protected Mediator mediator;

    public Colleague(String name, Mediator mediator) {
        this.name = name;
        this.mediator = mediator;
    }
}
//Mediator（抽象中介者）
abstract class Mediator {
    public abstract void constact(String message, Colleague colleague);
}
//ConcreteColleague（具体同事类）
class ConcreteColleagueHR extends Colleague {
    public ConcreteColleagueHR(String name, Mediator mediator) {
        super(name, mediator);
    }

    public void constact(String message) {
        mediator.constact(message, this);
    }

    public void getMessage(String msg) {
        System.out.println("HR#"+name+"#:"+msg);
    }
}

class ConcreteColleagueAndroidDeveloper extends Colleague {
    public ConcreteColleagueAndroidDeveloper(String name, Mediator mediator) {
        super(name, mediator);
    }

    public void constact(String message) {
        mediator.constact(message, this);
    }

    public void getMessage(String msg) {
        System.out.println("Android Developer#"+name+"#:"+msg);
    }
}
//ConcreteMediator（具体中介者）
class ConcreteMediator extends Mediator {
    private ConcreteColleagueHR hr;
    private ConcreteColleagueAndroidDeveloper ad;

    public ConcreteColleagueHR getConcreteColleagueHR() {
        return hr;
    }

    public ConcreteColleagueAndroidDeveloper getConcreteColleagueAndroidDeveloper() {
        return ad;
    }

    public void setConcreteColleagueHR(ConcreteColleagueHR hr) {
        this.hr = hr;
    }

    public void setConcreteColleagueAndroidDeveloper(ConcreteColleagueAndroidDeveloper ad) {
        this.ad = ad;
    }

    @Override
    public void constact(String message, Colleague colleague) {
        if (colleague == hr) {
            ad.getMessage(message);
        }
        else {
            hr.getMessage(message);
        }
    }
}
//客户端
public class Main {
    public static void main(String[] args) {
        ConcreteMediator mediator = new ConcreteMediator();
        ConcreteColleagueHR hr = new ConcreteColleagueHR("Google招聘专员", mediator);
        ConcreteColleagueAndroidDeveloper ad = new ConcreteColleagueAndroidDeveloper("屌丝开发者", mediator);

        mediator.setConcreteColleagueHR(hr);
        mediator.setConcreteColleagueAndroidDeveloper(ad);

        hr.constact("Hi，你有意向来我们公司吗？");
        ad.constact("是Google开发Android吗？");
        hr.constact("yes!");
        ad.constact("我愿意！");
    }
}
```

### 总结一把

#### 中介者模式优点：

- 简化了对象之间的交互，它用中介者和同事的一对多交互代替了原来同事之间的多对多交互，一对多关系更容易理解、维护和扩展，将原本难以理解的网状结构转换成相对简单的星型结构。
- 可将各同事对象解耦。
- 可以减少子类生成，中介者将原本分布于多个对象间的行为集中在一起，改变这些行为只需生成新的中介者子类即可，这使各个同事类可被重用，无须对同事类进行扩展。

#### 中介者模式缺点：

- 在具体中介者类中包含了大量同事之间的交互细节，可能会导致具体中介者类非常复杂，使得系统难以维护。

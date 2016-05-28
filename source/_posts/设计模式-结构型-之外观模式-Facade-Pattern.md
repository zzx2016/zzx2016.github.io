---
title: 设计模式(结构型)之外观模式(Facade Pattern)
date: 2016-05-28 16:51:13
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

一个客户类需要和多个业务类交互，而这些业务类经常会作为整体出现，由于涉及到的类比较多，导致使用时代码较为复杂。外观模式通过引入一个新的外观类(Facade)来实现该功能，外观类为多个业务类的调用提供统一入口，简化了类与类之间的交互。如果没有外观类，那么每个客户类需要和多个业务类之间进行复杂的交互，系统的耦合度将很大。外观模式是迪米特法则的一种具体实现，通过引入一个新的外观角色可以降低原有系统的复杂度，同时降低客户类与子系统的耦合度。

<!--more-->

### 核心

**概念：** 为子系统中的一组接口提供一个统一的入口。外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。

**重点：** 外观模式结构重要核心模块：

1. 外观角色

  客户端可以调用它的方法，在外观角色中可以知道相关子系统的功能和责任；在正常情况下，它将所有从客户端发来的请求委派到相应的子系统去，传递给相应的子系统对象处理。

2. 子系统角色

  在软件系统中可以有一个或者多个子系统角色，每一个子系统可以不是一个单独的类，而是一个类的集合，它实现子系统的功能；每一个子系统都可以被客户端直接调用，或者被外观角色调用，它处理由外观类传过来的请求；子系统并不知道外观的存在，对于子系统而言，外观角色仅仅是另外一个客户端而已。

### 使用场景

需要访问一系列复杂的子系统时提供一个简单入口。

客户端程序与多个子系统之间存在很大的依赖性。引入外观类可以将子系统与客户端解耦，从而提高子系统的独立性和可移植性。

在层次化结构中，可以使用外观模式定义系统中每一层的入口，层与层之间不直接产生联系，而通过外观类建立联系，降低层之间的耦合度。

### 程序猿实例

这里还是以苦逼的程序猿为例来说明外观模式。一个Android开发者开发Android程序需要很多工具条件，电脑，软件，网络。如下实现：

```Java
//子系统角色
class Computer {
    public void thinkpad() {
        System.out.println("Computer is ThinkPad!");
    }
}

class EathNet {
    public void net() {
        System.out.println("Net is CMCC!");
    }
}

class DeveloperTools {
    public void tools() {
        System.out.println("Developer Tool is Android Studio!");
    }
}
//外观角色
class AndroidDeveloperEquipment {
    public void equipment() {
        new Computer().thinkpad();
        new EathNet().net();
        new DeveloperTools().tools();
    }
}

public class Main {
    public static void main(String[] args) {
        AndroidDeveloperEquipment dev = new AndroidDeveloperEquipment();
        dev.equipment();
    }
}
```

### 总结一把

#### 外观模式优点：

- 减少客户端所需处理的对象数目，使得子系统使用起来更加容易。
- 实现了子系统与客户端之间的松耦合关系。
- 一个子系统的修改对其他子系统没有任何影响，而且子系统内部变化也不会影响到外观对象。

#### 外观模式缺点：

- 不能很好地限制客户端直接使用子系统类，如果对客户端访问子系统类做太多的限制则减少了可变性和灵活性。
- 如果设计不当，增加新的子系统可能需要修改外观类的源代码，违背了开闭原则。

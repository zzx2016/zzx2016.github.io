---
title: 设计模式(行为型)之命令模式(Command Pattern)
date: 2016-05-28 17:24:37
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

在软件开发中，我们经常需要向某些对象发送请求（调用其中的某个或某些方法），但是并不知道请求的接收者是谁，也不知道被请求的操作是哪个，此时，我们特别希望能够以一种松耦合的方式来设计软件，使得请求发送者与请求接收者能够消除彼此之间的耦合，让对象之间的调用关系更加灵活，可以灵活地指定请求接收者以及被请求的操作。命令模式为此类问题提供了一个较为完美的解决方案。命令模式可以将请求发送者和接收者完全解耦，发送者与接收者之间没有直接引用关系，发送请求的对象只需要知道如何发送请求，而不必知道如何完成请求。

<!--more-->

### 核心

**概念：** 将一个请求封装为一个对象，从而让我们可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。命令模式是一种对象行为型模式，其别名为动作(Action)模式或事务(Transaction)模式。

**命令模式结构重要核心模块：**

1. Command（抽象命令类）

  抽象命令类一般是一个抽象类或接口，在其中声明了用于执行请求的execute()等方法，通过这些方法可以调用请求接收者的相关操作。

2. ConcreteCommand（具体命令类）

  具体命令类是抽象命令类的子类，实现了在抽象命令类中声明的方法，它对应具体的接收者对象，将接收者对象的动作绑定其中。在实现execute()方法时，将调用接收者对象的相关操作(Action)。

3. Invoker（调用者）

  调用者即请求发送者，它通过命令对象来执行请求。一个调用者并不需要在设计时确定其接收者，因此它只与抽象命令类之间存在关联关系。在程序运行时可以将一个具体命令对象注入其中，再调用具体命令对象的execute()方法，从而实现间接调用请求接收者的相关操作。

4. Receiver（接收者）

  接收者执行与请求相关的操作，它具体实现对请求的业务处理。

### 使用场景

系统需要将请求调用者和请求接收者解耦，使得调用者和接收者不直接交互。请求调用者无须知道接收者的存在，也无须知道接收者是谁，接收者也无须关心何时被调用。

系统需要在不同的时间指定请求和执行请求。一个命令对象和请求的初始调用者可以有不同的生命期，换言之，最初的请求发出者可能已经不在了，而命令对象本身仍然是活动的，可以通过该命令对象去调用请求接收者，而无须关心请求调用者的存在性，可以通过请求日志文件等机制来具体实现。

系统需要支持命令的撤销(Undo)操作和恢复(Redo)操作。

系统需要将一组操作组合在一起形成宏命令。

### 程序猿实例

这里展示一个命令模式最基本的简单例子，不多解释：

```Java
//Command（抽象命令类）
interface Command {
    void exec();
}
//ConcreteCommand（具体命令类）
class ConcreteCommand implements Command {
    @Override
    public void exec() {
        Receiver receiver = new Receiver();
        receiver.action();
    }
}
//Invoker（调用者）
class Invoker {
    private Command command;

    public Invoker(Command command) {
        this.command = command;
    }

    public void setCommand(Command command) {
        this.command = command;
    }

    public void calling() {
        if (command != null) {
            command.exec();
        }
    }
}
//Receiver（接收者）
class Receiver {
    public void action() {
        System.out.println("I'm bei Called!");
    }
}

//客户端
public class Main {
    public static void main(String[] args) {
        Invoker invoker = new Invoker(new ConcreteCommand());
        invoker.calling();
    }
}
```

### 总结一把

#### 命令模式优点：

- 降低系统的耦合度。
- 新的命令可以很容易地加入到系统中。由于增加新的具体命令类不会影响到其他类，因此增加新的具体命令类很容易，无须修改原有系统源代码，甚至客户类代码，满足“开闭原则”的要求。
- 可以比较容易地设计一个命令队列或宏命令（组合命令）。
- 为请求的撤销(Undo)和恢复(Redo)操作提供了一种设计和实现方案。

#### 命令模式缺点：

- 使用命令模式可能会导致某些系统有过多的具体命令类。因为针对每一个对请求接收者的调用操作都需要设计一个具体命令类，因此在某些系统中可能需要提供大量的具体命令类，这将影响命令模式的使用。

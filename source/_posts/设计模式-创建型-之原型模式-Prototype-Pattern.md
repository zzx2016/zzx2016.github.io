---
title: 设计模式(创建型)之原型模式(Prototype Pattern)
date: 2016-05-28 16:32:30
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

要理解原型原型模式必须先理解Java里的浅复制和深复制。有的地方，复制也叫做克隆。Java提供这两种克隆方式。 因为Java中的提供clone()方法来实现对象的克隆,所以Prototype模式实现一下子变得很简单。

<!--more-->

**浅克隆：** 被克隆对象的所有变量都含有与原来的对象相同的值，而它所有的对其他对象的引用都仍然指向原来的对象。换一种说法就是浅克隆仅仅克隆所考虑的对象，而不克隆它所引用的对象。

**深克隆：** 被克隆对象的所有变量都含有与原来的对象相同的值，但它所有的对其他对象的引用不再是原有的，而这是指向被复制过的新对象。换言之，深复制把要复制的对象的所有引用的对象都复制了一遍，这种叫做间接复制。

**克隆必须满足的条件：**

1. 对任何的对象obj，都有obj.clone() != obj，即克隆对象与原对象不是同一个对象。
2. 对任何的对象obj，都有obj.clone().getClass() == obj.getClass()，即克隆对象与原对象的类型是一样的。
3. 如果对象obj的equals()方法定义恰当的话，那么obj.clone().equals(obj)应当是成立的。

在java中实现clone()应该满足以上三个条件（前两个是必须的，第三个是推荐但不强制的）。

### 核心

**概念：** 使用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。原型模式是一种对象创建型模式。

**重点：** 原型模式结构重要核心模块：

1. 实现Cloneable接口

  在java语言有一个Cloneable接口，它的作用只有一个，就是在运行时通知虚拟机可以安全地在实现了此接口的类上使用clone方法。在java虚拟机中，只有实现了这个接口的类才可以被拷贝，否则在运行时会抛出CloneNotSupportedException异常。

2. 重写Object类中的clone方法

  Java中，所有类的父类都是Object类，Object类中有一个clone方法，作用是返回对象的一个拷贝，但是其作用域protected类型的，一般的类无法调用，因此，Prototype类需要将clone方法的作用域修改为public类型。

### 使用场景

创建新对象成本较大（如初始化需要占用较长的时间，占用太多的CPU资源或网络资源），新的对象可以通过原型模式对已有对象进行复制来获得，如果是相似对象，则可以对其成员变量稍作修改。

如果系统要保存对象的状态，而对象的状态变化很小，或者对象本身占用内存较少时，可以使用原型模式配合备忘录模式来实现。

需要避免使用分层次的工厂类来创建分层次的对象，并且类的实例对象只有一个或很少的几个组合状态，通过复制原型对象得到新实例可能比使用构造函数创建一个新实例更加方便。

### 程序猿实例

```java
//实现Cloneable接口,重写Object类中的clone方法
class MonkeyPrototype implements Cloneable {
    @Override
    protected MonkeyPrototype clone() throws CloneNotSupportedException {
        MonkeyPrototype monkeyPrototype = (MonkeyPrototype) super.clone();
        return monkeyPrototype;
    }
}
//原型模式实现类
class ConcreteMonkeyPrototype extends MonkeyPrototype {
    public void printHasCode() {
        System.out.println("ConcreteMonkeyPrototype hascode="+this.hashCode());
    }
}

public class Main {
    public static void main(String[] args) {
        ConcreteMonkeyPrototype type = new ConcreteMonkeyPrototype();
        type.printHasCode();
        for (int index=0; index<5; index++) {
            try {
                ConcreteMonkeyPrototype clone = (ConcreteMonkeyPrototype) type.clone();
                clone.printHasCode();
            } catch (CloneNotSupportedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**运行结果：**

ConcreteMonkeyPrototype hascode=356573597
ConcreteMonkeyPrototype hascode=1735600054
ConcreteMonkeyPrototype hascode=21685669
ConcreteMonkeyPrototype hascode=2133927002
ConcreteMonkeyPrototype hascode=1836019240
ConcreteMonkeyPrototype hascode=325040804

### 注意事项

使用原型模式复制对象不会调用类的构造方法。因为对象的复制是通过调用Object类的clone方法来完成的，它直接在内存中复制数据，因此不会调用到类的构造方法。不但构造方法中的代码不会执行，甚至连访问权限都对原型模式无效。还记得单例模式吗？单例模式中，只要将构造方法的访问权限设置为private型，就可以实现单例。但是clone方法直接无视构造方法的权限，所以，单例模式与原型模式是冲突的，在使用时要特别注意。

### 总结一把

#### 原型模式优点如下：

- 当创建新的对象实例较为复杂时，可以简化对象创建过程，通过复制一个已有实例可以提高实例的创建效率。
- 扩展性较好，原型模式中提供了抽象原型类，在客户端可以针对抽象原型类进行编程。
- 原型模式提供了简化的创建结构，工厂方法模式常常需要有一个与产品类等级结构相同的工厂等级结构，而原型模式就不需要这样，原型模式中产品的复制是通过封装在原型类中的克隆方法实现的，无须专门的工厂类来创建产品。
- 可以使用深克隆的方式保存对象的状态，使用原型模式将对象复制一份并将其状态保存起来，以便在需要的时候使用（如恢复到某一历史状态），可辅助实现撤销操作。

#### 原型模式缺点如下：

- 需要为每一个类配备一个克隆方法，而且该克隆方法位于一个类的内部，当对已有的类进行改造时违背“开闭原则”。
- 在实现深克隆时需要编写较为复杂的代码，而且当对象之间存在多重的嵌套引用时，为了实现深克隆，每一层对象对应的类都必须支持深克隆比较麻烦。

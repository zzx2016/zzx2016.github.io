---
title: 设计模式(结构型)之装饰者模式(Decorator Pattern)
date: 2016-05-28 16:47:50
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

装饰模式可以在不改变一个对象本身功能的基础上给对象增加额外的新行为。装饰模式是一种用于替代继承的技术，它通过一种无须定义子类的方式来给对象动态增加职责，使用对象之间的关联关系取代类之间的继承关系。在装饰模式中引入了装饰类，在装饰类中既可以调用待装饰的原有类的方法，还可以增加新的方法，以扩充原有类的功能。

<!--more-->

### 核心

**概念：** 动态地给一个对象增加一些额外的职责，就增加对象功能来说，装饰模式比生成子类实现更为灵活。装饰模式是一种对象结构型模式。

**重点：** 装饰者模式结构重要核心模块：

1. Component（抽象构件）

  它是具体构件和抽象装饰类的共同父类，声明了在具体构件中实现的业务方法，它的引入可以使客户端以一致的方式处理未被装饰的对象以及装饰之后的对象，实现客户端的透明操作。

2. ConcreteComponent（具体构件）

  它是抽象构件类的子类，用于定义具体的构件对象，实现了在抽象构件中声明的方法，装饰器可以给它增加额外的职责（方法）。

3. Decorator（抽象装饰类）

  它也是抽象构件类的子类，用于给具体构件增加职责，但是具体职责在其子类中实现。它维护一个指向抽象构件对象的引用，通过该引用可以调用装饰之前构件对象的方法，并通过其子类扩展该方法，以达到装饰的目的。

4. ConcreteDecorator（具体装饰类）

  它是抽象装饰类的子类，负责向构件添加新的职责。每一个具体装饰类都定义了一些新的行为，它可以调用在抽象装饰类中定义的方法，并可以增加新的方法用以扩充对象的行为。

**注意事项：** 装饰者模式使用时需要注意以下几个问题：

- 尽量保持装饰类的接口与被装饰类的接口相同，这样，对于客户端而言，无论是装饰之前的对象还是装饰之后的对象都可以一致对待。这也就是说，在可能的情况下，我们应该尽量使用透明装饰模式。
- 尽量保持具体装饰类是一个“轻”类，也就是说不要把太多的行为放在具体构件类中，我们可以通过装饰类对其进行扩展。
- 如果只有一个具体构件类，那么抽象装饰类可以作为该具体构件类的直接子类。

### 使用场景

在不影响其他对象的情况下，以动态、透明的方式给单个对象添加职责。

当不能采用继承的方式对系统进行扩展或者采用继承不利于系统扩展和维护时可以使用装饰模式。

不能采用继承的情况主要有系统中存在大量独立的扩展，为支持每一种扩展或者扩展之间的组合将产生大量的子类，使得子类数目呈爆炸性增长；类已定义为不能被继承（譬如final类）。

### 程序猿实例

这里还是以苦逼的程序猿为例来说明装饰者模式。假设有两个程序员他们原来只会各自的技能，现在需要让Android程序猿具备设计模式技能，那就给他装饰一下。如下实现：

```Java
//Component（抽象构件）
interface ProgramMonkey {
    void skills();
}
//ConcreteComponent（具体构件）
class AndroidProgramMonkey implements ProgramMonkey {
    @Override
    public void skills() {
        System.out.println("会写Android代码！");
    }
}
//ConcreteComponent（具体构件）
class PHPProgramMonkey implements ProgramMonkey {
    @Override
    public void skills() {
        System.out.println("会写PHP代码！");
    }
}
//Decorator（抽象装饰类）
class ProgramMonkeyDecorator implements ProgramMonkey {
    protected ProgramMonkey mProgramMonkey;

    public ProgramMonkeyDecorator(ProgramMonkey mProgramMonkey) {
        this.mProgramMonkey = mProgramMonkey;
    }

    public void skills() {
        mProgramMonkey.skills();
    }
}
//ConcreteDecorator（具体装饰类）
class PatternDecorator extends ProgramMonkeyDecorator {
    public PatternDecorator(ProgramMonkey mProgramMonkey) {
        super(mProgramMonkey);
    }

    @Override
    public void skills() {
        super.skills();
        System.out.println("会设计模式！");
    }
}

public class Main {
    public static void main(String[] args) {
        //有一个Android程序猿只会写Android代码
        ProgramMonkey programMonkey = new AndroidProgramMonkey();
        programMonkey.skills();
        //装饰一下他，装逼的技能，他竟然除了写Android还懂设计模式
        programMonkey = new PatternDecorator(programMonkey);
        programMonkey.skills();

        programMonkey = new PHPProgramMonkey();
        programMonkey.skills();
    }
}
```

### 总结一把

#### 装饰模式优点如下：

- 对于扩展一个对象的功能，装饰模式比继承更加灵活性，不会导致类的个数急剧增加。
- 可以通过一种动态的方式在运行时选择不同的具体装饰类，从而实现不同的行为。
- 可以对一个对象进行多次装饰，通过使用不同的具体装饰类以及这些装饰类的排列组合，可以创造出很多不同行为的组合，得到功能更为强大的对象。
- 具体构件类与具体装饰类可以独立变化，用户可以根据需要增加新的具体构件类和具体装饰类，原有类库代码无须改变，符合“开闭原则”。

#### 装饰模式缺点如下：

- 使用装饰模式进行系统设计时将产生很多小对象，这些对象的区别在于它们之间相互连接的方式有所不同，而不是它们的类或者属性值有所不同，大量小对象的产生势必会占用更多的系统资源，在一定程序上影响程序的性能。
- 装饰模式提供了一种比继承更加灵活机动的解决方案，但同时也意味着比继承更加易于出错，排错也很困难，对于多次装饰的对象，调试时寻找错误可能需要逐级排查，较为繁琐。

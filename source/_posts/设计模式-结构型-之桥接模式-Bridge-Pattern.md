---
title: 设计模式(结构型)之桥接模式(Bridge Pattern)
date: 2016-05-28 16:37:46
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

桥接模式是一种很实用的结构型设计模式，如果软件系统中某个类存在多个独立变化的维度，通过该模式可以将这多个维度分离出来，使他们可以独立扩展，让系统更加符合“单一职责原则”。与多层继承方案不同，它将多个独立变化的维度设计为多个独立的继承等级结构，并且在抽象层建立一个抽象关联，该关联关系类似一条连接多个独立继承结构的桥，故名桥接模式。桥接模式用一种巧妙的方式处理多层继承存在的问题，用抽象关联取代了传统的多层继承，将类之间的静态继承关系转换为动态的对象组合关系，使得系统更加灵活，并易于扩展，同时有效控制了系统中类的个数。

<!--more-->

### 核心

**概念：** 将抽象部分与它的实现部分分离，使它们都可以独立地变化。它是一种对象结构型模式，又称为柄体(Handle and Body)模式或接口(Interface)模式。

**重点：** 桥接模式结构重要核心模块：

1. Abstraction（抽象类）

  定义抽象类的接口，它一般是抽象类而不是接口，其中定义了一个Implementor（实现类接口）类型的对象并可以维护该对象，它与Implementor之间具有关联关系，它既可以包含抽象业务方法，也可以包含具体业务方法。

2. RefinedAbstraction（扩充抽象类）

  扩充由Abstraction定义的接口，通常情况下它不再是抽象类而是具体类，它实现了在Abstraction中声明的抽象业务方法，在RefinedAbstraction中可以调用在Implementor中定义的业务方法。

3. Implementor（实现类接口）

  定义实现类的接口，这个接口不一定要与Abstraction的接口完全一致，事实上这两个接口可以完全不同，一般而言，Implementor接口仅提供基本操作，而Abstraction定义的接口可能会做更多更复杂的操作。Implementor接口对这些基本操作进行了声明，而具体实现交给其子类。通过关联关系，在Abstraction中不仅拥有自己的方法，还可以调用到Implementor中定义的方法，使用关联关系来替代继承关系。

4. ConcreteImplementor（具体实现类）

  具体实现Implementor接口，在不同的ConcreteImplementor中提供基本操作的不同实现，在程序运行时，ConcreteImplementor对象将替换其父类对象，提供给抽象类具体的业务操作方法。

**特点：** 桥接模式中体现了“单一职责原则”、“开闭原则”、“合成复用原则”、“里氏代换原则”、“依赖倒转原则”等。

**技巧：** 在使用桥接模式时，首先应该识别出一个类所具有的多个独立变化的维度，将它们设计为多个独立的继承等级结构，为多个维度都提供抽象层，并建立抽象耦合。通常情况下，我们将具有多个独立变化维度的类的一些普通业务方法和与之关系最密切的维度设计为“抽象类”层次结构（抽象部分），而将另一个维度设计为“实现类”层次结构（实现部分）。

### 使用场景

如果一个系统需要在抽象化和具体化之间增加更多的灵活性，避免在多个层次之间建立静态的继承关系，通过桥接模式可以使它们在抽象层建立一个关联关系。

“抽象部分”和“实现部分”可以以继承的方式独立扩展而互不影响，在程序运行时可以动态将一个抽象化子类的对象和一个实现化子类的对象进行组合，即系统需要对抽象化角色和实现化角色进行动态耦合。

一个类存在两个（或多个）独立变化的维度，且这两个（或多个）维度都需要独立进行扩展。

对于那些不希望使用继承或因为多层继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。

### 程序猿实例

假设问题环境：

就拿程序猿编程来作为例子说明。程序猿既有移动开发攻城狮，也有服务器开发攻城狮，也有嵌入式开发攻城狮，他们可以使用Windows开发、也可以使用Linux开发，还可以使用MAC OS开发。这时你会发现对于程序猿来说他们有不同的类型，对于开发平台来说他们也有不同的类型。在软件系统中要适应两个方面（不同的程序猿和不的平台）的变化我们就需要动动脑袋了。

**先看最顺手的继承方式代码实现：**

看着下面代码你都会头疼，因为这才是二维的两种情况的组合，要是拓展就更加多代码和蛋疼了！

```Java
class Platform {
    public void program() {
        System.out.println("使用的平台！");
    }
}

class WindowsPlatform extends Platform {
    @Override
    public void program() {
        System.out.println("使用Windows平台！");
    }
}

class LinuxPlatform extends Platform {
    @Override
    public void program() {
        System.out.println("使用Linux平台！");
    }
}

class ServerWindowsPlatform extends WindowsPlatform {
    @Override
    public void program() {
        System.out.println("服务器攻城狮使用Windows平台！");
    }
}

class ServerLinuxPlatform extends LinuxPlatform {
    @Override
    public void program() {
        System.out.println("服务器攻城狮使用Linux平台！");
    }
}

class MobileWindowsPlatform extends WindowsPlatform {
    @Override
    public void program() {
        System.out.println("移动端攻城狮使用Windows平台！");
    }
}

class MobileLinuxPlatform extends LinuxPlatform {
    @Override
    public void program() {
        System.out.println("移动端攻城狮使用Linux平台！");
    }
}

public class Main {
    public static void main(String[] args) {
        ServerLinuxPlatform serverLinuxPlatform = new ServerLinuxPlatform();
        serverLinuxPlatform.program();
        ServerWindowsPlatform serverWindowsPlatform = new ServerWindowsPlatform();
        serverWindowsPlatform.program();
        MobileLinuxPlatform mobileLinuxPlatform = new MobileLinuxPlatform();
        mobileLinuxPlatform.program();
        MobileWindowsPlatform mobileWindowsPlatform = new MobileWindowsPlatform();
        mobileWindowsPlatform.program();
    }
}
```

**升个级吧**

上面代码可以发现，通过继承实现这种多维度变化类的数量太多，太乱，你拿前边的设计模式想想会发现它在遵循开放-封闭原则的同时，违背了类的单一职责原则，即一个类只有一个引起它变化的原因，而这里引起变化的原因却有两个；其次是重复代码会很多；再次是类的结构过于复杂，继承关系太多，难于维护，还有就是扩展性太差。

那么有没有好的办法，符合面向对象设计原则的方式呢？

当然有，就是使用桥接模式，如下展示：

```Java
abstract class Platform {
    protected Monkey mMonkey;

    public abstract void program();
}

abstract class Monkey {
    public abstract void type();
}

class WindowsPlatform extends Platform {
    public WindowsPlatform(Monkey monkey) {
        this.mMonkey = monkey;
    }

    @Override
    public void program() {
        mMonkey.type();
        System.out.println("使用Windows平台！");
    }
}

class LinuxPlatform extends Platform {
    public LinuxPlatform(Monkey monkey) {
        this.mMonkey = monkey;
    }

    @Override
    public void program() {
        mMonkey.type();
        System.out.println("使用Linux平台！");
    }
}

class ServerMonkey extends Monkey {
    @Override
    public void type() {
        System.out.print("服务器攻城狮");
    }
}

class MobileMonkey extends Monkey {
    @Override
    public void type() {
        System.out.print("移动端攻城狮");
    }
}

public class Main {
    public static void main(String[] args) {
        Monkey monkeyS = new ServerMonkey();
        Monkey monkeyM = new MobileMonkey();
        Platform platform = new WindowsPlatform(monkeyS);
        platform.program();

        platform = new WindowsPlatform(monkeyM);
        platform.program();

        platform = new LinuxPlatform(monkeyS);
        platform.program();

        platform = new LinuxPlatform(monkeyM);
        platform.program();
    }
}
```

如上实例通过对象的组合方式，使用桥接模式把两个角色之间的继承关系改为耦合关系，从而使这两者可以各自独立得变化，这特妹的就是桥接模式的真谛。但是你可能会说如上这样写增加了客户程序与平台和工程师的偶合关系。其实这种耦合是对象创建引起的，所以可以用创建型模式去解决。卧槽！！！这不就是设计模式真谛么？设计模式相互之间没有界限，没有完全的框架套用，需要灵活组建。

**继续带你装逼带你飞的升级一把**

玩完底端的二维度的变化以后咱们再来整点刺激的三维度，这下你就彻底明白为啥桥接模式比之前的继承好了，如下就是对现有程序猿使用平台的拓展，增加了所属公司。

```Java
//新加的维度
abstract class Company {
    protected Platform mPlatform;

    public abstract void work();
}
//原有代码
abstract class Platform {
    protected Monkey mMonkey;

    public abstract void program();
}

abstract class Monkey {
    public abstract void type();
}

class WindowsPlatform extends Platform {
    public WindowsPlatform(Monkey monkey) {
        this.mMonkey = monkey;
    }

    @Override
    public void program() {
        mMonkey.type();
        System.out.println("使用Windows平台！");
    }
}

class LinuxPlatform extends Platform {
    public LinuxPlatform(Monkey monkey) {
        this.mMonkey = monkey;
    }

    @Override
    public void program() {
        mMonkey.type();
        System.out.println("使用Linux平台！");
    }
}

class ServerMonkey extends Monkey {
    @Override
    public void type() {
        System.out.print("服务器攻城狮");
    }
}

class MobileMonkey extends Monkey {
    @Override
    public void type() {
        System.out.print("移动端攻城狮");
    }
}
//新加的维度实现代码
class GoogleCompany extends Company {
    public GoogleCompany(Platform platform) {
        this.mPlatform = platform;
    }

    @Override
    public void work() {
        System.out.print("Google公司的");
        this.mPlatform.program();
    }
}

class TaoBaoCompany extends Company {
    public TaoBaoCompany(Platform platform) {
        this.mPlatform = platform;
    }

    @Override
    public void work() {
        System.out.print("TaoBao公司的");
        this.mPlatform.program();
    }
}

public class Main {
    public static void main(String[] args) {
        Monkey monkeyS = new ServerMonkey();
        Monkey monkeyM = new MobileMonkey();
        Platform platform = new WindowsPlatform(monkeyS);
        platform.program();

        Company company = new GoogleCompany(platform);
        company.work();

        platform = new WindowsPlatform(monkeyM);
        platform.program();

        company = new TaoBaoCompany(platform);
        company.work();

        platform = new LinuxPlatform(monkeyS);
        platform.program();

        company = new GoogleCompany(platform);
        company.work();

        platform = new LinuxPlatform(monkeyM);
        platform.program();

        company = new TaoBaoCompany(platform);
        company.work();
    }
}
```

如上代码一弄你就明白啥是桥接模式和桥接模式的强大了吧！！！

### 总结一把

#### 桥接模式优点如下：

- 分离抽象接口及其实现部分。桥接模式使用“对象间的关联关系”解耦了抽象和实现之间固有的绑定关系，使得抽象和实现可以沿着各自的维度来变化。所谓抽象和实现沿着各自维度的变化，也就是说抽象和实现不再在同一个继承层次结构中，而是“子类化”它们，使它们各自都具有自己的子类，以便任何组合子类，从而获得多维度组合对象。
- 很多情况下桥接模式可以取代多层继承方案，多层继承方案违背了“单一职责原则”，复用性较差，且类的个数非常多，桥接模式是比多层继承方案更好的解决方法，它极大减少了子类的个数。
- 桥接模式提高了系统的可扩展性，在两个变化维度中任意扩展一个维度，都不需要修改原有系统，符合“开闭原则”。

#### 桥接模式缺点如下：

- 桥接模式的使用会增加系统的理解与设计难度，由于关联关系建立在抽象层，要求开发者一开始就针对抽象层进行设计与编程。
- 桥接模式要求正确识别出系统中两个独立变化的维度，因此其使用范围具有一定的局限性，如何正确识别两个独立维度也需要一定的经验积累。

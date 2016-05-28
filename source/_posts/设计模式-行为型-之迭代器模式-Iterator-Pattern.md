---
title: 设计模式(行为型)之迭代器模式(Iterator Pattern)
date: 2016-05-28 17:16:41
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

在软件构建过程中，集合对象内部结构常常变化各异。但对于这些集合对象，我们希望在不暴露其内部结构的同时，可以让外部客户代码透明地访问其中包含的元素;同时这种“透明遍历”也为“ 同一种算法在多种集合对象上进行操作”提供了可能。使用面向对象技术将这种遍历机制抽象为“迭代器对象”为“应对变化中的集合对象”提供了一种优雅的方法。

<!--more-->

### 核心

**概念：** 提供一种方法来访问聚合对象，而不用暴露这个对象的内部表示，其别名为游标(Cursor)。迭代器模式是一种对象行为型模式。

**迭代器模式结构重要核心模块：**

1. 迭代器角色（Iterator）

  迭代器角色负责定义访问和遍历元素的接口。

2. 具体迭代器角色（Concrete Iterator）

  具体迭代器角色要实现迭代器接口，并要记录遍历中的当前位置。

3. 容器角色（Container）

  容器角色负责提供创建具体迭代器角色的接口。

4. 具体容器角色（Concrete Container）

  具体容器角色实现创建具体迭代器角色的接口——这个具体迭代器角色于该容器的结构相关。

迭代器模式中应用了工厂方法模式，抽象迭代器对应于抽象产品角色，具体迭代器对应于具体产品角色，抽象聚合类对应于抽象工厂角色，具体聚合类对应于具体工厂角色。

### 使用场景

访问一个聚合对象的内容而无需暴露它的内部表示。

支持对聚合对象的多种遍历。

为遍历不同的聚合结构提供一个统一的接口(即, 支持多态迭代)。

迭代器模式是与集合共生共死的，一般来说，我们只要实现一个集合，就需要同时提供这个集合的迭代器，就像java中的Collection，List、Set、Map等，这些集合都有自己的迭代器。假如我们要实现一个这样的新的容器，当然也需要引入迭代器模式，给我们的容器实现一个迭代器。但是，由于容器与迭代器的关系太密切了，所以大多数语言在实现容器的时候都给提供了迭代器，并且这些语言提供的容器和迭代器在绝大多数情况下就可以满足我们的需要，所以现在需要我们自己去实践迭代器模式的场景还是比较少见的，我们只需要使用语言中已有的容器和迭代器就可以了。

### 程序猿实例

如下示例是一个简单的迭代器实例程序，不做过多解释：

```Java
import java.util.ArrayList;
import java.util.List;

//容器角色（Container）
interface Container {
    void add(Object obj);
    void remove(Object obj);
    Iterator createIterator();
}
//具体容器角色（Concrete Container）
class ConcreteContainer implements Container {
    private List<Object> list;

    public ConcreteContainer(List<Object> list) {
        this.list = list;
    }

    @Override
    public void add(Object obj) {
        list.add(obj);
    }

    @Override
    public void remove(Object obj) {
        list.remove(obj);
    }

    @Override
    public Iterator createIterator() {
        return new ConcreteIterator(list);
    }
}
//迭代器角色（Iterator）
interface Iterator {
    Object first();
    Object next();
    boolean hasNext();
    Object currentItem();
}
//具体迭代器角色（Concrete Iterator）
class ConcreteIterator implements Iterator {
    private List<Object> list;
    private int cursor;

    public ConcreteIterator(List<Object> list) {
        this.list = list;
    }

    @Override
    public Object first() {
        cursor = 0;
        return list.get(cursor);
    }

    @Override
    public Object next() {
        Object ret = null;
        if (hasNext()) {
            ret = list.get(cursor);
        }
        cursor++;
        return ret;
    }

    @Override
    public boolean hasNext() {
        return !(cursor == list.size());
    }

    @Override
    public Object currentItem() {
        return list.get(cursor);
    }
}
//客户端
public class Main {
    public static void main(String[] args) {
        List<Object> list = new ArrayList<Object>();
        list.add("Android");
        list.add("PHP");
        list.add("C Language");

        Container container = new ConcreteContainer(list);
        container.add("HardWare");

        Iterator iterator = container.createIterator();

        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

**升个级！使用内部类实现迭代器模式：**

上面的例子可以看到具体迭代器类和具体聚合类之间存在双重关系，其中一个关系为关联关系，在具体迭代器中需要维持一个对具体聚合对象的引用，该关联关系的目的是访问存储在聚合对象中的数据，以便迭代器能够对这些数据进行遍历操作。除了使用关联关系外，为了能够让迭代器可以访问到聚合对象中的数据，我们还可以将迭代器类设计为聚合类的内部类。如下代码示例就是对上面代码的改良版本。 其实无论使用哪种方式，客户端代码都是一样的。客户端用不着关心具体迭代器对象的创建细节，只需通过调用工厂方法createIterator()即可得到一个可用的迭代器对象，这也是使用工厂方法模式的好处，通过工厂来封装对象的创建过程，简化了客户端的调用。

```java
import java.util.ArrayList;
import java.util.List;

//容器角色（Container）
interface Container {
    void add(Object obj);
    void remove(Object obj);
    Iterator createIterator();
}
//具体容器角色（Concrete Container）
class ConcreteContainer implements Container {
    private List<Object> list;

    public ConcreteContainer(List<Object> list) {
        this.list = list;
    }

    @Override
    public void add(Object obj) {
        list.add(obj);
    }

    @Override
    public void remove(Object obj) {
        list.remove(obj);
    }

    @Override
    public Iterator createIterator() {
        return new ConcreteIterator();
    }

    //具体迭代器角色（Concrete Iterator）
    class ConcreteIterator implements Iterator {
        private int cursor;

        public ConcreteIterator() {
        }

        @Override
        public Object first() {
            cursor = 0;
            return list.get(cursor);
        }

        @Override
        public Object next() {
            Object ret = null;
            if (hasNext()) {
                ret = list.get(cursor);
            }
            cursor++;
            return ret;
        }

        @Override
        public boolean hasNext() {
            return !(cursor == list.size());
        }

        @Override
        public Object currentItem() {
            return list.get(cursor);
        }
    }
}
//迭代器角色（Iterator）
interface Iterator {
    Object first();
    Object next();
    boolean hasNext();
    Object currentItem();
}
//客户端
public class Main {
    public static void main(String[] args) {
        List<Object> list = new ArrayList<Object>();
        list.add("Android");
        list.add("PHP");
        list.add("C Language");

        Container container = new ConcreteContainer(list);
        container.add("HardWare");

        Iterator iterator = container.createIterator();

        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}
```

### 总结一把

#### 迭代器模式优点：

- 它支持以不同的方式遍历一个聚合对象，在同一个聚合对象上可以定义多种遍历方式。
- 迭代器简化了聚合类。由于引入了迭代器，在原有的聚合对象中不需要再自行提供数据遍历等方法，这样可以简化聚合类的设计。
- 在迭代器模式中，由于引入了抽象层，增加新的聚合类和迭代器类都很方便，无须修改原有代码，满足“开闭原则”的要求。

#### 迭代器模式缺点：

- 由于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。
- 抽象迭代器的设计难度较大，需要充分考虑到系统将来的扩展，例如JDK内置迭代器Iterator就无法实现逆向遍历，如果需要实现逆向遍历，只能通过其子类ListIterator等来实现，而ListIterator迭代器无法用于操作Set类型的聚合对象。在自定义迭代器时，创建一个考虑全面的抽象迭代器并不是件很容易的事情。

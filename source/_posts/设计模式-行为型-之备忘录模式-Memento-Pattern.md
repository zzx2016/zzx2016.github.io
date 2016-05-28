---
title: 设计模式(行为型)之备忘录模式(Memento Pattern)
date: 2016-05-28 17:50:12
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

备忘录模式提供了一种状态恢复的实现机制，使得用户可以方便地回到一个特定的历史步骤，当新的状态无效或者存在问题时，可以使用暂时存储起来的备忘录将状态复原，当前很多软件都提供了撤销(Undo)操作，其中就使用了备忘录模式。

<!--more-->

### 核心

**概念：** 在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样可以在以后将对象恢复到原先保存的状态。它是一种对象行为型模式，其别名为Token。

**备忘录模式结构重要核心模块：**

1. Originator（原发器）

  它是一个普通类，可以创建一个备忘录，并存储它的当前内部状态，也可以使用备忘录来恢复其内部状态，一般将需要保存内部状态的类设计为原发器。

2. Memento（备忘录)

  存储原发器的内部状态，根据原发器来决定保存哪些内部状态。备忘录的设计一般可以参考原发器的设计，根据实际需要确定备忘录类中的属性。需要注意的是，除了原发器本身与负责人类之外，备忘录对象不能直接供其他类使用，原发器的设计在不同的编程语言中实现机制会有所不同。

3. Caretaker（负责人）

  负责人又称为管理者，它负责保存备忘录，但是不能对备忘录的内容进行操作或检查。在负责人类中可以存储一个或多个备忘录对象，它只负责存储对象，而不能修改对象，也无须知道对象的实现细节。

**注意事项：**

由于在备忘录中存储的是原发器的中间状态，因此需要防止原发器以外的其他对象访问备忘录，特别是不允许其他对象来修改备忘录。所以在设计备忘录类时需要考虑其封装性，除了Originator类，不允许其他类来调用备忘录类Memento的构造函数与相关方法，如果不考虑封装性，允许其他类调用，将导致在备忘录中保存的历史状态发生改变，通过撤销操作所恢复的状态就不再是真实的历史状态，备忘录模式也就失去了本身的意义。

### 使用场景

保存一个对象在某一个时刻的全部状态或部分状态，这样以后需要时它能够恢复到先前的状态，实现撤销操作。

防止外界对象破坏一个对象历史状态的封装性。

### 程序猿实例

这里展示一个备忘录模式的例子（模拟一个Android机器人在二维平面地图上坐标点移动，可以回退一步），严格遵守模式几大核心模板，不多解释：

```Java
//Originator（原发器）
class RobotPosition {
    private String mRobotName;
    private int mCurXPos;
    private int mCurYpos;

    public RobotPosition(String mRobotName, int mCurXPos, int mCurYpos) {
        this.mRobotName = mRobotName;
        this.mCurXPos = mCurXPos;
        this.mCurYpos = mCurYpos;
    }

    public void setPos(int mCurXPos, int mCurYpos) {
        this.mCurXPos = mCurXPos;
        this.mCurYpos = mCurYpos;
    }

    public void drawScreen() {
        System.out.println("#RobotPosition#"+mRobotName+": "+mCurXPos+", "+mCurYpos);
    }

    public RobotPositionMemento save() {
        return new RobotPositionMemento(mRobotName, mCurXPos, mCurYpos);
    }

    public void restore(RobotPositionMemento memento) {
        this.mRobotName = memento.getmRobotName();
        this.mCurXPos = memento.getmCurXPos();
        this.mCurYpos = memento.getmCurYpos();
    }
}
//Memento（备忘录)
class RobotPositionMemento {
    private String mRobotName;
    private int mCurXPos;
    private int mCurYpos;

    public RobotPositionMemento(String mRobotName, int mCurXPos, int mCurYpos) {
        this.mRobotName = mRobotName;
        this.mCurXPos = mCurXPos;
        this.mCurYpos = mCurYpos;
    }

    public String getmRobotName() {
        return mRobotName;
    }

    public int getmCurXPos() {
        return mCurXPos;
    }

    public int getmCurYpos() {
        return mCurYpos;
    }
}
//Caretaker（负责人）
class RobotPositionCaretaker {
    private  RobotPositionMemento memento;

    public RobotPositionMemento getMemento() {
        return memento;
    }

    public void setMemento(RobotPositionMemento memento) {
        this.memento = memento;
    }
}
//客户端
public class Main {
    public static void main(String[] args) {
        RobotPositionCaretaker caretaker = new RobotPositionCaretaker();
        RobotPosition robot = new RobotPosition("Android", 0, 0);
        robot.drawScreen();
        caretaker.setMemento(robot.save());

        robot.setPos(0, 1);
        robot.drawScreen();
        caretaker.setMemento(robot.save());

        robot.setPos(5, 5);
        robot.drawScreen();

        //back on step
        robot.restore(caretaker.getMemento());
        robot.drawScreen();
        caretaker.setMemento(robot.save());
    }
}
```

**升级装逼：**

哪有机器人回退只能回退一步的说法呢？太弱了，再怎么地也得支持多步回退啊。对上面修改如下（支持多步回退，不做过多解释）：

```Java
import java.util.ArrayList;
import java.util.List;

//Originator（原发器）
class RobotPosition {
    private String mRobotName;
    private int mCurXPos;
    private int mCurYpos;

    public RobotPosition(String mRobotName, int mCurXPos, int mCurYpos) {
        this.mRobotName = mRobotName;
        this.mCurXPos = mCurXPos;
        this.mCurYpos = mCurYpos;
    }

    public void setPos(int mCurXPos, int mCurYpos) {
        this.mCurXPos = mCurXPos;
        this.mCurYpos = mCurYpos;
    }

    public void drawScreen() {
        System.out.println("#RobotPosition#"+mRobotName+": "+mCurXPos+", "+mCurYpos);
    }

    public RobotPositionMemento save() {
        return new RobotPositionMemento(mRobotName, mCurXPos, mCurYpos);
    }

    public void restore(RobotPositionMemento memento) {
        this.mRobotName = memento.getmRobotName();
        this.mCurXPos = memento.getmCurXPos();
        this.mCurYpos = memento.getmCurYpos();
    }
}
//Memento（备忘录)
class RobotPositionMemento {
    private String mRobotName;
    private int mCurXPos;
    private int mCurYpos;

    public RobotPositionMemento(String mRobotName, int mCurXPos, int mCurYpos) {
        this.mRobotName = mRobotName;
        this.mCurXPos = mCurXPos;
        this.mCurYpos = mCurYpos;
    }

    public String getmRobotName() {
        return mRobotName;
    }

    public int getmCurXPos() {
        return mCurXPos;
    }

    public int getmCurYpos() {
        return mCurYpos;
    }
}
//Caretaker（负责人）
class RobotPositionCaretaker {
    private List<RobotPositionMemento> mementoList = new ArrayList<>();

    public RobotPositionMemento getMemento(int setp) {
        return mementoList.get(setp);
    }

    public void setMemento(RobotPositionMemento memento) {
        this.mementoList.add(memento);
    }
}
//客户端
public class Main {
    public static void main(String[] args) {
        RobotPositionCaretaker caretaker = new RobotPositionCaretaker();
        RobotPosition robot = new RobotPosition("Android", 0, 0);
        robot.drawScreen();
        caretaker.setMemento(robot.save());

        robot.setPos(0, 1);
        robot.drawScreen();
        caretaker.setMemento(robot.save());

        robot.setPos(5, 5);
        robot.drawScreen();

        //back to step 1
        robot.restore(caretaker.getMemento(0));
        robot.drawScreen();
        caretaker.setMemento(robot.save());
    }
}
```

**运行结果：**

RobotPosition#Android: 0, 0
RobotPosition#Android: 0, 1
RobotPosition#Android: 5, 5
RobotPosition#Android: 0, 0

### 总结一把

#### 备忘录模式优点：

- 提供了一种状态恢复的实现机制，使得用户可以方便地回到一个特定的历史步骤，当新的状态无效或者存在问题时，可以使用暂时存储起来的备忘录将状态复原。
- 实现了对信息的封装，一个备忘录对象是一种原发器对象状态的表示，不会被其他代码所改动。备忘录保存了原发器的状态，采用列表、堆栈等集合来存储备忘录对象可以实现多次撤销操作。

#### 备忘录模式缺点：

- 资源消耗过大，如果需要保存的原发器类的成员变量太多，就不可避免需要占用大量的存储空间，每保存一次对象的状态都需要消耗一定的系统资源。

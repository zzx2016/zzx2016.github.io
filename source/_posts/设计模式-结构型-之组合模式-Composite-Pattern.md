---
title: 设计模式(结构型)之组合模式(Composite Pattern)
date: 2016-05-28 16:43:22
tags:
- Java
- 设计模式
---

[原文地址](http://blog.csdn.net/yanbober)

### 概述

组合模式又叫做部分-整体模式，使我们在树型结构的问题中模糊简单元素和复杂元素的概念，客户程序可以像处理简单元素一样来处理复杂的元素，从而使得客户程序与复杂元素的内部结构解耦。组合模式可以优化处理递归或分级数据结构。有许多关于分级数据结构的例子，使得组合模式非常有用武之地。

<!--more-->

### 核心

**概念：** 组合多个对象形成树形结构以表示具有“整体—部分”关系的层次结构。组合模式对单个对象（即叶子对象）和组合对象（即容器对象）的使用具有一致性，组合模式又可以称为“整体—部分”(Part-Whole)模式，它是一种对象结构型模式。

**重点：** 组合模式结构重要核心模块：

1. 抽象构件（Component）

  组合中的对象声明接口，在适当的情况下实现所有类共有接口的默认行为。声明一个接口用于访问和管理Component子部件。

2. 树叶构件（Leaf）

  在组合中表示树的叶子结点对象，叶子结点没有子结点。

3. 容器构件（Composite）

  定义有枝节点的部件，在Component接口中实现与子部件有关操作，如增加(add)和删除(remove)等。

**核心：** 组合模式的关键是定义一个抽象构件类，它既可以代表Leaf，又可以代表Composite，而客户端针对该抽象构件类进行编程，无须知道它到底表示的是叶子还是容器，可以对其进行统一处理。同时容器对象与抽象构件类之间还建立一个聚合关联关系，在容器对象中既可以包含叶子，也可以包含容器，以此实现递归组合，形成一个树形结构。

### 使用场景

在具有整体和部分的层次结构中，希望通过一种方式忽略整体与部分的差异，客户端可以一致地对待它们。

在一个使用面向对象语言开发的系统中需要处理一个树形结构。

在一个系统中能够分离出叶子对象和容器对象，而且它们的类型不固定，需要增加一些新的类型。

### 程序猿实例

假设问题环境：

就拿Android开发中最常见的一种情景来分析吧。我们有一种需求是遍历整个cache文件夹下的所有文件及文件夹，打印出来（实际指定不是打印，而是逻辑操作，这里演示而已）。如下我们先不使用组合模式。

```Java
import java.util.ArrayList;
import java.util.List;

class MediaFile {
    private String mName;
    private String mDescription;

    public MediaFile(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    @Override
    public String toString() {
        return "MediaFile# Name="+mName+", Description="+mDescription;
    }
}

class OfficeFile {
    private String mName;
    private String mDescription;

    public OfficeFile(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    @Override
    public String toString() {
        return "OfficeFile# Name="+mName+", Description="+mDescription;
    }
}

class PackageFile {
    private String mName;
    private String mDescription;

    public PackageFile(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    @Override
    public String toString() {
        return "PackageFile# Name="+mName+", Description="+mDescription;
    }
}

class BaseFolder {
    private String mName;
    private String mDescription;

    private List<BaseFolder> mBaseFolderList = new ArrayList<>();
    private List<MediaFile> mMediaFileList = new ArrayList<>();
    private List<OfficeFile> mOfficeFileList = new ArrayList<>();
    private List<PackageFile> mPackageFileList = new ArrayList<>();

    public BaseFolder(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    public void addBaseFolder(BaseFolder baseFolder) {
        mBaseFolderList.add(baseFolder);
    }

    public void addMediaFile(MediaFile mediaFile) {
        mMediaFileList.add(mediaFile);
    }

    public void addOfficeFile(OfficeFile officeFile) {
        mOfficeFileList.add(officeFile);
    }

    public void addPackageFile(PackageFile packageFile) {
        mPackageFileList.add(packageFile);
    }

    @Override
    public String toString() {
        StringBuilder builder = new StringBuilder("*****BaseFolder# enter folder is:"+mName+"\n");

        for (BaseFolder baseFolder : mBaseFolderList) {
            builder.append(baseFolder.toString()+"\n");
        }

        for (MediaFile mediaFile : mMediaFileList) {
            builder.append(mediaFile.toString()+"\n");
        }

        for (OfficeFile officeFile : mOfficeFileList) {
            builder.append(officeFile.toString()+"\n");
        }

        for (PackageFile packageFile : mPackageFileList) {
            builder.append(packageFile.toString()+"\n");
        }

        return builder.toString();
    }
}

public class Main {
    public static void main(String[] args) {
        BaseFolder folder = new BaseFolder("cache", "app cache dir.");
        MediaFile mediaFile = new MediaFile("test.png", "test picture.");
        folder.addMediaFile(mediaFile);
        mediaFile = new MediaFile("haha.mp4", "test video.");
        folder.addMediaFile(mediaFile);
        PackageFile packageFile = new PackageFile("yanbo.apk", "an android application.");
        folder.addPackageFile(packageFile);
        BaseFolder childDir = new BaseFolder("word", "office dir.");
        OfficeFile officeFile = new OfficeFile("rrrr.doc", "office doc file.");
        childDir.addOfficeFile(officeFile);
        folder.addBaseFolder(childDir);
        System.out.println(folder.toString());
    }
}
```

有了如上实现那么问题来了。。。

如上代码你会发现客户端代码过多地依赖于容器对象复杂的内部实现结构，容器对象内部实现结构的变化将引起客户代码的频繁变化，带来了代码维护复杂、可扩展性差等弊端。组合模式的引入将在一定程度上解决这些问题。

准么办？升个级呗，用一下组合模式。

**升个级吧**

给上面代码来个重构？ 那就按照组合模式结构重要核心模块来对上面代码升级重构一把吧。

```Java
import java.util.ArrayList;
import java.util.List;

abstract class AbstractComponent {
    public abstract void add(AbstractComponent c);
    public abstract AbstractComponent get(int index);
    public abstract String getString();
}

class MediaFile extends AbstractComponent {
    private String mName;
    private String mDescription;

    public MediaFile(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    @Override
    public void add(AbstractComponent c) {
        //unused
    }

    @Override
    public AbstractComponent get(int index) {
        return null;
    }

    @Override
    public String getString() {
        return "MediaFile# Name="+mName+", Description="+mDescription;
    }
}

class OfficeFile extends AbstractComponent {
    private String mName;
    private String mDescription;

    public OfficeFile(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    @Override
    public void add(AbstractComponent c) {
        //unused
    }

    @Override
    public AbstractComponent get(int index) {
        return null;
    }

    @Override
    public String getString() {
        return "OfficeFile# Name="+mName+", Description="+mDescription;
    }
}

class PackageFile extends AbstractComponent {
    private String mName;
    private String mDescription;

    public PackageFile(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    @Override
    public void add(AbstractComponent c) {
        //unused
    }

    @Override
    public AbstractComponent get(int index) {
        return null;
    }

    @Override
    public String getString() {
        return "PackageFile# Name="+mName+", Description="+mDescription;
    }
}

class BaseFolder extends AbstractComponent {
    private String mName;
    private String mDescription;

    private List<AbstractComponent> abstractComponents = new ArrayList<>();

    public BaseFolder(String mName, String mDescription) {
        this.mName = mName;
        this.mDescription = mDescription;
    }

    @Override
    public void add(AbstractComponent c) {
        abstractComponents.add(c);
    }

    @Override
    public AbstractComponent get(int index) {
        return abstractComponents.get(index);
    }

    @Override
    public String getString() {
        StringBuilder builder = new StringBuilder("*****BaseFolder# enter folder is:"+mName+"\n");

        for (AbstractComponent baseFolder : abstractComponents) {
            builder.append(baseFolder.getString()+"\n");
        }

        return builder.toString();
    }
}

public class Main {
    public static void main(String[] args) {
        AbstractComponent folder = new BaseFolder("cache", "app cache dir.");
        AbstractComponent mediaFile = new MediaFile("test.png", "test picture.");
        folder.add(mediaFile);
        mediaFile = new MediaFile("haha.mp4", "test video.");
        folder.add(mediaFile);
        AbstractComponent packageFile = new PackageFile("yanbo.apk", "an android application.");
        folder.add(packageFile);
        BaseFolder childDir = new BaseFolder("word", "office dir.");
        AbstractComponent officeFile = new OfficeFile("rrrr.doc", "office doc file.");
        childDir.add(officeFile);
        folder.add(childDir);
        System.out.println(folder.getString());
    }
}
```

上例通过组合模式代码具有了良好的可扩展性，在增加新的文件类型时，无须修改现有类库代码，只需增加一个新的文件类作为AbstractFile类的子类即可（不用在BaseFolder中再去修改添加），但是由于在AbstractFile中声明了大量用于管理和访问成员构件的方法，例如add()，我们需要在新增的文件类中实现这些方法，提供对应的错误提示和异常处理。这下你就明白了，其实如上代码还可以继续优化的，把每个子类实现的错误提示和异常处理写入抽象的基类中作为默认处理，这样也可以减少代码重复。哈哈。

### 总结一把

当发现需求中是体现部分与整体层次结构时，以及你希望用户可以忽略组合对象与单个对象的不同，统一地使用组合结构中的所有对象时，就应该考虑组合模式了。

#### 组合模式优点如下：

- 组合模式可以清楚地定义分层次的复杂对象，表示对象的全部或部分层次，它让客户端忽略了层次的差异，方便对整个层次结构进行控制。
- 客户端可以一致地使用一个组合结构或其中单个对象，不必关心处理的是单个对象还是整个组合结构，简化了客户端代码。
- 在组合模式中增加新的容器构件和叶子构件都很方便，无须对现有类库进行任何修改，符合“开闭原则”。
- 组合模式为树形结构的面向对象实现提供了一种灵活的解决方案，通过叶子对象和容器对象的递归组合，可以形成复杂的树形结构，但对树形结构的控制却非常简单。

#### 组合模式缺点如下：

- 在增加新构件时很难对容器中的构件类型进行限制。有时候我们希望一个容器中只能有某些特定类型的对象，例如在某个文件夹中只能包含文本文件，使用组合模式时，不能依赖类型系统来施加这些约束，因为它们都来自于相同的抽象层，在这种情况下，必须通过在运行时进行类型检查来实现，这个实现过程较为复杂。

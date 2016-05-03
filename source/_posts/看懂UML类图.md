---
title: 看懂UML类图
date: 2016-04-29 11:48:47
tags: UML
---

统一建模语言（UML，UnifiedModelingLanguage）是面向对象软件的标准化建模语言。UML因其简单、统一的特点，而且能表达软件设计中的动态和静态信息，目前已成为可视化建模语言的工业标准。本文针对静态类图。

<!--more-->

### 类

第一行表示类名，第二行表示成员变量，第三行表示成员方法。其中变量和方法前的“+”表示public，“#”表示protected，“-”表示private，什么都没有的话表示访问权限是友好的。如下如所示：

![类](http://7q5ctm.com1.z0.glb.clouddn.com/uml-%E7%B1%BB.png)

### 接口

接口和类的表示方法类似，区别就是接口的名字必须要是斜体，而且必须要用“<<interface>>”修饰。如下如所示：

![接口](http://7q5ctm.com1.z0.glb.clouddn.com/uml-%E6%8E%A5%E5%8F%A3.png)

### 泛化关系

泛化关系是指，类的继承关系。如下如所示：

![泛化关系](http://7q5ctm.com1.z0.glb.clouddn.com/uml-%E6%B3%9B%E5%8C%96.png)

### 实现关系

实现关系是指，一个类实现了一个接口。如下如所示：

![实现关系](http://7q5ctm.com1.z0.glb.clouddn.com/uml-%E5%AE%9E%E7%8E%B0.png)

### 关联关系

关联关系是指，A类中的成员变量的类型是B类型。如下如所示：

![关联关系](http://7q5ctm.com1.z0.glb.clouddn.com/uml-%E5%85%B3%E8%81%94.png)

### 依赖关系

依赖关系是指，A类中方法的参数类型是B类型。如下如所示：

![依赖关系](http://7q5ctm.com1.z0.glb.clouddn.com/uml-%E4%BE%9D%E8%B5%96.png)

### 注释

使用注释为类图添加说明。如下如所示：

![注释](http://7q5ctm.com1.z0.glb.clouddn.com/uml-%E6%B3%A8%E9%87%8A.png)

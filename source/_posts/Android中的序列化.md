---
title: Android中的序列化
date: 2016-03-19 15:10:02
tags: Android
---

#### 为什么要序列化
1. 将对象的字节序列保存到本地文件。
2. 在网络中传递对象。
3. 在进程或者Activity中传递对象。
<!--more-->

#### 序列化方式
序列化分为两种：一种是实现Serializable接口，另一种是实现Parcelable接口。
1. 实现Serializable接口
直接实现Serializable接口即可，此方法支持Java SE和Android，示例代码如下：

```Java
public class Student implements Serializable {
    private String name; private int age;
    public String getName(){
        return name;
    }
    public void setName(String name){
        this.name = name;
    }
    public int getAge(){
        return age;
    }
    public void setAge(int age){
        this.age = age;
    }
}
```

2. 实现Parcelable接口

采用此方法相对复杂，且仅支持Android，使用步骤如下：
- 实现Parcelable接口，即implements Parcelable。
- 重写writeToParcel()方法，将对象序列化为一个Parcel对象。
- 重写describeContents()方法，内容接口描述，默认返回0即可（暂时不清楚具体含义）。
- 实例化静态内部对象CREATOR（变量必须用static final修饰，变量名必须为CREATOR，并且为大写）。

示例代码如下：
```Java
public class Student implements Parcelable {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }  

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel out, int flags) {
        out.writeString(name);
        out.writeInt(age);
    }

    public static final Parcelable.Creator<student> CREATOR = new Parcelable.Creator<student>() {
        @Override
        public Student[] newArray(int size) {
            return new Student[size];
        }

        @Override
        public Student createFromParcel(Parcel in) {
            return new Student(in);
        }
    };
    /** * 属性的读取顺序必须与writeToParcel()方法一致 */
    public Student(Parcel in) {
        name = in.readString();
        age = in.readInt(); 
    }
}
```

#### Serializable与Parcelabel的优缺点
1. 采用Serializable方式使用简单，只需要实现Serializable接口即可，但该方式采用了反射机制，所以效率较低。
2. 采用Parcelabel方式使用相对复杂，除了需要实现Parcelabel接口，还要添加CREATOR变量，但此种方式效率明显高于Serializable方式。

#### 适用范围
Parcelabel适合在进程间和Activity之间传递数据，而Serializable更倾向于网络传输或者将对象本地化。

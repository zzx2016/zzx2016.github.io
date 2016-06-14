---
title: Java注解
date: 2016-06-02 14:24:58
tags: Java
---

[原文地址](http://daishiwen.github.io/Java-Annotation.html)

之前对注解的认识，只是局限在@Override @Deprecated @SuppressWarnings，好处也只是知道使用注解可以有更加干净易读的代码、可以在编译期进行类型检查，其他的知之甚少。但随着目前新技术的蓬勃发展，各种新技术、新框架涌现了出来，其中好多会用到注解，目前知道的有Dragger2 EventBus3 Retrofit ActiveAndroid等等。如果不深入了解注解的相关知识，可能在学习这些新东西时徒增一些困惑。所以接下来，我会由浅入深地带你走进注解的殿堂，一起学习，一起探索。

<!--more-->

### 基本用法

在下面的例子中，使用@Test对testExecute()方法进行注解。该注解本身并不做任何事情，我们可以创建一个通过反射机制来运行testExecute()方法的工具。

```Java
public class Testable {

    public void execute() {
        System.out.println("Executing...");
    }

    @Test
    void testExecute() {
        execute();
    }
}
```

被注解的方法与其他方法并没有区别。在这个例子中，注解@Test可以与任何修饰符共同作用于方法，例如public、static或void。从语法的角度看，注解和修饰符的使用方式几乎一摸一样。

#### 定义注解

可以看到，注解的定义看起来很像接口的定义。事实上，与其他任何Java接口一样，注解也将会编译成class文件。

```Java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {

}
```

定义注解时，会需要一些元注解（meta-annotation），如@Target和@Retention。@Target用来定义你的注解将应用于什么地方（例如是一个方法或者一个域）。@Retention用来定义注解的可用级别，在源代码中（SOURCE）、类文件中（CLASS）或者运行时（RUNTIME）。

在注解中，一般都会包含一些元素以表示某些值。当分析处理注解时，程序或工具可以利用这些值。注解的元素看起来就像接口的方法，唯一的区别是你可以为其指定默认值。 没有元素的注解称为标记注解（marker annotation），例如上例中的@Test。

下面看看有元素的注解：

```Java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface UseCase {
    int id();
    String description() default "no description";
}
```

注意，id和description类似方法定义。description元素有一个default值，如果在注解某个方法时没有给出description的值，则该注解的处理器就会使用此元素的默认值。在下面的类中，有三个方法被注解：

```Java
public class PasswordUtils {
    @UseCase(id = 47, description = "Passwords must contain at least one numeric")
    public boolean validatePassword(String password) {
        return password.matches("\\w*\\d\\w*");
    }

    @UseCase(id = 48)
    public String encryptPassword(String password) {
        return new StringBuilder(password).reverse().toString();
    }

    @UseCase(id = 49, description = "New passwords can't equal previously used ones")
    public boolean checkForNewPassword(List<String> prevPasswords, String password) {
        return !prevPasswords.contains(password);
    }
}
```

注解的元素在使用时表现为key-value键值对的形式。在encryptPassword()方法的注解中，并没有给出description元素的值，因此，在注解处理器分析处理这个类时会使用该元素的默认值。

#### 元注解

Java目前只内置了三种标准注解，以及四中元注解。元注解专职负责注解其他的注解：

1. @Target 表示该注解可以用于什么地方。

  - CONSTRUCTOR 构造器的声明
  - FIELD 域声明（包括enum实例）
  - LOCAL_VARIABLE 局部变量声明
  - METHOD 方法声明
  - PACKAGE 包声明
  - PARAMETER 参数声明
  - TYPE 类、接口（包括注解类型）或enum声明

2. @Retention 表示需要在什么级别保存该注解信息。

  - SOURCE 注解将被编译器丢弃
  - CLASS 注解在class文件中可用，但会被VM丢弃。
  - RUNTIME VM将在运行期也保留注解，因此可以通过反射机制读取注解的信息。

3. @Documented 将此注解包含在Javadoc中

4. @Inherited 允许子类继承父类中的注解

### 编写注解处理器

如果没有用来读取注解的工具，那么注解也不会比注释更有用。使用注解的过程中，很重要的一个部分就是创建与使用注解处理器。Java SE5扩展了反射机制的API，已帮助我们构造这类工具。同时，它还提供了一个外部工具apt帮助我们解析带有注解的Java源代码。

下面是一个非常简单的注解处理器，我们将用它来读取PasswordUtils类，并使用反射机制查找@UseCase标记。

```Java
public class UseCaseTracker {
    public static void trackUseCases(List<Integer> useCases, Class<?> cl) {
        for (Method m : cl.getDeclaredMethods()) {
            UseCase uc = m.getAnnotation(UseCase.class);
            if (uc != null) {
                System.out.println("Found Use Case: " + uc.id() + " " + uc.description());
                useCases.remove(new Integer(uc.id()));
            }
        }
        for (int i : useCases) {
            System.out.println("Warning: Missing use case " + i);
        }
    }

    public static void main(String[] args) {
        List<Integer> useCases = new ArrayList<>();
        Collections.addAll(useCases, 47, 48, 49, 50);
        trackUseCases(useCases, PasswordUtils.class);
    }
}
```

**Output:**

Found Use Case: 47 Passwords must contain at least one numeric
Found Use Case: 48 no description
Found Use Case: 49 New passwords can't equal previously used ones
Warning: Missing use case-50

这个程序用到了两个反射方法：getDeclaredMethods()和getAnnotation()，它们都属于AnnotationElement接口（Class、Method与Field等类都实现了该接口）。getAnnotation()方法返回指定类型的注解对象，在这里就是UseCase。如果被注解的方法上没有该类型的注解，则返回null。然后我们通过调用id()和description()方法从返回的UseCase对象中提取元素的值。

#### 注解元素

注解元素可用的类型如下所示：

- 所有基本类型（int、float、boolean等）
- String
- Class
- enum
- Annotation
- 以上类型的数组

如果你使用了其他类型，那么编译器就会报错。注解也可以作为元素的类型，也就是说注解可以嵌套，这是一个很有用的技巧。

#### 默认值限制

编译器对元素的默认值有些过分的挑剔。首先，元素不能有不确定的值。也就是说，元素必须要么具有默认值，要么在使用时提供元素的值。其次，对于非基本类型的元素，不论是在定义默认值时，或是在使用注解时，都不能以null作为其值。这个约束使得处理器很难表现一个元素的存在或缺失状态，为了绕开这个约束，我们只能自己定义一些特殊的值，例如空字符串或负数，以此表示某个元素不存在。

#### 生成外部文件（了解）

假如你希望提供一些基本的对象/关系映射功能，能够自动生成数据库表，用以存储JavaBean对象。你可以选用XML描述文件，指明类的名字、每个成员以及数据库映射的相关信息。然而如果使用注解的话，你可以将所有信息保存在JavaBean源文件中。为此，我们需要一些新的注解，用以定义与Bean关联的数据库表的名字，以及与Bean属性关联的列的名字和SQL类型。

以下是一个注解的定义，它告诉注解处理器，你需要为我生成一个数据库表：

```Java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface DBTable {
    String name() default "";
}
```

在@Target注解中指定的每一个ElementType就是一个约束，它告诉编译器，这个自定义的注解只能应用于该类型。我们可以指定一个值或者以逗号分隔的形式指定多个值。如果想要将注解应用于所有的ElementType，可以省去@Target元注解，不过这并不常见。

接下来是为修饰JavaBean域准备的注解：

```Java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Constrains {
    boolean primaryKey() default false;
    boolean allowNull() default true;
    boolean unique() default false;
}

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLString {
    int value() default 0;
    String name() default "";
    Constrains constrains() default @Constrains;
}

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
public @interface SQLInteger {
    String name() default "";
    Constrains constrains() default @Constrains;
}
```

下面是一个简单的Bean定义，我们应用了以上这些注解：

```Java
@DBTable(name = "Member")
public class Member {
    @SQLString(30)
    String firstName;
    @SQLString(50)
    String lastName;
    @SQLInteger
    Integer age;
    @SQLString(value = 30, constrains = @Constrains(primaryKey = true))
    String handle;
    static int memberCount;
}
```

类的注解@DBTable的元素值Member将会用来作为表的名字。Bean的属性firstName和lastName都被注解为@SQLString类型，并且其元素值分别为30和50。这些注解有两个有趣的地方：第一，它们都使用了嵌入的@Constrains注解的默认值；第二，它们都使用了快捷方式。何为快捷方式呢？如果注解中定义了名为value的元素，并且在使用该注解的时候，如果该元素是唯一一个需要赋值的元素，那么此时无需使用key-value的写法，而只需在括号内给出value元素的值即可。

#### 注解不支持继承

不能使用关键字extends来继承某个@interface

#### 实现注解处理器（了解）

下面是一个注解处理器的例子，它将读取一个类文件，检查其上的注解，并生成SQL命令：

```Java
public class TableCreater {

    public static void main(String[] args) throws Exception {

        createSQL(new String[]{"com.example.kevindai.annotation.db.Member"});
    }

    private static void createSQL(String[] classnames) throws Exception {

        for (String classname : classnames) {
            Class<?> cl = Class.forName(classname);
            DBTable dbTable = cl.getAnnotation(DBTable.class);
            if (dbTable == null) {
                System.out.println("No DBTable annotations in class " + classname);
                continue;
            }
            String tableName = dbTable.name();
            // If the tableName is empty use the class name.
            if (tableName.length() < 1)
                tableName = cl.getSimpleName();
            List<String> columnDefs = new ArrayList<>();
            for (Field field : cl.getDeclaredFields()) {
                Annotation[] anns = field.getDeclaredAnnotations();
                if (anns.length < 1)
                    continue;// Not a db table column
                if (anns[0] instanceof SQLInteger) {
                    SQLInteger sInt = (SQLInteger) anns[0];
                    String columnName;
                    // Use field name if name not specified
                    if (sInt.name().length() < 1)
                        columnName = field.getName();
                    else
                        columnName = sInt.name();
                    columnDefs.add(columnName + " INT" + getConstrains(sInt.constrains()));
                }
                if (anns[0] instanceof SQLString) {
                    SQLString sString = (SQLString) anns[0];
                    String columnName;
                    // use field name if name not specified
                    if (sString.name().length() < 1)
                        columnName = field.getName();
                    else
                        columnName = sString.name();
                    columnDefs.add(columnName + " VARCHAR(" + sString.value() + ")" + getConstrains(sString.constrains()));
                }
            }
            StringBuilder createCommand = new StringBuilder("CREATE TABLE " + tableName + "(");
            for (String columnDef : columnDefs)
                createCommand.append("\n    ").append(columnDef).append(",");
            // Remove trailing comma
            String tableCreate = createCommand.substring(0, createCommand.length() - 1) + ");";
            System.out.println(tableCreate);
        }
    }

    private static String getConstrains(Constrains con) {

        String constrains = "";
        if (!con.allowNull())
            constrains += " NOT NULL";
        if (con.primaryKey())
            constrains += " PRIMARY KEY";
        if (con.unique())
            constrains += " UNIQUE";
        return constrains;
    }

}
```

```SQL
CREATE TABLE Member(
    firstName VARCHAR(30),
    lastName VARCHAR(50),
    age INT,
    handle VARCHAR(30) PRIMARY KEY);
```

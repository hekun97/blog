---
title: 面向对象编程
date: 2020-12-01 11:14:00
type: artitalk
categories:
- Java基础
tags:
- JAVA
cover: https://cdn.pixabay.com/photo/2017/02/22/11/47/foggy-2089233_960_720.jpg

---

# 面向对象编程(`OOP`)

## 面向对象编程的学习路线

面向对象的基本概念，包括：

- 类
- 实例
- 方法

面向对象的实现方式，包括：

- 继承
- 多态

Java语言本身提供的机制，包括：

- package
- classpath
- jar

以及Java标准库提供的核心类，包括：

- 字符串
- 包装类型
- JavaBean
- 枚举
- 常用工具类

## 面向对象基础

**面向对象和面向过程编程的概念**

1. **面向过程编程：**是把模型分解成一步一步的过程。

2. **面向对象编程：**是一种通过对象的方式，把现实世界映射到计算机模型的一种编程方法。

**面向对象编程解析**

现实世界中，我们定义了“人”这种抽象概念，而具体的人则是“小明”、“小红”、“小军”等一个个具体的人。所以，“人”可以定义为一个类（`class`），而具体的人则是实例（`instance`）

| 现实世界 | 计算机模型    | `Java`代码                   |
| :------- | :------------ | :--------------------------- |
| 人       | 类 / `class`  | `class Person { }`           |
| 小明     | 实例 / `ming` | `Person ming = new Person()` |
| 小红     | 实例 / `hong` | `Person hong = new Person()` |
| 小军     | 实例 / `jun`  | `Person jun = new Person()`  |

**`class`和`instance`**

`class`是一种对象模版，它定义了如何创建实例，因此，`class`本身就是一种数据类型。

![class类似模型](https://www.liaoxuefeng.com/files/attachments/1260571618658976/l)

而`instance`是对象实例，`instance`是根据`class`创建的实例，可以创建多个`instance`，每个`instance`类型相同，但各自属性可能不相同。

![instances类似用模型做出来的实例](https://www.liaoxuefeng.com/files/attachments/1260571718581056/l)

> `class`本身就是一种数据类型，类似`String`；每个`instance`类型相同，但各自属性可能不相同，类似"`Hello`"，"`hi`"的类型相同，但值不一样。

###  方法(`method`)

一个`class`可以包含多个`field`，但是，直接把`field`用`public`暴露给外部可能会破坏封装性，直接操作`field`，容易造成逻辑混乱。因此可以使用`private`修饰`field`，拒绝外部访问，然后使用方法（`method`）来让外部代码可以间接修改`field`，在方法内部，我们还可以检查参数对不对。

### 构造方法(`Constructor Method`)

在创建对象实例时就把内部字段全部初始化为合适的值，构造方法的名称就是类名。



> 1. 和普通方法相比，构造方法没有返回值（也没有`void`），调用构造方法，必须用`new`操作符。
> 2. 一个构造方法可以调用其他构造方法，这样做的目的是便于代码复用。调用其他构造方法的语法是`this(…)`

### 方法重载(`overloading method`)

在一个类中，我们可以定义多个方法。如果有一系列方法，它们的功能都是类似的，只有参数有所不同，那么，可以把这一组方法名做成同名方法。这种方法名相同，但各自的参数不同，称为方法重载（`Overload`）。

方法重载的目的是，功能类似的方法使用同一名字，更容易记住，因此，调用起来更简单。

> 方法重载的返回值类型通常都是相同的。

### 继承(`inherit`)

继承是面向对象编程中非常强大的一种机制，它可以复用代码，我们只需要为子类编写新增的功能

> 继承有个特点，就是子类无法访问父类的`private`字段或者`private`方法。为了让子类可以访问父类的字段，我们需要把`private`改为`protected`

### 多态(`polymorphic`)

#### 覆写的概念

在继承关系中，子类如果定义了一个与父类方法签名完全相同的方法，被称为覆写（`Override`）

##### 覆写和重载的不同

如果方法签名不同，就是重载(`Overload`)，`Overload`方法是一个新方法；如果方法签名相同，并且返回值也相同，就是覆写(`Override`)。

> 1. 方法签名由方法名+方法参数构成
>
> 2. 加上`@Override`可以让编译器帮助检查是否进行了正确的覆写，但是`@Override`不是必需的。

#### 多态的概念

一个实际类型为`Student`，引用类型为`Person`的变量，调用其`run()`方法，实际上调用的方法是`Student`的`run()`方法。因此可得出结论：

- `Java`的实例方法调用是基于运行时的**实际类型**的动态调用，而非变量的声明类型。这个非常重要的特性在面向对象编程中称之为多态。

多态是指，针对某个类型的方法调用，其真正执行的方法取决于运行时期**实际类型**的方法。

##### 多态的作用

允许添加更多类型的子类实现功能扩展，却不需要修改基于父类的代码

#### `final`修饰符的作用

- `final`修饰的方法可以阻止被覆写；
- `final`修饰的class可以阻止被继承；
- `final`修饰的field必须在创建对象时初始化，随后不可修改。

### 抽象类(`abstract class`)

#### 抽象类的概念

父类的方法本身不需要实现任何功能，仅仅是为了定义方法签名，目的是让子类去覆写它，那么，可以把父类的方法声明为抽象方法。

```java
abstract class Person {
    public abstract void run();
}
```

> 必须把`Person`类本身声明为`abstract`抽象类，才能正确编译。

#### 抽象类的作用

抽象方法实际上相当于定义了“规范”。因此，抽象类强迫定义子类时，必须实现其定义的抽象方法，否则编译会报错。

#### 面向抽象编程

尽量引用高层类型，避免引用实际子类型的方式，称之为面向抽象编程。

面向抽象编程的本质就是：

- 上层代码只定义规范（例如：`abstract class Person`）；
- 不需要子类就可以实现业务逻辑（正常编译）；
- 具体的业务逻辑由不同的子类实现，调用者并不关心。

### 接口(`interface`)

接口的概念

如果一个抽象类没有字段，所有方法全部都是抽象方法，就可以把该抽象类改写为接口(`interface`)。

```java
interface Person {
    void run();
    String getName();
}
```

> 1. `interface`是比抽象类还要抽象的纯抽象接口，不能拥有字段，但可以拥有静态字段，且静态字段为`final`类型。
> 2. 接口定义的所有方法默认都是`public abstract`的，所以这两个修饰符不需要写出来（写不写效果都一样）。

当一个具体的`class`去实现一个`interface`时，需要使用`implements`关键字。

在Java中，一个类只能继承自另一个类，不能从多个类继承。但是，一个类可以实现多个`interface`。

#### 抽象类和接口的对比

|            | abstract class       | interface                   |
| :--------- | :------------------- | :-------------------------- |
| 继承       | 只能extends一个class | 可以implements多个interface |
| 字段       | 可以定义实例字段     | 不能定义实例字段            |
| 抽象方法   | 可以定义抽象方法     | 可以定义抽象方法            |
| 非抽象方法 | 可以定义非抽象方法   | 可以定义default方法         |

#### 接口继承

一个`interface`可以继承自另一个`interface`。`interface`继承自`interface`使用`extends`，它相当于扩展了接口的方法。

```java
interface Hello {
    void hello();
}

interface Person extends Hello {
    void run();
    String getName();
}
```

#### default方法

在接口中，可以定义`default`方法。

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Student("Xiao Ming");
        p.run();
    }
}

interface Person {
    String getName();
    default void run() {
        System.out.println(getName() + " run");
    }
}

class Student implements Person {
    private String name;

    public Student(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
}
```

实现类可以不必覆写`default`方法。`default`方法的目的是，当我们需要给接口新增一个方法时，会涉及到修改全部子类。如果新增的是`default`方法，那么子类就不必全部修改，只需要在需要覆写的地方去覆写新增方法。

> `default`方法和抽象类的普通方法是有所不同的。因为`interface`没有字段，`default`方法无法访问字段，而抽象类的普通方法可以访问实例字段。

### 静态字段和静态方法(`static field and static method`)

#### 静态字段

**实例字段**：每个实例中都有自己的一个独立“空间”，属于实例(`instance`)。

**静态字段**：只有一个共享“空间”，所有实例都会共享该字段，实际上属于类(`class`)。

对于静态字段，无论修改哪个实例的静态字段，效果都是一样的：所有实例的静态字段都被修改了，原因是静态字段并不属于实例。

推荐用`类名.静态字段`去访问静态字段。

##### 接口的静态字段

因为`interface`是一个纯抽象类，所以它不能定义实例字段。但是，`interface`是可以有静态字段的，并且静态字段必须为`final`类型。

```java
public interface Person {
    //因为interface的字段只能是public static final类型，所以可以把这些修饰符都去掉
    //public static final int MALE = 1;
    //public static final int FEMALE = 2;
    int MALE = 1;
    int FEMALE = 2;
}
```



#### 静态方法

有静态字段，就有静态方法。用`static`修饰的方法称为静态方法。

调用实例方法必须通过一个实例变量，而调用静态方法则不需要实例变量，通过类名就可以调用。静态方法类似其它编程语言的函数。

```java
public class Main {
    public static void main(String[] args) {
        Person.setNumber(99);
        System.out.println(Person.number);
    }
}

class Person {
    public static int number;

    public static void setNumber(int value) {
        number = value;
    }
}
```

> 1. 因为静态方法属于`class`而不属于实例，因此，静态方法内部，无法访问`this`变量，也无法访问实例字段，它只能访问静态字段和其它静态方法。
> 2. 静态方法经常用于工具类(`Arrays.sort()`、`Math.random()`)和辅助方法。

### 作用域

由`public`、`protected`、`private`这些修饰符用来限定访问作用域。

#### `public`

1. 定义为`public`的`class`、`interface`可以被其他任何类访问；
2. 定义为`public`的`field`、`method`可以被其他类访问，前提是首先有访问该`class`的权限。

#### `private`

定义为`private`的`field`、`method`访问权限被限定在`class`的内部，无法被其他类访问，但如果该类内部定义了嵌套类(`nested class`)，那么嵌套类可以访问。

#### `protected`

`protected`作用于继承关系。定义为`protected`的字段和方法可以被子类访问，以及子类的子类。

#### `package`

包作用域是指一个类允许访问同一个`package`中的没有`public`、`private`修饰的`class`，以及没有`public`、`private`、`protected`修饰的字段和方法。

> 1. 同一个包，指包名(`package abc;`)一致；
> 2. 只访问同一个包下的类的，加不加`public`都可以访问，但是如果是不同包下，就需要`public`。


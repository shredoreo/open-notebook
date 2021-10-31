> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/kaleidoscope/p/9790766.html)

# **Java 多态概述**

多态是面向对象编程语言的重要特性，它允许基类的指针或引用指向派生类的对象，而在具体访问时实现方法的动态绑定。Java 对于方法调用动态绑定的实现主要依赖于方法表，但通过类引用调用 (invokevitual) 和接口引用调用 (invokeinterface) 的实现则有所不同。

## 类引用调用

类引用调用的大致过程为：Java 编译器将 Java 源代码编译成 class 文件，在编译过程中，会根据静态类型将调用的符号引用写到 class 文件中。在执行时，JVM 根据 class 文件找到调用方法的符号引用，然后在静态类型的方法表中找到偏移量，然后根据 this 指针确定对象的实际类型，使用实际类型的方法表，偏移量跟静态类型中方法表的偏移量一样，如果在**实际类型的方法表中找到该方法，则直接调用，否则，认为没有重写父类该方法。按照继承关系从下往上搜索**。 

接口引用调用后面再说吧。

![](https://img-blog.csdn.net/20160812142709857)

从上图可以看出，当程序运行时，需要某个类时，类载入子系统会将相应的 class 文件载入到 JVM 中，并在内部建立该类的类型信息（这个类型信息其实就是 class 文件在 JVM 中存储的一种数据结构），包含 java 类定义的所有信息，包括方法代码，类变量、成员变量、以及本博文要重点讨论的方法表。这个类型信息就存储在方法区。 

注意，这个方法区中的类型信息跟在堆中存放的 class 对象是不同的。在方法区中，这个 class 的类型信息只有唯一的实例（所以是各个线程共享的内存区域），而在堆中可以有多个该 class 对象。可以通过堆中的 class 对象访问到方法区中类型信息。就像在 java 反射机制那样，通过 class 对象可以访问到该类的所有信息一样。

**【重点】** 

**方法表是实现动态调用的核心。**上面讲过方法表存放在方法区中的类型信息中。为了优化对象调用方法的速度，方法区的类型信息会增加一个指针，该指针指向一个记录该类方法的方法表，方法表中的每一个项都是对应方法的指针。  
这些方法中包括从父类继承的所有方法以及自身重写（override）的方法。

**【拓展】**

方法区：方法区和 JAVA 堆一样，是各个线程共享的内存区域，用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。   
运行时常量池：它是方法区的一部分，Class 文件中除了有类的版本、方法、字段等描述信息外，还有一项信息是常量池，用于存放编译器生成的各种符号引用，这部分信息在类加载时进入方法区的运行时常量池中。   
方法区的内存回收目标是针对常量池的回收及对类型的卸载。

## **Java** **的方法调用方式**

Java 的方法调用有两类，动态方法调用与静态方法调用。

*   静态方法调用是指对于类的静态方法的调用方式，是静态绑定的
*   动态方法调用需要有方法调用所作用的对象，是动态绑定的。

类调用 (invokestatic) 是在编译时就已经确定好具体调用方法的情况。

实例调用 (invokevirtual) 则是在调用的时候才确定具体的调用方法，这就是动态绑定，也是多态要解决的核心问题。

JVM 的方法调用指令有四个，分别是 invokestatic，invokespecial，invokesvirtual 和 invokeinterface。前两个是静态绑定，后两个是动态绑定的。本文也可以说是对于 JVM 后两种调用实现的考察。

**方法表与方法调用**

如有类定义 Person, Girl, Boy

```
class Person {
    public String toString() {
        return "I'm a person.";
    }
 
    public void eat() {
    }
 
    public void speak() {
    }
 
}
 
class Boy extends Person {
    public String toString() {
        return "I'm a boy";
    }
 
    public void speak() {
    }
 
    public void fight() {
    }
}
 
class Girl extends Person {
    public String toString() {
        return "I'm a girl";
    }
 
    public void speak() {
    }
 
    public void sing() {
    }
}
```

当这三个类被载入到 Java 虚拟机之后，方法区中就包含了各自的类的信息。Girl 和 Boy 在方法区中的方法表可表示如下：

<img src="assets/simpread-Java 多态实现原理/20160812143114843.png" style="zoom:150%;" />  

可以看到，Girl 和 Boy 的方法表包含继承自 Object 的方法，继承自直接父类 Person 的方法及各自新定义的方法。注意方法表条目指向的具体的方法地址，如 Girl 继承自 Object 的方法中，只有 toString() 指向自己的实现（Girl 的方法代码），其余皆指向 Object 的方法代码；其继承自于 Person 的方法 eat() 和 speak() 分别指向 Person 的方法实现和本身的实现。

如果子类改写了父类的方法，那么子类和父类的那些同名的方法共享一个方法表项。

因此，方法表的偏移量总是固定的。所有继承父类的子类的方法表中，其父类所定义的方法的偏移量也总是一个定值。  
Person 或 Object 中的任意一个方法，在它们的方法表和其子类 Girl 和 Boy 的方法表中的位置 (index) 是一样的。这样 JVM 在调用实例方法其实只需要指定调用方法表中的第几个方法即可。

如调用如下:  

```
class Party {
    void happyHour() {
        Person girl = new Girl();
        girl.speak();
    }
}
```

当编译 Party 类的时候，生成 girl.speak() 的方法调用假设为：

Invokevirtual #12

设该调用代码对应着 girl.speak(); #12 是 Party 类的常量池的索引。JVM 执行该调用指令的过程如下所示：

![](https://img-blog.csdn.net/20160812143436640)

（1）在常量池（这里有个错误，上图为 ClassReference 常量池而非 Party 的常量池）中找到方法调用的符号引用 。  
（2）查看 Person 的方法表，得到 speak 方法在该方法表的偏移量（假设为 15），这样就得到该方法的直接引用。 
（3）根据 this 指针得到具体的对象（即 girl 所指向的位于堆中的对象）。
（4）根据对象得到该对象对应的方法表，根据偏移量 15 查看有无重写（override）该方法，如果重写，则可以直接调用（Girl 的方法表的 speak 项指向自身的方法而非父类）；如果没有重写，则需要拿到按照继承关系从下往上的基类（这里是 Person 类)的方法表，同样按照这个偏移量 15 查看有无该方法。

**接口调用**

因为 Java 类是可以同时实现多个接口的，而当用接口引用调用某个方法的时候，情况就有所不同了。

Java 允许一个类实现多个接口，从某种意义上来说相当于多继承，这样同样的方法在基类和派生类的方法表的位置就可能不一样了

```
interface IDance {
    void dance();
}
 
class Person {
    public String toString() {
        return "I'm a person.";
    }
 
    public void eat() {
    }
 
    public void speak() {
    }
 
}
 
class Dancer extends Person implements IDance {
    public String toString() {
        return "I'm a dancer.";
    }
 
    public void dance() {
    }
}
 
class Snake implements IDance {
    public String toString() {
        return "A snake.";
    }
 
    public void dance() {
        //snake dance   
    }
}
```

可以看到，由于接口的介入，继承自于接口 IDance 的方法 dance() 在类 Dancer 和 Snake 的方法表中的位置已经不一样了，显然我们无法仅根据偏移量来进行方法的调用。

Java 对于接口方法的调用是采用搜索方法表的方式，如，要在 Dancer 的方法表中找到 dance() 方法，**必须搜索 Dancer 的整个方法表。**

因为每次接口调用都要搜索方法表，所以从效率上来说，接口方法的调用总是慢于类方法的调用的。
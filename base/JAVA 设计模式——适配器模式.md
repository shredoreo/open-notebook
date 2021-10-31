> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/mrkohaku/article/details/79087688)

适配器模式是一种**结构型**设计模式。适配器模式的思想是：**把一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起工作的两个类能够在一起工作**。

用电器来打个比喻：有一个电器的插头是三脚的，而现有的插座是两孔的，要使插头插上插座，我们需要一个插头转换器，这个转换器即是适配器。

适配器模式涉及 3 个角色：

*   **源（Adaptee）**：需要被适配的对象或类型，相当于插头。
*   **适配器（Adapter）**：连接目标和源的中间对象，相当于插头转换器。
*   **目标（Target）**：期待得到的目标，相当于插座。

适配器模式包括 3 种形式：类适配器模式、对象适配器模式、接口适配器模式（或又称作缺省适配器模式）。  
  

### 类适配器模式

从下面的结构图可以看出，`Adaptee`类并没有`method2()`方法，而客户端则期待这个方法。为使客户端能够使用 Adaptee 类，我们把`Adaptee`与`Target`衔接起来。`Adapter`与`Adaptee`是**继承关系**，这决定了这是一个类适配器模式。  
![](https://img-blog.csdn.net/20180118084339863?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXJrb2hha3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

代码实现：

源：

```
public class Adaptee {
    public void method1(){
        System.out.println("method 1");
    }
}
```

目标：

```
public interface Target {
    void method1();
    void method2();
}
```

适配器：

```
public class Adapter extends Adaptee implements Target {
    @Override
    public void method2() {
        System.out.println("method 2");
    }
}

// 测试
class AdapterTest {
    public static void main(String[] args) {
        Adapter adapter = new Adapter();
        adapter.method1();
        adapter.method2();
    }
}
```

运行结果：

> method 1  
> method 2

### 对象适配器模式

对象适配器模式是另外 6 种结构型设计模式的起源。  
![](https://img-blog.csdn.net/20180118092704554?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXJrb2hha3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)  
(图片来源于网络)

  
从下面的结构图可以看出，`Adaptee`类并没有`method2()`方法，而客户端则期待这个方法。与类适配器模式一样，为使客户端能够使用 Adaptee 类，我们把`Adaptee`与`Target`衔接起来。但这里我们不继承`Adaptee`，而是把`Adaptee`封装进`Adapter`里。这里`Adaptee`与`Adapter`是**组合关系**。  
![](https://img-blog.csdn.net/20180118091558254?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbXJrb2hha3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

代码实现：

`Target`和`Adaptee`和上面的类适配器一样，不再贴出。

适配器：

```
public class Adapter implements Target {

    private Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    @Override
    public void method1() {
        adaptee.method1();
    }

    @Override
    public void method2() {
        System.out.println("method 2");
    }

}

class AdapterTest {
    public static void main(String[] args) {
        Adapter adapter = new Adapter(new Adaptee());
        adapter.method1();
        adapter.method2();
    }
}
```

运行结果：

> method 1  
> method 2

### 类适配器与对象适配器的区别

类适配器使用的是继承的方式，直接继承了`Adaptee`，所以无法对`Adaptee`的子类进行适配。

对象适配器使用的是组合的方式，· 所以`Adaptee`及其子孙类都可以被适配。另外，对象适配器对于增加一些新行为非常方便，而且新增加的行为同时适用于所有的源。

基于组合 / 聚合优于继承的原则，使用对象适配器是更好的选择。但具体问题应该具体分析，某些情况可能使用类适配器会适合，最适合的才是最好的。

### 接口适配器模式（缺省适配模式）

接口适配器模式（缺省适配模式）的思想是，为一个接口提供缺省实现，这样子类可以从这个缺省实现进行扩展，而不必从原有接口进行扩展。

这里提供一个例子。`java.awt.KeyListener`是一个键盘监听器接口，我们把这个接口的实现类对象注册进容器后，这个容器就会对键盘行为进行监听，像这样：

```
public static void main(String[] args) {
        JFrame frame = new JFrame();
        frame.addKeyListener(new KeyListener() {
            @Override
            public void keyTyped(KeyEvent e) {}

            @Override
            public void keyPressed(KeyEvent e) {
                System.out.println("hey geek!");
            }

            @Override
            public void keyReleased(KeyEvent e) {
            }
        });
    }
```

可以看到其实我们只使用到其中一个方法，但必须要把接口中所有方法都实现一遍，如果接口里方法非常多，那岂不是非常麻烦。于是我们引入一个默认适配器，让适配器把接口里的方法都实现一遍，使用时继承这个适配器，把需要的方法实现一遍就好了。JAVA 里也为`java.awt.KeyListener`提供了这样一个适配器：`java.awt.KeyAdapter`。我们使用这个适配器来改改上面的代码：

```
public static void main(String[] args) {
        JFrame frame = new JFrame();
        frame.addKeyListener(new KeyAdapter() {
            @Override
            public void keyPressed(KeyEvent e) {
                System.out.println("fxcku!");
            }
        });
    }
```

这样不必再把每个方法都实现一遍，代码看起来简洁多了。在任何时候，如果不准备实现一个接口里的所有方法时，就可以使用 “缺省适配模式” 制造一个抽象类，实现所有方法，这样，从这个抽象类再继承下去的子类就不必实现所有的方法，只要重写需要的方法就可以了。

### 适配器模式的优缺点

*   **优点**  
    *   **更好的复用性**：系统需要使用现有的类，而此类的接口不符合系统的需要。那么通过适配器模式就可以让这些功能得到更好的复用。
    *   **更好的扩展性**：在实现适配器功能的时候，可以扩展自己源的行为（增加方法），从而自然地扩展系统的功能。
*   **缺点**  
    *   **会导致系统紊乱**：滥用适配器，会让系统变得非常零乱。例如，明明看到调用的是 A 接口，其实内部被适配成了 B 接口的实现，一个系统如果太多出现这种情况，无异于一场灾难。因此如果不是很有必要，可以不使用适配器，而是直接对系统进行重构。
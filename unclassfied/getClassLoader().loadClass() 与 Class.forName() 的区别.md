> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://blog.csdn.net/gyshun/article/details/80899148

       为什么要把 ClassLoader.loadClass(String name) 和 Class.forName(String name) 进行比较呢，因为他们都能在运行时对任意一个类，都能够知道该类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性。

在比较它俩之前需先了解一下 java 类装载的过程

java 类装载过程分为 3 步：

 　　![](https://images2015.cnblogs.com/blog/673139/201704/673139-20170407121140988-38103611.png)

　　1：加载

　　　　Jvm 把 class 文件字节码加载到内存中，并将这些静态数据装换成运行时数据区中方法区的类型数据，在运行时数据区堆中生成一个代表这个类

　　的 java.lang.Class 对象，作为方法区类数据的访问入口。

　　* 释：方法区不仅仅是存放方法，它存放的是类的类型信息。

　　2：链接：执行下面的校验、准备和解析步骤，其中解析步骤是可选的

　　　　a：校验：检查加载的 class 文件的正确性和安全性

　　　　b：准备：为类变量分配存储空间并设置类变量初始值，类变量随类型信息存放在方法区中, 生命周期很长，使用不当和容易造成内存泄漏。

　　　　* 释：类变量就是 static 变量；初始值指的是类变量类型的默认值而不是实际要赋的值

　　　　c：解析：jvm 将常量池内的符号引用转换为直接引用

　　3：初始化：执行类变量赋值和静态代码块

在了解了类装载过程之后我们继续比较二者区别：

*   Classloder.loaderClass(String name)

　　　　其实该方法内部调用的是：Classloder. loadClass(name, false)

　　　　方法：Classloder. loadClass(String name, boolean resolve)

　　　　　　　　1：参数 name 代表类的全限定类名

　　　　　　　　2：参数 resolve 代表是否解析，resolve 为 true 是解析该类

*   Class.forName(String name)

　　　　其实该方法内部调用的是：Class.forName(className, true, ClassLoader.getClassLoader(caller))

　　　　方法：Class.forName0(String name, boolean initialize, ClassLoader loader)

　　　　　　参数 name 代表全限定类名

　　　　　　参数 initialize 表示是否初始化该类，为 true 是初始化该类

　　　　　　参数 loader 对应的类加载器

*   两者最大的区别

　　　　Class.forName 得到的 class 是已经初始化完成的

　　　　Classloder.loaderClass 得到的 class 是还没有链接的

*   怎么使用

　　　　有些情况是只需要知道这个类的存在而不需要初始化的情况使用 Classloder.loaderClass，而有些时候又必须执行初始化就选择 Class.forName

　　　　例如：数据库驱动加载就是使用 Class.froName(“com.mysql.jdbc.Driver”),

　　　　下面我们来看看 Driver 的源代码：

<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>12345678910111213</td><td><code>public</code>&nbsp;<code>class</code>&nbsp;<code>Driver&nbsp;</code><code>extends</code>&nbsp;<code>NonRegisteringDriver&nbsp;</code><code>implements</code>&nbsp;<code>java.sql.Driver {</code>&nbsp;<code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>public</code>&nbsp;<code>Driver()&nbsp;</code><code>throws</code>&nbsp;<code>SQLException {</code>&nbsp;<code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>} &lt;br&gt;</code><code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>static</code>&nbsp;<code>{</code><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>try</code>&nbsp;<code>{</code><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>DriverManager.registerDriver(</code><code>new</code>&nbsp;<code>Driver());</code><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>}&nbsp;</code><code>catch</code>&nbsp;<code>(SQLException var1) {</code><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>throw</code>&nbsp;<code>new</code>&nbsp;<code>RuntimeException(</code><code>"Can\'t register driver!"</code><code>);</code><code>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</code><code>}</code><code>&nbsp;&nbsp;&nbsp;&nbsp;</code><code>}</code><code>}</code></td></tr></tbody></table>

　　　　从 Driver 的源码中我们可以看出 Driver 这个类只有一个 static 块，这样我们需要初始化后才能得到 DriverManager, 所以我们选择使用 Class.forName()
> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/554c138ca0f5)

java.lang.ClassLoader 类概述:

　　中文文档中对 ClassLoader 类的定义如下:

![](http://upload-images.jianshu.io/upload_images/2025271-71283413c8178179.png)

　　　从文档中对 ClassLoader 类的介绍可以总结出这个类的作用就是根据一个指定的类的全限定名, 找到对应的 Class 字节码文件, 然后加载它转化成一个 java.lang.Class 类的一个实例.

类加载器的划分:

　　　大部分 java 程序会使用以下 3 中系统提供的类加载器:

**启动类加载器 (Bootstrap ClassLoader):**

 这个类加载器负责将 \ lib 目录下的类库加载到虚拟机内存中, 用来加载 java 的核心库, 此类加载器并不继承于 java.lang.ClassLoader, 不能被 java 程序直接调用, 代码是使用 C++ 编写的. 是虚拟机自身的一部分.

**扩展类加载器 (Extendsion ClassLoader):**

这个类加载器负责加载 \ lib\ext 目录下的类库, 用来加载 java 的扩展库, 开发者可以直接使用这个类加载器.

**应用程序类加载器 (Application ClassLoader):**

　　　　这个类加载器负责加载用户类路径 (CLASSPATH) 下的类库, 一般我们编写的 java 类都是由这个类加载器加载, 这个类加载器是 CLassLoader 中的 getSystemClassLoader()方法的返回值, 所以也称为系统类加载器. 一般情况下这就是系统默认的类加载器.

　　除此之外, 我们还可以加入自己定义的类加载器, 以满足特殊的需求, 需要继承 java.lang.ClassLoader 类.

　　类加载器之间的层次关系如下图:

![](http://upload-images.jianshu.io/upload_images/2025271-bc7b0cebbf242d1d.png)

使用代码观察一下类加载器:

package com.wang.test;publicclass TestClassLoader {

    publicstaticvoid main(String[] args) {

        ClassLoader loader = TestClassLoader.class.getClassLoader();

        System.out.println(loader.toString());

        System.out.println(loader.getParent().toString());

        System.out.println(loader.getParent().getParent());

    }

}

　　观察打印结果:

sun.misc.Launcher$AppClassLoader@500c05c2

sun.misc.Launcher$ExtClassLoader@454e2c9c

null

　　第一行打印的是应用程序类加载器 (默认加载器), 第二行打印的是其父类加载器, 扩展类加载器, 按照我们的想法第三行应该打印启动类加载器的, 这里却返回的 null, 原因是 getParent(), 返回时 null 的话, 就默认使用启动类加载器作为父加载器.

 类加载器的双亲委派模型:

　　双亲委派模型是一种组织类加载器之间关系的一种规范, 他的工作原理是: 如果一个类加载器收到了类加载的请求, 它不会自己去尝试加载这个类, 而是把这个请求委派给父类加载器去完成, 这样层层递进, 最终所有的加载请求都被传到最顶层的启动类加载器中, 只有当父类加载器无法完成这个加载请求 (它的搜索范围内没有找到所需的类) 时, 才会交给子类加载器去尝试加载.

　　这样的好处是: java 类随着它的类加载器一起具备了带有优先级的层次关系. 这是十分必要的, 比如 java.langObject, 它存放在 \ jre\lib\rt.jar 中, 它是所有 java 类的父类, 因此无论哪个类加载都要加载这个类, 最终所有的加载请求都汇总到顶层的启动类加载器中, 因此 Object 类会由启动类加载器来加载, 所以加载的都是同一个类, 如果不使用双亲委派模型, 由各个类加载器自行去加载的话, 系统中就会出现不止一个 Object 类, 应用程序就会全乱了.

Class.forname() 与 ClassLoader.loadClass():

　　Class.forname(): 是一个静态方法, 最常用的是 Class.forname(String className); 根据传入的类的全限定名返回一个 Class 对象. 该方法在将 Class 文件加载到内存的同时, 会执行类的初始化.

如: Class.forName("com.wang.HelloWorld");

　　ClassLoader.loadClass(): 这是一个实例方法, 需要一个 ClassLoader 对象来调用该方法, 该方法将 Class 文件加载到内存时, 并不会执行类的初始化, 直到这个类第一次使用时才进行初始化. 该方法因为需要得到一个 ClassLoader 对象, 所以可以根据需要指定使用哪个类加载器.

如: ClassLoader cl=.......;cl.loadClass("com.wang.HelloWorld");
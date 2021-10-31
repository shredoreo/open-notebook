> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/144741342)

前面整理完 `Unsafe` ，不得不去了解下 `java.lang.invoke.Varhandle` 。如前面文章所说， `Unsafe` 是不建议开发者直接使用的，因为 `Unsafe` 所操作的并不属于 Java 标准，会容易带来一些安全性的问题。JDK9 之后，官方推荐使用 `java.lang.invoke.Varhandle` 来替代 `Unsafe` 大部分功能，对比 `Unsafe` ，`Varhandle` 有着相似的功能，但会更加安全，并且，在并发方面也提高了不少性能。

### **简介**

`Varhandle`是对变量或参数定义的变量系列的动态强类型引用，包括静态字段，非静态字段，数组元素或堆外数据结构的组件。 在各种访问模式下都支持访问这些变量，包括简单的读 / 写访问，volatile 的读 / 写访问以及 CAS (compare-and-set) 访问。简单来说 `Variable` 就是对这些变量进行绑定，通过 `Varhandle` 直接对这些变量进行操作。

### **实例**

*   目标实体类

```
public class Demo {
    public int publicVar = 1;
    protected int protectedVar = 2;
    private int privateVar = 3;
    public int[] arrayData = new int[]{1, 2, 3};
    @Override
    public String toString() {
        return "Demo{" +
                "publicVar=" + publicVar +
                ", protectedVar=" + protectedVar +
                ", privateVar=" + privateVar +
                ", arrayData=" + Arrays.toString(arrayData) +
                '}';
    }
}
```

*   访问 private 成员

```
private static void privateDemo() throws NoSuchFieldException, IllegalAccessException {
    Demo instance = new Demo();
    VarHandle varHandle = MethodHandles.privateLookupIn(Demo.class, MethodHandles.lookup())
            .findVarHandle(Demo.class, "privateVar", int.class);
    varHandle.set(instance, 33);
    System.out.println(instance);
}
```

输出

```
Demo{publicVar=11, protectedVar=2, privateVar=3, arrayData=[1, 2, 3]}
```

*   访问 protected 成员

```
private static void protectedDemo() throws NoSuchFieldException, IllegalAccessException {
    Demo instance = new Demo();

      VarHandle varHandle = MethodHandles.privateLookupIn(Demo.class,MethodHandles.lookup())
              .findVarHandle(Demo.class, "protectedVar", int.class);

    VarHandle varHandle = MethodHandles.lookup()
            .in(Demo.class)
            .findVarHandle(Demo.class, "protectedVar", int.class);
    varHandle.set(instance, 22);
    System.out.println(instance);
}
```

输出

```
Demo{publicVar=1, protectedVar=22, privateVar=3, arrayData=[1, 2, 3]}
```

*   访问 public 成员

```
private static void publicDemo() throws NoSuchFieldException, IllegalAccessException {
     Demo instance = new Demo();
     VarHandle varHandle = MethodHandles.lookup()
             .in(Demo.class)
             .findVarHandle(Demo.class, "publicVar", int.class);
     varHandle.set(instance, 11);
     System.out.println(instance);
 }
```

输出

```
Demo{publicVar=1, protectedVar=2, privateVar=33, arrayData=[1, 2, 3]}
```

*   访问 数组

```
private static void arrayDemo() throws NoSuchFieldException, IllegalAccessException {
    Demo instance = new Demo();
    VarHandle arrayVarHandle = MethodHandles.arrayElementVarHandle(int[].class);
    arrayVarHandle.compareAndSet(instance.arrayData, 0, 1, 11);
    arrayVarHandle.compareAndSet(instance.arrayData, 1, 2, 22);
    arrayVarHandle.compareAndSet(instance.arrayData, 2, 3, 33);
    System.out.println(instance);
}
```

输出

```
Demo{publicVar=1, protectedVar=2, privateVar=3, arrayData=[11, 22, 33]}
```

### **获取 Varhandle 方式汇总**

*   `MethodHandles.privateLookupIn(class, MethodHandles.lookup())`获取访问私有变量的 Lookup  
    
*   `MethodHandles.lookup()` 获取访问 protected、public 的 Lookup  
    
*   `findVarHandle`：用于创建对象中非静态字段的`VarHandle`。接收参数有三个，第一个为接收者的 class 对象，第二个是字段名称，第三个是字段类型。  
    
*   `findStaticVarHandle`：用于创建对象中静态字段的`VarHandle`，接收参数与`findVarHandle`一致。  
    
*   `unreflectVarHandle`：通过反射字段`Field`创建`VarHandle`。  
    
*   `MethodHandles.arrayElementVarHandle(int[].class)` 获取管理数组的 Varhandle  
    

### **功能**

VarHandle 来使用 plain、opaque、release/acquire 和 volatile 四种共享内存的访问模式，根据这四种共享内存的访问模式又分为写入访问模式、读取访问模式、原子更新访问模式、数值更新访问模式、按位原子更新访问模式。

*   写入访问模式 (write access modes)

获取指定内存排序效果下的变量值，包含的方法有 get、getVolatile、getAcquire、getOpaque 。

*   读取访问模式 (read access modes)

在指定的内存排序效果下设置变量的值，包含的方法有 set、setVolatile、setRelease、setOpaque 。

*   原子更新模式 (atomic update access modes)

原子更新访问模式，例如，在指定的内存排序效果下，原子的比较和设置变量的值，包含的方法有 compareAndSet、weakCompareAndSetPlain、weakCompareAndSet、weakCompareAndSetAcquire、weakCompareAndSetRelease、compareAndExchangeAcquire、compareAndExchange、compareAndExchangeRelease、getAndSet、getAndSetAcquire、getAndSetRelease 。

*   数值更新访问模式 (numeric atomic update access modes)

数字原子更新访问模式，例如，通过在指定的内存排序效果下添加变量的值，以原子方式获取和设置。 包含的方法有 getAndAdd、getAndAddAcquire、getAndAddRelease 。

*   按位原子更新访问模式 (bitwise atomic update access modes)

按位原子更新访问模式，例如，在指定的内存排序效果下，以原子方式获取和按位 OR 变量的值。 包含的方法有 getAndBitwiseOr、getAndBitwiseOrAcquire、getAndBitwiseOrRelease、 getAndBitwiseAnd、getAndBitwiseAndAcquire、getAndBitwiseAndRelease、getAndBitwiseXor、getAndBitwiseXorAcquire ， getAndBitwiseXorRelease 。

### **内存屏障**

`VarHandle` 除了支持各种访问模式下访问变量之外，还提供了一套内存屏障方法，目的是为了给内存排序提供更细粒度的控制。主要如下几个方法：

```
public static void fullFence() {
    UNSAFE.fullFence();
}
public static void acquireFence() {
    UNSAFE.loadFence();
}
public static void releaseFence() {
    UNSAFE.storeFence();
}
public static void loadLoadFence() {
    UNSAFE.loadLoadFence();
}
public static void storeStoreFence() {
    UNSAFE.storeStoreFence();
}
```

### **小结**

在 java9 之后，对一些变量的并发操作时，可以考虑用 `java.lang.invoke.VarHandle` 来处理，而不是通过 `Unsafe` 类来处理，毕竟 `Unsafe` 不太适合直接使用。

微信关注 JFound，发现更多 java 领域知识
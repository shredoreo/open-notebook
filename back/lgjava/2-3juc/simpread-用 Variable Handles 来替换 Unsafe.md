> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.dazhuanlan.com](https://www.dazhuanlan.com/babyvox/topics/1023137)

> 在 JDK9 中，包含了一个叫 Variable Handles 的新功能，下面是该功能的描述： Define a standard means to invoke the equivalents of......

在 JDK9 中，包含了一个叫 Variable Handles 的新功能，下面是该功能的描述：

> Define a standard means to invoke the equivalents of various java.util.concurrent.atomic and sun.misc.Unsafe operations upon object fields and array elements, a standard set of fence operations for fine-grained control of memory ordering, and a standard reachability-fence operation to ensure that a referenced object remains strongly reachable.

从这段官方描述中我们可以得知，Variable Handles 主要是提供`java.util.concurrent.atomic` 和 `sun.misc.Unsafe`相似的功能，但会更加安全和易用，并且在并发方面提高了性能。在 Java 并发包中，由于包中的类大量调用`sun.misc.Unsafe`提供的功能，官方现在已不推荐再使用`sun.misc.Unsafe`这个类了，所以看看这个功能十分有必要，例如在`AtomicIntegerArray`这个类中已经用 Variable Handles 替换了`sun.misc.Unsafe`，并且在 CAS 中也会用到。

说到 CAS 的应用，我们需要对某一字段进行原子更新，可能会用到以下几种方式：

1.  使用 AtomicInteger 类来更新，但会带来额外的内存消耗以及因引用替换带来的新的并发问题；
2.  使用 Atomic*FieldUpdater 类来完成字段的原子更新，其是基于反射来实现的，对字段和包装类型有一定的限制，操作开销比较大；
3.  使用`sun.misc.Unsafe`提供的 JVM 内置函数 API，这个类提供不安全的操作，损害安全性和移植性，官方不允许开发者使用，以后也会被替代掉。

变量句柄是对变量的类型化引用，它支持在各种访问模式下对变量的读写访问。支持的变量类型包括实例字段、静态字段和数组元素。下面我们来看看它的基础 API 如何使用。

创建 VarHandle 需要通过`MethodHandles`这个类调用其静态方法来实现，根据要访问类的不同类型的成员变量调用不同的静态方法：

*   `MethodHandles.lookup` 访问类非私有属性
*   `MethodHandles.privateLookupIn` 访问类的私有属性
*   `MethodHandles.arrayElementVarHandle` 访问类中的数组

获取到 Lookup，然后通过调用`findVarHandle`方法来获取`VarHandle`实例，在 JDK9 中，

*   `findVarHandle`：用于创建对象中非静态字段的 VarHandle。接收参数有三个，第一个为接收者的 Class 对象，第二个是字段名称，第三个是字段类型。
*   `findStaticVarHandle`：用于创建对象中静态字段的 VarHandle，接收参数与 findVarHandle 一致。
*   `unreflectVarHandle`：通过反射字段创建 VarHandle。

为了保证效率，VarHandle 类的实例通常需要被声明为 static final 变量（其实就是常量），这样可以在编译期对它进行优化。代码如下：

```
private static final VarHandle VH_TEST_FIELD_I;
private static final VarHandle VH_TEST_ARRAY;
private static final VarHandle VH_TEST_FIELD_J;

int i = 1;
int[] arr = new int[]{1, 2, 3};
private int j = 2;

static {
    try {
        VH_TEST_FIELD_I = MethodHandles.lookup()
                .in(Test.class)
                .findVarHandle(Test.class, "i", int.class);

        VH_TEST_ARRAY = MethodHandles.arrayElementVarHandle(int[].class);

        VH_TEST_FIELD_J = MethodHandles.privateLookupIn(Test.class, MethodHandles.lookup())
                .findVarHandle(Test.class, "j", int.class);
    } catch (ReflectiveOperationException e) {
        throw new Error(e);
    }
}
```

获取到了`VarHandle`实例，接下来可以做些什么呢？`VarHandle`提供了几种访问模式（access modes）：

1.  read access modes, such as reading a variable with volatile memory ordering effects;
2.  write access modes, such as updating a variable with release memory ordering effects;
3.  atomic update access modes, such as a compare-and-set on a variable with volatile memory order effects for both read and writing;
4.  numeric atomic update access modes, such as get-and-add with plain memory order effects for writing and acquire memory order effects for reading.
5.  bitwise atomic update access modes, such as get-and-bitwise-and with release memory order effects for writing and plain memory order effects for reading.

后面三个访问模式被称为 `read-modify-write`。从上面 5 个访问模式来看，主要包括普通变量读写、volatile 变量读写和 CAS 操作。示例代码如下：

```
public void () {
    
    System.out.println(VH_this_FIELD_I.get(this)); 
    System.out.println(VH_this_ARRAY.get(arr, 2)); 

    
    VH_this_FIELD_I.set(this, 99);
    System.out.println(VH_this_FIELD_I.get(this)); 

    VH_this_ARRAY.set(arr, 2, 4);
    System.out.println(VH_this_ARRAY.get(arr, 2)); 

    
    System.out.println(VH_this_FIELD_I.compareAndSet(this, 99, 3)); 
    System.out.println(VH_this_FIELD_I.get(this)); 

    System.out.println(VH_this_ARRAY.compareAndSet(arr, 2, 4, 8)); 
    System.out.println(VH_this_ARRAY.get(arr, 2)); 

    
    System.out.println(VH_this_FIELD_I.getAndAdd(this, 6)); 
    System.out.println(VH_this_FIELD_I.get(this)); 
}
```

除了上面的五中访问模式外，在 [JEP 193](http://openjdk.java.net/jeps/193) 的描述中，还提供了一组内存屏障（Memory fences）方法，为内存排序提供更细粒度的控制。其中涉及到`memory order`的概念，与最近的 c++ 11 内存模型一致，详情请参考其相关概念。

在想要利用 CAS 去实现我们的逻辑，首先推荐使用 VarHandle 来实现，它提供了精细粒度的 API，更加安全，相信在以后的产品中应用会非常广泛。

参考：

*   [JEP 193: Variable Handles](http://openjdk.java.net/jeps/193)
*   [VarHandle Doc](https://docs.oracle.com/javase/9/docs/api/java/lang/invoke/VarHandle.html)
*   [Java 9 变量句柄 - VarHandle](https://www.jianshu.com/p/e231042a52dd)
*   [当我们在谈论 memory order 的时候，我们在谈论什么](https://cloud.tencent.com/developer/article/1005903)
*   杨晓峰，AtomicInteger 底层实现原理是什么？如何在自己的产品代码中应用 CAS 操作？
*   [内存屏障](http://ifeve.com/memory-barriers-or-fences/)
*   [memory_order](https://zh.cppreference.com/w/cpp/atomic/memory_order)

[向本文提出修改或勘误建议](https://github.com/mstao/mstao.github.io/blob/hexo/source/_posts/use-VariableHandles-to-replace-Unsafe.md)
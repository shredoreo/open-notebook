> https://www.zhihu.com/question/40427344/answer/86545388



java7 在 JSR 292 中增加了对动态类型语言的支持，使 java 也可以像 C 语言那样将方法作为参数传递，其实现在 lava.lang.invoke 包中。MethodHandle 作用类似于反射中的 Method 类，但它比 Method 类要更加灵活和轻量级。通过 MethodHandle 进行方法调用一般需要以下几步：

（1）创建 MethodType 对象，指定方法的签名；

（2）在 MethodHandles.Lookup 中查找类型为 MethodType 的 MethodHandle；

（3）传入方法参数并调用 MethodHandle.invoke 或者 MethodHandle.invokeExact 方法。



## 1、MethodType

可以通过 MethodHandle 类的 type 方法查看其类型，返回值是 MethodType 类的对象。也可以在得到 MethodType 对象之后，调用 MethodHandle.asType(mt) 方法适配得到 MethodHandle 对象。可以通过调用 MethodType 的静态方法创建 MethodType 实例，有三种创建方式：

（1）methodType 及其重载方法：需要指定返回值类型以及 0 到多个参数；

（2）genericMethodType：需要指定参数的个数，类型都为 Object；

（3）fromMethodDescriptorString：通过方法描述来创建。

创建好 MethodType 对象后，还可以对其进行修改，MethodType 类中提供了一系列的修改方法，比如：changeParameterType、changeReturnType 等。



## 2、Lookup

MethodHandle.Lookup 相当于 MethodHandle 工厂类，通过 findxxx 方法可以得到相应的 MethodHandle，还可以配合反射 API 创建 MethodHandle，对应的方法有 unreflect、unreflectSpecial 等。



## 3、invoke

在得到 MethodHandle 后就可以进行方法调用了，有三种调用形式：

（1）invokeExact: 调用此方法与直接调用底层方法一样，需要做到参数类型精确匹配；

（2）invoke: 参数类型松散匹配，通过 asType 自动适配；

（3）invokeWithArguments: 直接通过方法参数来调用。其实现是先通过 genericMethodType 方法得到 MethodType，再通过 MethodHandle 的 asType 转换后得到一个新的 MethodHandle，最后通过新 MethodHandle 的 invokeExact 方法来完成调用。

## 4、MethodHandle

  MethodHandle是什么？简单的说就是方法句柄，通过这个句柄可以调用相应的方法。官方文档对其的解释为：

> “ A method handle is a typed, directly executable reference to an underlying method, constructor, field, or similar low-level operation, with optional transformations of arguments or return values. These transformations are quite general, and include such patterns as conversion, insertion, deletion, and substitution.”

翻译如下：

> 方法句柄是对底层方法、构造函数、字段或类似低级操作的类型化、直接可执行的引用，具有参数或返回值的可选转换。这些转换非常普遍，包括转换、插入、删除和替换等模式

常用的方法为invokexxx,如下图

![img](assets/simpread-Java 中 MethodHandle 的使用问题？/1195929-20190316204226233-920584901.png)

  其中需要注意的是`invoke`和`invokeExact`,前者在调用的时候可以进行返回值和参数的类型转换工作，而后者是精确匹配的。比如，在MethodType中指定的参数类型是`int`，如果你用`invoke`调用时指定的参数是`Integer`类型，方法调用是可以运行的，这是通过`MethodHandle`类的`astype`方法产生一个新的方法句柄。而如果用的是`invokeExact`则在运行时会报错。
  另外一个需要注意的是invokexxx的所有方法返回的是`Object`,调用时若有返回结果一般需进行强制类型转换。
  最后还有一点，如果调用的方法没有返回值，那么在`MethodType`的工厂方法中的返回值类型写为`void.class`



使用方法：

- mh.invokeXxx(instatnce, args)
- mh.bindTo(instance).invokeWithArguments(args)



## 示例代码：

```java
 
import java.lang.invoke.MethodHandle;
import java.lang.invoke.MethodHandles;
import java.lang.invoke.MethodType;
 
 

public class MHTest {
 
 
    public String toString(String s) {
 
        return "hello," + s + "MethodHandle";
    }
 
 
    public static void main(String[] args) {
        MHTest mhTest = new MHTest();
        MethodHandle mh = getToStringMH();  //获取方法句柄
       
        try {
            // 1.调用方法：
            String result = (String) mh.invokeExact(mhTest, "ssssss");  //根据方法句柄调用方法----注意返回值必须强转
            System.out.println(result);
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
 
 
        // 2.or like this:
        try {
            MethodHandle methodHandle2 = mh.bindTo(mhTest);
            String toString2 = (String) methodHandle2.invokeWithArguments("sssss");
            System.out.println(toString2);
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
 
 
        // 得到当前Class的不同表示方法，最后一个最好。一般我们在静态上下文用SLF4J得到logger用。
        System.out.println(MHTest.class);
        System.out.println(mhTest.getClass());
        System.out.println(MethodHandles.lookup().lookupClass()); // like getClass()
 
 
    }
 
    /**
     * 获取方法句柄
     * @return
     */
    public static MethodHandle getToStringMH() {
 
        MethodType mt = MethodType.methodType(String.class, String.class);  //获取方法类型 参数为:1.返回值类型,2方法中参数类型
 
        MethodHandle mh = null;
        try {
            mh = MethodHandles.lookup().findVirtual(MHTest.class, "toString", mt);  //查找方法句柄
        } catch (NoSuchMethodException | IllegalAccessException e) {
            e.printStackTrace();
        }
 
        return mh;
    }
}
```


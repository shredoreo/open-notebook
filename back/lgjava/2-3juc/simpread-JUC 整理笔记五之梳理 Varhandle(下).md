> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [zhuanlan.zhihu.com](https://zhuanlan.zhihu.com/p/145654924)

前文综合描述了 `Varhandle` 以及 `Varhandle` 能够做的事情，但是要了解并使用 `Varhandle` 并非是一件容易的事。总的来说，要想很好地使用 `Varhandle` ，必须先了解 **plain(普通方式)**、**opaque**、**release/acquire**、**volatile** 的区别及使用。

结合前面所学习的 **jcstress** ，本文用 jcsstress 作为并发测试工具来结合一些例子说明 plain、opaque、release/acqiure、volatile 的特性。

如果不知道 jcstress 的使用的话，可以参考下 **[JUC 整理笔记三之测试工具 jcstress](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzU2NjQ5MTAxNA%3D%3D%26mid%3D2247483750%26idx%3D2%26sn%3D7cbc365473cd806101350118afb1b333%26chksm%3Dfcaae38fcbdd6a99a1537914a7db12bfd5f836abd04b3886939ddaa158264d24e9f6811d7e42%26scene%3D21%23wechat_redirect)**

**内存可见性的区别**
------------

### **普通变量**

```
@JCStressTest(Mode.Termination)
@Outcome(id = &quot;STALE&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;TERMINATED&quot;, expect = Expect.ACCEPTABLE)
public static class PlainTester {
    private int x = 0;
    @Actor
    public void actor() {
        while (x == 0) {
            //do nothing
        }
    }
    @Signal
    public void signal() {
        x = 1;
    }
}
```

上面测试案例是有一个 while 循环，然后另外一个线程修改 **x** 的值来达到让循环终止的目的，其测试结果 **STALE**、**TERMINATED** 并行存在， 说明在测试案例中，是有存在修改 **x** 值后，循环是没有结束的案例。

结果表明，**多线程的环境中，普通变量是无法保证线程内存可见性的** 。

### **opaque**

```
@JCStressTest(Mode.Termination)
@Outcome(id = &quot;STALE&quot;, expect = Expect.FORBIDDEN)
@Outcome(id = &quot;TERMINATED&quot;, expect = Expect.ACCEPTABLE)
public static class OpaqueTester {
    private volatile int x = 0;
    private static final VarHandle X; d
    static {
        try {
            X = MethodHandles.lookup().findVarHandle(OpaqueTester.class, &quot;x&quot;, int.class);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new Error(e);
        }
    }

    @Actor
    public void actor() {
        while ((int) X.getOpaque(this) == 0) {
            //do nothing
        }
    }
    @Signal
    public void signal() {
        X.setOpaque(this, 1);
    }
}
```

这里使用了 **Varhanle** 来测试 opaque 的功能，测试的 **Outcome** 是不存在 **STALE** 。

结果表明，**多线程环境中， opaque 是可以保存内存可见性的** 。

### **release/acquire**

```
@JCStressTest(Mode.Termination)
@Outcome(id = &quot;STALE&quot;, expect = Expect.FORBIDDEN)
@Outcome(id = &quot;TERMINATED&quot;, expect = Expect.ACCEPTABLE)
public static class ReleaseAcquireTester {
    private volatile int x = 0;
    private static final VarHandle X;
    static {
        try {
            X = MethodHandles.lookup().findVarHandle(ReleaseAcquireTester.class, &quot;x&quot;, int.class);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new Error(e);
        }
    }
    @Actor
    public void actor() {
        while ((int) X.getAcquire(this) == 0) {
            //do nothing
        }
    }
    @Signal
    public void signal() {
        X.setRelease(this, 1);
    }
}
```

该例子和 opaque 一样，也是不存在 **STALE**

结果表明，**多线程环境下，release/acquire 是可以保证内存可见性的** 。

### **volatile**

```
@JCStressTest(Mode.Termination)
@Outcome(id = &quot;STALE&quot;, expect = Expect.FORBIDDEN)
@Outcome(id = &quot;TERMINATED&quot;, expect = Expect.ACCEPTABLE)
public static class Volatile2Tester {
    private int x = 0;
    private static final VarHandle X;
    static {
        try {
            X = MethodHandles.lookup().findVarHandle(Volatile2Tester.class, &quot;x&quot;, int.class);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new Error(e);
        }
    }
    @Actor
    public void actor() {
        while ((int) X.getVolatile(this) == 0) {
            //do nothing
        }
    }
    @Signal
    public void signal() {
        X.setVolatile(this, 1);
    }
}
```

这里的 volatile 是借助 Varhandle 来测试，用法和在变量前加 **volatile** 关键字是一样的。

和前面的两个例子一样，也是不存在 **STALE**

结果表明，**多线程环境下 volatile 是可以保证内存可见性的** 。

### **小结**

普通变量是不保证内存可见性的，opaque 、 release/acquire 、volatile 是可以保证内存可见性。

**特性区别**
--------

在测试之前，先准备下测试实体类

```
package jfound;
import java.lang.invoke.MethodHandles;
import java.lang.invoke.VarHandle;
public class TestData {
    public int x;
    public int y;
    public static final VarHandle X;
    public static final VarHandle Y;
    static {
        try {
            X = MethodHandles.lookup().findVarHandle(TestData.class, &quot;x&quot;, int.class);
            Y = MethodHandles.lookup().findVarHandle(TestData.class, &quot;x&quot;, int.class);
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new Error(e);
        }
    }
}
```

### **普通变量**

```
@JCStressTest
@Outcome(id = &quot;0, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;0, 1&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 1&quot;, expect = Expect.ACCEPTABLE)
@State
public static class PlainOrderTester {
    private final TestData testData = new TestData();
    @Actor
    public void actor1(II_Result r) {
        testData.x = 1;
        r.r2 = testData.y;
    }
    @Actor
    public void actor2(II_Result r) {
        testData.y = 1;
        r.r1 = testData.x;
    }
}
```

上面的测试案例中，存在 “ **0, 0** ” 这个结果，说明代码中出现了重排序。

### **opaque**

Varhandle 中对 getOpaque 、 setOpaque 的说明是，**按程序顺序执行, 不确保其他线程可见顺序**。

*   测试一

```
/*
 * 不确保顺序，存在 0, 0 这结果
 */
@JCStressTest
@Outcome(id = &quot;0, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;0, 1&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 1&quot;, expect = Expect.ACCEPTABLE)
@State
public static class OpaqueOrderTester1 {
    private final TestData testData = new TestData();
    @Actor
    public void actor1(II_Result r) {
        TestData.X.setOpaque(testData, 1);
        r.r2 = (int) TestData.Y.getOpaque(testData);
    }
    @Actor
    public void actor2(II_Result r) {
        TestData.Y.setOpaque(testData, 1);
        r.r1 = (int) TestData.X.getOpaque(testData);
    }
}
/**
 * 不确保顺序，存在 1, 1 这结果
 */
@JCStressTest
@Outcome(id = &quot;0, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;0, 1&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 1&quot;, expect = Expect.ACCEPTABLE)
@State
public static class OpaqueOrderTester2 {
    private final TestData testData = new TestData();
    @Actor
    public void actor1(II_Result r) {
        r.r2 = (int) TestData.Y.getOpaque(testData);
        TestData.X.setOpaque(testData, 1);
    }
    @Actor
    public void actor2(II_Result r) {
        r.r1 = (int) TestData.X.getOpaque(testData);
        TestData.Y.setOpaque(testData, 1);
    }
}
```

结论: 结合前面 opaque 是保证可见性的结论，根据上面的例子可以得出，**opaque 不保证其它线程的可见顺序**。

*   测试二

```
/**
 * 不存在 1, 0 ，相对普通变量来说，也是按顺序执行
 */
@JCStressTest
@Outcome(id = &quot;1, 1&quot;, expect = ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = FORBIDDEN)
@Outcome(id = &quot;0, 1&quot;, expect = ACCEPTABLE)
@Outcome(id = &quot;0, 0&quot;, expect = ACCEPTABLE)
@State
public static class OpaqueOrderTester3 {
    private final TestData testData = new TestData();
    @Actor
    public void actor1() {
        testData.y = 1;
        TestData.X.setOpaque(testData, 1);
    }
    @Actor
    public void actor2(II_Result r) {
        r.r1 = (int) TestData.X.getOpaque(testData);
        r.r2 = testData.y;
    }
}

/**
 * 不存在 1, 0 ，说明 setOpaque 和 getOpaque 都是按顺序执行的
 */
@JCStressTest
@Outcome(id = &quot;1, 1&quot;, expect = ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = FORBIDDEN)
@Outcome(id = &quot;0, 1&quot;, expect = ACCEPTABLE)
@Outcome(id = &quot;0, 0&quot;, expect = ACCEPTABLE)
@State
public static class OpaqueOrderTester4 {
    private final TestData testData = new TestData();
    @Actor
    public void actor1() {
        TestData.Y.setOpaque(testData, 1);
        TestData.X.setOpaque(testData, 1);
    }
    @Actor
    public void actor2(II_Result r) {
        r.r1 = (int) TestData.X.getOpaque(testData);
        r.r2 = (int) TestData.Y.getOpaque(testData);
    }
}
```

结论：上面两个例子都是没有 `1, 0` 这个例子，说明在 `load` 、`store` 情况下， opaque 都是能够保证执行顺序的。

### **release/acquire**

*   测试一

```
@JCStressTest
@Outcome(id = &quot;1, 1&quot;, expect = ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = FORBIDDEN)
@Outcome(id = &quot;0, 1&quot;, expect = ACCEPTABLE)
@Outcome(id = &quot;0, 0&quot;, expect = ACCEPTABLE)
@State
public static class RAOrderTester1 {
    private final TestData testData = new TestData();
    @Actor
    public void actor1() {
        testData.y = 1;
        TestData.X.setRelease(testData, 1);
    }
    @Actor
    public void actor2(II_Result r) {
        r.r1 = (int) TestData.X.getAcquire(testData);
        r.r2 = testData.y;
    }
}
```

结论：不存在`1, 0` 这个结果，说明是 release/acquire 是可以按顺序执行的

*   测试二

```
/**
 * 不存在1, 1，不存在重排序
 */
@JCStressTest
@Outcome(id = &quot;0, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;0, 1&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 1&quot;, expect = Expect.FORBIDDEN)
@State
public static class RAOrderTester2 {
    private final TestData testData = new TestData();
    @Actor
    public void actor1(II_Result r) {
        r.r2 = (int) TestData.Y.getAcquire(testData);
        TestData.X.setRelease(testData, 1);
    }
    @Actor
    public void actor2(II_Result r) {
        r.r1 = (int) TestData.X.getAcquire(testData);
        TestData.Y.setRelease(testData, 1);
    }
}

/**
 * 出现 0, 0 则表示被重排序
 */
@JCStressTest
@Outcome(id = &quot;0, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 0&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;0, 1&quot;, expect = Expect.ACCEPTABLE)
@Outcome(id = &quot;1, 1&quot;, expect = Expect.ACCEPTABLE)
@State
public static class RAOrderTester3 {
    private final TestData testData = new TestData();
    @Actor
    public void actor1(II_Result r) {
        TestData.X.setRelease(testData, 1);
        r.r2 = (int) TestData.Y.getAcquire(testData);
    }
    @Actor
    public void actor2(II_Result r) {
        TestData.Y.setRelease(testData, 1);
        r.r1 = (int) TestData.X.getAcquire(testData);
    }
}
```

结论：setRelease 确保前面的 load 和 store 不会被重排序到后面，但不确保后面的 load 和 store 重排序到前面；getAcquire 确保后面的 load 和 store 不会被重排序到前面，但不确保前面的 load 和 store 被重排序。

### **volatile**

*   测试

```
/**
  * 没有出现 0, 0
  */
 @JCStressTest
 @Outcome(id = &quot;1, 1&quot;, expect = ACCEPTABLE)
 @Outcome(id = &quot;1, 0&quot;, expect = ACCEPTABLE)
 @Outcome(id = &quot;0, 1&quot;, expect = ACCEPTABLE)
 @State
 public static class VolatileTester1 {

     private final TestData testData = new TestData();


     @Actor
     public void actor1(II_Result r) {
         TestData.X.setVolatile(testData, 1);
         r.r2 = (int) TestData.Y.getVolatile(testData);
     }

     @Actor
     public void actor2(II_Result r) {
         TestData.Y.setVolatile(testData, 1);
         r.r1 = (int) TestData.X.getVolatile(testData);
     }
 }


 /**
  * 没有出现 1, 0
  */
 @JCStressTest
 @Outcome(id = &quot;1, 1&quot;, expect = ACCEPTABLE)
 @Outcome(id = &quot;1, 0&quot;, expect = FORBIDDEN)
 @Outcome(id = &quot;0, 1&quot;, expect = ACCEPTABLE)
 @Outcome(id = &quot;0, 0&quot;, expect = ACCEPTABLE)
 @State
 public static class VolatileTester2 {

     private final TestData testData = new TestData();

     @Actor
     public void actor1() {
         testData.y = 1;
         TestData.X.setVolatile(testData, 1);
     }

     @Actor
     public void actor2(II_Result r) {
         r.r1 = (int) TestData.X.getVolatile(testData);
         r.r2 = testData.y;
     }
 }
```

结论： volatile 之间操作是能够确保顺序的，能保证变量之间的不被重排序

**总结**
------

前面的几个例子，说明了普通变量、opaque、release/acquire、volatile 之间的区别

*   普通变量是不确保内存可见的，opaque、release/acquire、volatile 是可以保证内存可见的
*   opaque 确保程序执行顺序，但不保证其它线程的可见顺序
*   release/acquire 保证程序执行顺序，setRelease 确保前面的 load 和 store 不会被重排序到后面，但不确保后面的 load 和 store 重排序到前面；getAcquire 确保后面的 load 和 store 不会被重排序到前面，但不确保前面的 load 和 store 被重排序。
*   volatile 确保程序执行顺序，能保证变量之间的不被重排序。

代码地址：[https://github.com/jfound/varhandle](https://link.zhihu.com/?target=https%3A//github.com/jfound/varhandle)
> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/8a44d4a819bc)

这里我们就从 AtomicBoolean 开始说吧，自己正好也复习一下。对于官方的说明是：

可以用原子方式更新的 boolean 值。有关原子变量属性的描述，请参阅 java.util.concurrent.atomic  
包规范。AtomicBoolean 可用在应用程序中（如以原子方式更新的标志），但不能用于替换 Boolean。

换一句话说，Atomic 就是原子性的意思，即能够保证在高并发的情况下只有一个线程能够访问这个属性值。（类似我们之前所说的 volatile）

一般情况下，我们使用 AtomicBoolean 高效并发处理 “只初始化一次” 的功能要求：

```
private static AtomicBoolean initialized = new AtomicBoolean(false);
public void init()
{
   if( initialized.compareAndSet(false, true) )
   {
       // 这里放置初始化代码....
   }
}
```

如果没有 AtomicBoolean，我们可以使用 volatile 做如下操作：

```
public static volatile initialized = false;
public void init()
{
    if( initialized == false ){
        initialized = true;
        // 这里初始化代码....
    }
}
```

既然如此神奇，那么我们看看 AtomicBoolean 的源码时如何实现的，查看源码如下：

```
public class AtomicBoolean implements java.io.Serializable {
    private static final long serialVersionUID = 4654671469794556979L;
    // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;

    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                (AtomicBoolean.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }

    private volatile int value;

    /**
     * Creates a new {@code AtomicBoolean} with the given initial value.
     *
     * @param initialValue the initial value
     */
    public AtomicBoolean(boolean initialValue) {
        value = initialValue ? 1 : 0;
    }

    /**
     * Creates a new {@code AtomicBoolean} with initial value {@code false}.
     */
    public AtomicBoolean() {
    }

    /**
     * Returns the current value.
     *
     * @return the current value
     */
    public final boolean get() {
        return value != 0;
    }

    /**
     * Atomically sets the value to the given updated value
     * if the current value {@code ==} the expected value.
     *
     * @param expect the expected value
     * @param update the new value
     * @return {@code true} if successful. False return indicates that
     * the actual value was not equal to the expected value.
     */
    public final boolean compareAndSet(boolean expect, boolean update) {
        int e = expect ? 1 : 0;
        int u = update ? 1 : 0;
        return unsafe.compareAndSwapInt(this, valueOffset, e, u);
    }

    /**
     * Atomically sets the value to the given updated value
     * if the current value {@code ==} the expected value.
     *
     * <p><a href="package-summary.html#weakCompareAndSet">May fail
     * spuriously and does not provide ordering guarantees</a>, so is
     * only rarely an appropriate alternative to {@code compareAndSet}.
     *
     * @param expect the expected value
     * @param update the new value
     * @return {@code true} if successful
     */
    public boolean weakCompareAndSet(boolean expect, boolean update) {
        int e = expect ? 1 : 0;
        int u = update ? 1 : 0;
        return unsafe.compareAndSwapInt(this, valueOffset, e, u);
    }

    /**
     * Unconditionally sets to the given value.
     *
     * @param newValue the new value
     */
    public final void set(boolean newValue) {
        value = newValue ? 1 : 0;
    }

    /**
     * Eventually sets to the given value.
     *
     * @param newValue the new value
     * @since 1.6
     */
    public final void lazySet(boolean newValue) {
        int v = newValue ? 1 : 0;
        unsafe.putOrderedInt(this, valueOffset, v);
    }

    /**
     * Atomically sets to the given value and returns the previous value.
     *
     * @param newValue the new value
     * @return the previous value
     */
    public final boolean getAndSet(boolean newValue) {
        boolean prev;
        do {
            prev = get();
        } while (!compareAndSet(prev, newValue));
        return prev;
    }

    /**
     * Returns the String representation of the current value.
     * @return the String representation of the current value
     */
    public String toString() {
        return Boolean.toString(get());
    }

}
```

你猜的没错，AtomicBoolean 就是使用了 Volatile 属性来完成的。

Java6 以后出现的很多的原子行的类，除了上述我们所说的 AtomicBoolean 以外，AtomicBoolean 家族还是比较强大的，后面我们有时间在一一介绍。包括：

基本类：  
AtomicInteger、AtomicLong、AtomicBoolean；  
引用类型：  
AtomicReference、AtomicReference 的 ABA 实例、AtomicStampedRerence、AtomicMarkableReference；  
数组类型：  
AtomicIntegerArray、AtomicLongArray、AtomicReferenceArray  
属性原子修改器（Updater）：  
AtomicIntegerFieldUpdater、AtomicLongFieldUpdater、AtomicReferenceFieldUpdater
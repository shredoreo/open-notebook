> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/u010853261/article/details/77940767)

> 本文主要是分析 Spring bean 的循环依赖，以及 Spring 的解决方式。 通过这种解决方式，我们可以应用在我们实际开发项目中。
> 
> 1.  什么是循环依赖？
> 2.  怎么检测循环依赖
> 3.  Spring 怎么解决循环依赖
> 4.  Spring 对于循环依赖无法解决的场景
> 5.  Spring 解决循环依赖的方式我们能够学到什么？

1. 什么是循环依赖？
===========

循环依赖其实就是循环引用，也就是两个或则两个以上的 bean 互相持有对方，最终形成闭环。比如 A 依赖于 B，B 依赖于 C，C 又依赖于 A。如下图：

![](https://img-blog.csdn.net/20170912082357749?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDg1MzI2MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

注意，这里不是函数的循环调用，是对象的相互依赖关系。循环调用其实就是一个死循环，除非有终结条件。

Spring 中循环依赖场景有：  
（1）构造器的循环依赖  
（2）field 属性的循环依赖。

2. 怎么检测是否存在循环依赖
===============

检测循环依赖相对比较容易，Bean 在创建的时候可以给该 Bean 打标，如果递归调用回来发现正在创建中的话，即说明了循环依赖了。

3. Spring 怎么解决循环依赖
==================

Spring 的循环依赖的理论依据其实是基于 Java 的引用传递，当我们获取到对象的引用时，对象的 field 或则属性是可以延后设置的 (但是构造器必须是在获取引用之前)。

Spring 的单例对象的初始化主要分为三步：  
![](https://img-blog.csdn.net/20170912091609918?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDg1MzI2MQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)  
（1）createBeanInstance：实例化，其实也就是调用对象的构造方法实例化对象

（2）populateBean：填充属性，这一步主要是多 bean 的依赖属性进行填充

（3）initializeBean：调用 spring xml 中的 init 方法。

从上面讲述的单例 bean 初始化步骤我们可以知道，循环依赖主要发生在第一、第二部。也就是构造器循环依赖和 field 循环依赖。

那么我们要解决循环引用也应该从初始化过程着手，对于单例来说，在 Spring 容器整个生命周期内，有且只有一个对象，所以很容易想到这个对象应该存在 Cache 中，Spring 为了解决单例的循环依赖问题，使用了**三级缓存**。

首先我们看源码，三级缓存主要指：

```
/** Cache of singleton objects: bean name --> bean instance */
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);

/** Cache of singleton factories: bean name --> ObjectFactory */
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);

/** Cache of early singleton objects: bean name --> bean instance */
private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
```

这三级缓存分别指：  
singletonFactories ： 单例对象工厂的 cache  
earlySingletonObjects ：提前暴光的单例对象的 Cache  
singletonObjects：单例对象的 cache

我们在创建 bean 的时候，首先想到的是从 cache 中获取这个单例的 bean，这个缓存就是 singletonObjects。主要调用方法就就是：

```
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    Object singletonObject = this.singletonObjects.get(beanName);
    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
        synchronized (this.singletonObjects) {
            singletonObject = this.earlySingletonObjects.get(beanName);
            if (singletonObject == null && allowEarlyReference) {
                ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                if (singletonFactory != null) {
                    singletonObject = singletonFactory.getObject();
                    this.earlySingletonObjects.put(beanName, singletonObject);
                    this.singletonFactories.remove(beanName);
                }
            }
        }
    }
    return (singletonObject != NULL_OBJECT ? singletonObject : null);
}
```

上面的代码需要解释两个参数：

*   isSingletonCurrentlyInCreation() 判断当前单例 bean 是否正在创建中，也就是没有初始化完成 (比如 A 的构造器依赖了 B 对象所以得先去创建 B 对象， 或则在 A 的 populateBean 过程中依赖了 B 对象，得先去创建 B 对象，这时的 A 就是处于创建中的状态。)
*   allowEarlyReference 是否允许从 singletonFactories 中通过 getObject 拿到对象

分析 getSingleton() 的整个过程，Spring 首先从一级缓存 singletonObjects 中获取。如果获取不到，并且对象正在创建中，就再从二级缓存 earlySingletonObjects 中获取。如果还是获取不到且允许 singletonFactories 通过 getObject() 获取，就从三级缓存 singletonFactory.getObject()(三级缓存) 获取，如果获取到了则：

```
this.earlySingletonObjects.put(beanName, singletonObject);
                        this.singletonFactories.remove(beanName);
```

从 singletonFactories 中移除，并放入 earlySingletonObjects 中。其实也就是从三级缓存移动到了二级缓存。

从上面三级缓存的分析，我们可以知道，Spring 解决循环依赖的诀窍就在于 singletonFactories 这个三级 cache。这个 cache 的类型是 ObjectFactory，定义如下：

```
public interface ObjectFactory<T> {
    T getObject() throws BeansException;
}
```

这个接口在下面被引用

```
protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
    Assert.notNull(singletonFactory, "Singleton factory must not be null");
    synchronized (this.singletonObjects) {
        if (!this.singletonObjects.containsKey(beanName)) {
            this.singletonFactories.put(beanName, singletonFactory);
            this.earlySingletonObjects.remove(beanName);
            this.registeredSingletons.add(beanName);
        }
    }
}
```

这里就是解决循环依赖的关键，这段代码发生在 createBeanInstance 之后，也就是说单例对象此时已经被创建出来 (调用了构造器)。这个对象已经被生产出来了，虽然还不完美（还没有进行初始化的第二步和第三步），但是已经能被人认出来了（根据对象引用能定位到堆中的对象），所以 Spring 此时将这个对象提前曝光出来让大家认识，让大家使用。

这样做有什么好处呢？让我们来分析一下 “A 的某个 field 或者 setter 依赖了 B 的实例对象，同时 B 的某个 field 或者 setter 依赖了 A 的实例对象” 这种循环依赖的情况。A 首先完成了初始化的第一步，并且将自己提前曝光到 singletonFactories 中，此时进行初始化的第二步，发现自己依赖对象 B，此时就尝试去 get(B)，发现 B 还没有被 create，所以走 create 流程，B 在初始化第一步的时候发现自己依赖了对象 A，于是尝试 get(A)，尝试一级缓存 singletonObjects(肯定没有，因为 A 还没初始化完全)，尝试二级缓存 earlySingletonObjects（也没有），尝试三级缓存 singletonFactories，由于 A 通过 ObjectFactory 将自己提前曝光了，所以 B 能够通过 ObjectFactory.getObject 拿到 A 对象(虽然 A 还没有初始化完全，但是总比没有好呀)，B 拿到 A 对象后顺利完成了初始化阶段 1、2、3，完全初始化之后将自己放入到一级缓存 singletonObjects 中。此时返回 A 中，A 此时能拿到 B 的对象顺利完成自己的初始化阶段 2、3，最终 A 也完成了初始化，进去了一级缓存 singletonObjects 中，而且更加幸运的是，由于 B 拿到了 A 的对象引用，所以 B 现在 hold 住的 A 对象完成了初始化。

知道了这个原理时候，肯定就知道为啥 Spring 不能解决 “A 的构造方法中依赖了 B 的实例对象，同时 B 的构造方法中依赖了 A 的实例对象” 这类问题了！因为加入 singletonFactories 三级缓存的前提是执行了构造器，所以构造器的循环依赖没法解决。

参考 [http://www.jianshu.com/p/6c359768b1dc](http://www.jianshu.com/p/6c359768b1dc)
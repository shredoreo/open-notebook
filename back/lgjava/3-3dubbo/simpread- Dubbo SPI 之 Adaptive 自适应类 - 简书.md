> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/f9e5ed9f7737)

> 翻看 `Dubbo` 的源码，不难发现，框架到处都在用 `SPI` 机制进行扩展。这是由于 `Dubbo` 框架对各种层做了很多的实现方式，然后由用户自己去选择具体的实现方式。比如 `Protocol`，`Dubbo` 提供的实现就有 `dubbo`，`hessian`，`thrift`，`redis`，`inJvm` 等等。这么多的实现方式，`Dubbo` 是如何做到动态的切换的呢？

接口自适应与 @Adaptive 注解
-------------------

> `Dubbo` 没有采用 `JDK` 提供的 `SPI` 机制，而是自己实现了一套，增强了很多功能。具体细节可以浏览网上其他博客。

`Dubbo` 充分利用面向对象思想，每一个组件内引入其他组件都是以接口的形式进行依赖，动态的 inject 实现类。所以这种思想上也用到了 `AOP` 和 `DI` 的思想。  
我们定义一个接口 `Registry`，假定它的功能是将本地服务暴露到注册中心，以及从注册中心获取可用服务，属于 `RPC` 框架中的服务注册与发现组件。

```
@SPI("zookeeper")
public interface Registry {
    /**
     * 注册服务
     */
    @Adaptive()
    String register(URL url, String content);
    /**
     * 发现服务
     */
    @Adaptive()
    String discovery(URL url, String content);
}
```

*   `@SPI` 注解标注这是一个可扩展的组件，注解内的内容后文详细介绍。
*   该接口定义了两个方法 `register`和 `discovery`，分别代表注册服务和发现服务。两个方法中都有 `URL` 参数，该类是 `Dubbo` 内置的类，代表了 `Dubbo` 整个执行过程中的上下文信息，包括各类配置信息，参数等。`content` 代表备注信息。
*   `@Adaptive()`注解表明这两个方法都是自适应方法，具体作用后文分析。

下面分别是两个实现类 `ZookeeperRegistry` 、`EtcdRegistry`

ZookeeperRegistry

```
public class ZookeeperRegistry implements Registry {
    private Logger logger = LoggerFactory.getLogger(ZookeeperRegistry.class);

    @Override
    public String register(URL url, String content) {
        logger.info("服务: {} 已注册到zookeeper上，备注: {}", url.getParameter("service"), content);

        return "Zookeeper register already! ";
    }

    @Override
    public String discovery(URL url, String content) {
        logger.info("zookeeper上发现服务: {} , 备注: {}", url.getParameter("service"), content);

        return "Zookeeper discovery already! ";
    }
}
```

EtcdRegistry

```
public class EtcdRegistry implements Registry {

    private Logger logger = LoggerFactory.getLogger(ZookeeperRegistry.class);

    @Override
    public String register(URL url, String content) {
        logger.info("服务: {} 已注册到 Etcd 上，备注: {}", url.getParameter("service"), content);

        return "Etcd register already! ";
    }

    @Override
    public String discovery(URL url, String content) {
        logger.info("Etcd 上发现服务: {} , 备注: {}", url.getParameter("service"), content);

        return "Etcd discovery already! ";
    }
}
```

我们可以将服务注册信息注册到老牌注册中心 `zookeeper` 上，或者使用新兴流行轻量级注册中心 `etcd` 上。

配置扩展点实现信息到 `Resource` 目录下
-------------------------

在 `Resource` 下的 `META-INF.dubbo` 下新建 以`Registry` 全限定名为名的文件，配置实现类信息，以 `key-value` 的形式。（这里要注意与 `JDK` 默认的 `SPI` 机制的区别）  

![](http://upload-images.jianshu.io/upload_images/6393906-bf53247f15fb966d.png) spi.png

文件内内容如下:

```
etcd=com.maple.spi.impl.EtcdRegistry
zookeeper=com.maple.spi.impl.ZookeeperRegistry
```

测试 `Dubbo SPI` 自适应类
-------------------

```
public class Main {
    public static void main(String[] args) {
        URL url = URL.valueOf("test://localhost/test")
                      .addParameter("service", "helloService");

        Registry registry = ExtensionLoader.getExtensionLoader(Registry.class)
                                           .getAdaptiveExtension();
        String register = registry.register(url, "maple");

        System.out.println(register);
    }
}
```

该程序首先通过 `Registry` 接口得到它专属的 `ExtensionLoader` 实例，然后调用 `getAdaptiveExtension` 拿到该接口的自适应类。`Dubbo` 会判断是否有实现类 (即实现了 `Registry` 接口) 上有注解 `@Adaptive`，如果没有就会动态生成。本例子将会动态生成。

直接运行程序结果如下, 程序最终选择的实现类是 `ZookeeperRegistry`，控制台结果如下:

```
09-20 23:43:13 323 main INFO - 服务: helloService 已注册到zookeeper上，备注: maple
Zookeeper register already!
```

上面代码我们没有看到任何实现类的信息，`Dubbo SPI` 机制会为动态的去调用实现类。

我们重点分析 `getAdaptiveExtension`方法找到的是 `Registry` 的自适应类，可以理解为是 `Registry` 的一个 适配器和代理类。如果该适配器类不存在，`Dubbo` 会通过动态代理方式在运行时自动生成一个自适应类。

打开 `DEBUG` 日志，在控制台我们看到了 `Dubbo` 生成的类的源码如下：

```
package com.maple.spi;

import com.alibaba.dubbo.common.extension.ExtensionLoader;

public class Registry$Adaptive implements com.maple.spi.Registry {
    public java.lang.String register(com.alibaba.dubbo.common.URL arg0, java.lang.String arg1) {
        if (arg0 == null) throw new IllegalArgumentException("url == null");
        com.alibaba.dubbo.common.URL url = arg0;
        String extName = url.getParameter("registry", "zookeeper");
        if (extName == null)
            throw new IllegalStateException("Fail to get extension(com.maple.spi.Registry) name from url(" + url.toString() + ") use keys([registry])");
        com.maple.spi.Registry extension = (com.maple.spi.Registry) ExtensionLoader.getExtensionLoader(com.maple.spi.Registry.class).getExtension(extName);
        return extension.register(arg0, arg1);
    }

    public java.lang.String discovery(com.alibaba.dubbo.common.URL arg0, java.lang.String arg1) {
        if (arg0 == null) throw new IllegalArgumentException("url == null");
        com.alibaba.dubbo.common.URL url = arg0;
        String extName = url.getParameter("registry", "zookeeper");
        if (extName == null)
            throw new IllegalStateException("Fail to get extension(com.maple.spi.Registry) name from url(" + url.toString() + ") use keys([registry])");
        com.maple.spi.Registry extension = (com.maple.spi.Registry) ExtensionLoader.getExtensionLoader(com.maple.spi.Registry.class).getExtension(extName);
        return extension.discovery(arg0, arg1);
    }
}
```

看代码，该适配器类的作用是类似于 `AOP` 的功能，再调用具体的实现类之前，先通`ExtensionLoader.getExtensionLoader(Registry.class).getExtension(extName);`  
根据 `extName` 去 `loader` 具体的实现类，然后再去调用实现类的相应的方法。

分析上面代码中的一句:

```
String extName = url.getParameter("registry", "zookeeper");
```

`extName` 可以通过 `url` 进行传递，默认值为 `zookeeper`, 该默认值即为我们定义的接口上的注解 `@SPI` 里的内容，上文我们定义的 `@SPI("zookeeper")`，所以这里的默认值为 `zookeeper`, 当 `url` 中没有对应的参数时，我们会去拿默认值。

我们可以修改 `Main` 测试程序，增加 `key` 为 `registry` 的 `parameter`

```
public static void main(String[] args) {
        URL url = URL.valueOf("test://localhost/test").addParameter("service", "helloService")
                .addParameter("registry","etcd");

        Registry registry = ExtensionLoader.getExtensionLoader(Registry.class).getAdaptiveExtension();

        String register = registry.register(url, "maple");

        System.out.println(register);

    }
```

在 `URL` 中增加 `Key`，并设置值为 `etcd`，运行程序，结果如下：

```
09-20 23:44:00 009 main INFO - 服务: helloService 已注册到 Etcd 上，备注: maple
Etcd register already!
```

实现类已经切换为 `EtcdRegistry` 了。

### @Adaptive 注意细节

@Adaptive 源码

```
public @interface Adaptive {
    String[] value() default {};
}
```

细心的读者已经发现了 `@Adaptive` 有`value`这个属性。上文在接口方法上定义的 `@Adaptive` 是没有设置值的。如果没有定义值，`Dubbo` 默认会使用一种策略生成。这种策略是将类名定义的驼峰法则转换为小写，并以 `.`号区分。 例如上文的接口名为 `Registry`，那么这个`Key` 值就是 `registry`。如果接口名为 `HelloWorld`，`Key` 值就为 `hello.world`。

当然如果 `@Adaptive` 是有值的话，优先按里面的这个值来作为 `Key`，例如 `Dubbo` 框架中的接口 `RegistryFactory` , 该接口的自适应类将会从 `URL` 以 `protocol` 为 `key` 来找实现类的 `extName`。

```
@SPI("dubbo")
public interface RegistryFactory {
   @Adaptive({"protocol"})
    Registry getRegistry(URL url);
}
```

### 总结

`Dubbo Adaptive` 模式在整个框架中运用十分广泛，如果用户没有在 `URL` 中进行自定义，`Dubbo` 默认会去加载扩展点接口上 `@SPI` 标注的内容, 如果此注解没有值，那么我们就必须要在 `URL` 中进行值的传递了。如果我们想覆盖 `Dubbo` 默认的实现策略。可以通过在 `URL` 中增加 `key-value` 的形式来改变。

这样一个组件充分体现了设计模式, 对修改关闭，对扩展开放的原则。当我们想自己实现 `Dubbo` 中的某个组件时，我们完全可以通过 `Dubbo Adaptive` 来动态的切换程序使用我们提供的组件。

彻底理解 `Dubbo SPI` 模式，以及 `Adaptive` 自适应类, `Activate` 激活类等，是看 `Dubbo` 源码的基础。如果我们想十分流畅的去分析 `Dubbo` 内部其他组件的实现机制，第一道要跨过的坎便是 `Dubbo SPI`。
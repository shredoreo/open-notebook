# 动态代理（dynamic proxy）

​           利用Java的反射技术(Java Reflection)，在运行时创建一个实现某些给定接口的新类（也称“动态代理类”）及其实例（对象）,代理的是接口(Interfaces)，不是类(Class)，也不是抽象类。在运行时才知道具体的实现，spring aop就是此原理。

# Proxy.newProxyInstance

先从如何创建代理对象说起，通过Proxy.newProxyInstance创建，需要传入3个参数

- lassLoader loader 类加载器
- Class<?>[] interfaces 代理类需要实现的接口
- InvocationHandler h 代理方法执行处理器

```java
public static Object newProxyInstance(ClassLoader loader,
                                      Class<?>[] interfaces,
                                      InvocationHandler h) {
```

由此可见，创建一个代理对象，首先，被代理对象需要实现接口，且需要构造一个InvocationHandler对象。

## 1、创建被代理对象

那么先创建一个接口、实现类，它的实例将作为被代理对象

```java
public interface Learning {
    void learn();
}


public class People implements Learning{
    @Override
    public void learn() {
        System.out.println("I'm learning ");
    }
}


```

## 2、代理方法执行处理器

InvocationHandler 接口有一个方法，传入三个参数：

proxy：就是代理对象，newProxyInstance方法的返回对象
method：调用的方法
args: 方法中的参数

```java
public interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args)
        throws Throwable;
}
```

实现 ：

```java
public class LearningInvocationHandler implements InvocationHandler {

    private final Learning learning;

    /**
     * 通过构造器存储被代理对象
     * @param learning
     */
    public LearningInvocationHandler(Learning learning) {
        this.learning = learning;
    }

    /**
     * 代理方法
     * @param proxy 代理对象，newProxyInstance方法的返回对象
     * @param method 调用的方法
     * @param args 方法中的参数
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        System.out.println("before: play one hour");
        Object invoke = method.invoke(learning, args);
        System.out.println("after: play one hour");

        return invoke;
    }
}
```

## 3、创建代理对象

```java
public class DynamicProxyTest {
    @Test
    public void test(){
        //1：被代理对象
        Learning me = new People();
      
      	//2：创建处理器
        InvocatinoHandler h = new LearningInvocationHandler(me)

        //3：创建代理对象
        Learning proxyInstance =(Learning) Proxy.newProxyInstance(
                me.getClass().getClassLoader(),
                People.class.getInterfaces(),
                h );

        //4、执行代理方法
        proxyInstance.learn();

    }
}
//=====console========
//before: play one hour
//I'm learning 
//after: play one hour
```






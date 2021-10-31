### Map.computeIfAbsent(key, mappingFn)

值不存在时执行mappingFn

可用于在map中value为数组的判断与初始化，返回值是value

```java
// used in org.springframework.core.io.support.SpringFactoriesLoader#loadSpringFactories
Map<String, List<String>> result = new HashMap<>();
....
result.computeIfAbsent(factoryTypeName, key -> new ArrayList<>())
								.add(factoryImplementationName.trim());


// source 

 default V computeIfAbsent(K key,
            Function<? super K, ? extends V> mappingFunction) {
        Objects.requireNonNull(mappingFunction);
        V v;
        if ((v = get(key)) == null) {
            V newValue;
            if ((newValue = mappingFunction.apply(key)) != null) {
                put(key, newValue);
                return newValue;
            }
        }

        return v;
    }
```



### java.util.Map#putIfAbsent

```
//

default V putIfAbsent(K key, V value) {
    V v = get(key);
    if (v == null) {
        v = put(key, value);
    }

    return v;
}
```



### java.util.Map#replaceAll

```
// Replace all lists with unmodifiable lists containing unique elements
result.replaceAll((factoryType, implementations) -> implementations.stream().distinct()
      .collect(Collectors.collectingAndThen(Collectors.toList(), Collections::unmodifiableList)));
```







### Objects.requireNonNull(mappingFn)

```java
//used in map.computifabsent
Objects.requireNonNull(mappingFunction);

//source
 public static <T> T requireNonNull(T obj) {
        if (obj == null)
            throw new NullPointerException();
        return obj;
    }
```



### Collectors.collectingAndThen

将Collector<T,A,R> 转换成 Collector<T,A,RR>

常见用法 ： List<String> people

     *         = people.stream().collect(collectingAndThen(toList(), Collections::unmodifiableList));

```java
// used in org.springframework.core.io.support.SpringFactoriesLoader#loadSpringFactories
// 将result的val列表，去重后转成不可变列表
			result.replaceAll((factoryType, implementations) -> implementations.stream().distinct()
					.collect(Collectors.collectingAndThen(Collectors.toList(), Collections::unmodifiableList)));


public static<T,A,R,RR> Collector<T,A,RR> collectingAndThen(Collector<T,A,R> downstream,Function<R,RR> finisher) 
```



### java.util.Collections#emptyList

创建空列表

```java
//org.springframework.core.io.support.SpringFactoriesLoader#loadFactoryNames
return loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
```




### java.lang.System#identityHashCode

```java

//org.springframework.context.annotation.ConfigurationClassPostProcessor#postProcessBeanDefinitionRegistry
/**
 * Derive further bean definitions from the configuration classes in the registry.
 */
@Override
public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) {
   int registryId = System.identityHashCode(registry);
   if (this.registriesPostProcessed.contains(registryId)) {
      throw new IllegalStateException(
            "postProcessBeanDefinitionRegistry already called on this post-processor against " + registry);
   }
   if (this.factoriesPostProcessed.contains(registryId)) {
      throw new IllegalStateException(
            "postProcessBeanFactory already called on this post-processor against " + registry);
   }
   this.registriesPostProcessed.add(registryId);

   processConfigBeanDefinitions(registry);
}
```



### Objects::nonNull

Jdk1.7

```java
//org.springframework.boot.env.SpringApplicationJsonEnvironmentPostProcessor#postProcessEnvironment
@Override
public void postProcessEnvironment(ConfigurableEnvironment environment, SpringApplication application) {
   MutablePropertySources propertySources = environment.getPropertySources();
   propertySources.stream().map(JsonPropertyValue::get).filter(Objects::nonNull).findFirst()
         .ifPresent((v) -> processJson(environment, v));
}
```





# org.springframework.util

## Assert

```java
//use	
Assert.notNull(ctor, "Constructor must not be null");
//source
public static void notNull(@Nullable Object object, String message) {
		if (object == null) {
			throw new IllegalArgumentException(message);
		}
	}
```



## ReflectionUtils

```java
/**
 * Make the given constructor accessible, explicitly setting it accessible
 * if necessary. The {@code setAccessible(true)} method is only called
 * when actually necessary, to avoid unnecessary conflicts with a JVM
 * SecurityManager (if active).
 * @param ctor the constructor to make accessible
 * @see java.lang.reflect.Constructor#setAccessible
 */
@SuppressWarnings("deprecation")  // on JDK 9
public static void makeAccessible(Constructor<?> ctor) {
   if ((!Modifier.isPublic(ctor.getModifiers()) ||
         !Modifier.isPublic(ctor.getDeclaringClass().getModifiers())) && !ctor.isAccessible()) {
      ctor.setAccessible(true);
   }
}
```



获取main方法所在类的方法

```java
private Class<?> deduceMainApplicationClass() {
   try {
      StackTraceElement[] stackTrace = new RuntimeException().getStackTrace();
      for (StackTraceElement stackTraceElement : stackTrace) {
         if ("main".equals(stackTraceElement.getMethodName())) {
            return Class.forName(stackTraceElement.getClassName());
         }
      }
   }
   catch (ClassNotFoundException ex) {
      // Swallow and continue
   }
   return null;
}
```





Method.getDeclaringClass

判断方法是否在某个类定义的

            // 如果是 Object 定义的方法，直接调用
                if (Object.class.equals(method.getDeclaringClass())) {
                    return method.invoke(this, args);
                    } else if (isDefaultMethod(method)) {
                return invokeDefaultMethod(proxy, method, args);
            }



# 创建实例

```
return instantiateClass(clazz.getDeclaredConstructor());
```





# 数组方法 

## 拷贝数组

## System.arraycopy

System.arraycopy是System类提供的一个静态方法

```java
public static native void arraycopy(Object src,  int  srcPos,
                                          Object dest, int destPos,int length);
//可以看出它是一个本地方法，所以效率比较高

```

- src:源数组
- srcPos:源数组要复制起始的位置
- dest：目的数组
- destPos: 目的数组放置的起始位置
- length：源数组复制的长度
  **src和dest必须是同类型或者可以进行转换类型的数组**

## Arrays.copyOf()

源码

```java
public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }
public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
//底层调用的是System.arraycopy

```

- T[] original ：原始数组
- int newLength：新的数组长度
- Arrays类的方法

### 联系与区别

从两种拷贝方式的定义来看：

- System.arraycopy()使用时必须有原数组和目标数组
- Arrays.copyOf()使用时只需要有原数组即可。

从两种拷贝方式的底层实现来看：

- System.arraycopy()是底层方法
- Arrays.copyOf()是在方法中重新创建了一个数组，并调用System.arraycopy()进行拷贝。

### 两种拷贝方式的效率分析：

由于Arrays.copyOf()不但创建了新的数组而且最终还是调用System.arraycopy()，所以System.arraycopy()的效率高于Arrays.copyOf()。



### 实例

```
public class CopyOnWriteArrayList<E>
implements List<E>, RandomAccess, Cloneable, java.io.Serializable {

public void add(int index, E element) {
synchronized (lock) { // 同步锁对象
Object[] es = getArray();
int len = es.length;
if (index > len || index < 0)
throw new IndexOutOfBoundsException(outOfBounds(index,
len));
Object[] newElements;
int numMoved = len - index;
if (numMoved == 0)
newElements = Arrays.copyOf(es, len + 1);
else {
newElements = new Object[len + 1];
System.arraycopy(es, 0, newElements, 0, index); //
CopyOnWrite，写的时候，先拷贝一份之前的数组。
System.arraycopy(es, index, newElements, index + 1,
numMoved);
}
newElements[index] = element;
setArray(newElements); // 把新数组赋值给老数组
}
}
```


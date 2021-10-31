> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://www.jianshu.com/p/c169ddd34903



### 1. 当我们还在以如下几种方式使用 Optional 时, 就得开始检视自己了

*   调用 isPresent() 方法时
*   调用 get() 方法时
*   Optional 类型作为类 / 实例属性时
*   Optional 类型作为方法参数时

1.  isPresent() 与 obj != null 无任何区别, 我们的生活依然在步步惊心. 而没有 isPresent() 作铺垫的 get() 调用在 IntelliJ IDEA 中会收到告警。调用 Optional.get() 前不事先用 isPresent() 检查值是否可用. 假如 Optional 不包含一个值, get() 将会抛出一个异常！
2.  把 Optional 类型用作属性或是方法参数在 IntelliJ IDEA 中更是强力不推荐的！
3.  使用任何像 Optional 的类型作为字段或方法参数都是不可取的. Optional 只设计为类库方法的, 可明确表示可能无值情况下的返回类型. Optional 类型不可被序列化, 用作字段类型会出问题的！！！

##### 所以 Optional 中我们真正可依赖的应该是除了 isPresent() 和 get() 的其他方法:

```
public<U> Optional<U> map(Function<? super T, ? extends U> mapper)
public T orElse(T other)
public T orElseGet(Supplier<? extends T> other)
public void ifPresent(Consumer<? super T> consumer)
public Optional<T> filter(Predicate<? super T> predicate)
public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper)
public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X


```

Optional 的三种构造方式:  
Optional.of(obj), Optional.ofNullable(obj) 和明确的 Optional.empty()

1.  Optional.of(obj): 它要求传入的 obj 不能是 null 值的, 否则还没开始进入角色就倒在了 NullPointerException 异常上了.
2.  Optional.ofNullable(obj): 它以一种智能的, 宽容的方式来构造一个 Optional 实例. 来者不拒, 传 null 进到就得到 Optional.empty(), 非 null 就调用 Optional.of(obj).  
    那是不是我们只要用 Optional.ofNullable(obj) 一劳永逸, 以不变应二变的方式来构造 Optional 实例就行了呢? 那也未必, 否则 Optional.of(obj) 何必如此暴露呢, 私有则可?

#### 使用 Optional.of(obj) 原则

当我们非常非常的明确将要传给 Optional.of(obj) 的 obj 参数不可能为 null 时, 比如它是一个刚 new 出来的对象 (Optional.of(new User(...))), 或者是一个非 null 常量时; 2. 当想为 obj 断言不为 null 时, 即我们想在万一 obj 为 null 立即报告 NullPointException 异常, 立即修改, 而不是隐藏空指针异常时, 我们就应该果断的用 Optional.of(obj) 来构造 Optional 实例, 而不让任何不可预计的 null 值有可乘之机隐身于 Optional 中.  
以下为 Optional<T> 的正确使用方式：

*   存在即返回, 无则提供默认值

```
return user.orElse(null);  
return user.orElse(UNKNOWN_USER);


```

*   存在即返回, 无则由函数来产生

```
return user.orElseGet(() -> fetchAUserFromDatabase()); 


```

*   存在才对它做点什么

```
user.ifPresent(System.out::println);
 

if (user.isPresent()) {
  System.out.println(user.get());
}


```

### map 函数隆重登场

当 user.isPresent() 为真, 获得它关联的 orders 的映射集合, 为假则返回一个空集合时, 我们用上面的 orElse, orElseGet 方法都乏力时, 那原本就是 map 函数的责任, 我们可以这样一行

```
return user.map(u -> u.getOrders()).orElse(Collections.emptyList())
 

if(user.isPresent()) {
  return user.get().getOrders();
} else {
  return Collections.emptyList();
}


```

map 是可能无限级联的, 比如再深一层, 获得用户名的大写形式

```
return user.map(u -> u.getUsername())
           .map(name -> name.toUpperCase())
           .orElse(null);


```

这要搁在以前, 每一级调用的展开都需要放一个 null 值的判断

```
User user = .....
if(user != null) {
  String name = user.getUsername();
  if(name != null) {
    return name.toUpperCase();
  } else {
    return null;
  }
} else {
  return null;
}


```

*   filter() : 如果有值并且满足条件返回包含该值的 Optional，否则返回空 Optional。

```
Optional<String> longName = name.filter((value) -> value.length() > 6);  
System.out.println(longName.orElse("The name is less than 6 characters"));


```

*   flatMap() :  
    如果有值，为其执行 mapping 函数返回 Optional 类型返回值，否则返回空 Optional。flatMap 与 map（Funtion）方法类似，区别在于 flatMap 中的 mapper 返回值必须是 Optional。调用结束时，flatMap 不会对结果用 Optional 封装。  
    flatMap 方法与 map 方法类似，区别在于 mapping 函数的返回值不同。map 方法的 mapping 函数返回值可以是任何类型 T，而 flatMap 方法的 mapping 函数必须是 Optional。

```
upperName = name.flatMap((value) -> Optional.of(value.toUpperCase()));  
System.out.println(upperName.orElse("No value found"));


```

*   orElseThrow() 在有值时直接返回, 无值时抛出想要的异常.


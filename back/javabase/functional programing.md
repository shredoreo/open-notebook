# functional programing

![1579581134747](asserts/functional%20programing/1579581134747.png)

### Predicate

```java
public interface Predicate<T> {
	boolean test(T t);
}
```



### BinaryOperator

该接口接受两个参数，返回一个值，参数和值的类型均相同。





### Supplier



```java
@FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
}
```



###Lambda 表达式是

􀅖 一个匿名方法，将行为像数据一样进行传递。
􀅖 Lambda表达式的常见结构：BinaryOperator<Integer> add = (x, y) → x + y。
􀅖 函数接口指仅具有单个抽象方法的接口，用来表示 Lambda表达式的类型。
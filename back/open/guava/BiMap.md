#### 1、bimap和普通HashMap区别

（1）在Java集合类库中的Map，它的特点是存放的键（Key）是唯一的，而值（Value）可以不唯一，而

​        bimap要求key和value都唯一，如果key不唯一则覆盖key，如果value不唯一则直接报错。



#### 2、案例展示

- 如果value值重复，则运行直接报错

- 如果你想value也发生覆盖key值，那么可以inverse()：

  inverse方法会返回一个反转的BiMap，但是注意这个反转的map不是新的map对象，它实现了一种视图关联，这样你对于反转后的map的所有操作都会影响原先的map对象。



```java
public class bimapTest {
    public static void main(String args[]) {
        //双向map
        BiMap<Integer, String> biMap = HashBiMap.create();
        biMap.put(1, "张三");
        biMap.put(2, "李四");
        biMap.put(3, "王五");
        biMap.put(4, "赵六");
        biMap.put(5, "李七");
        biMap.put(4, "小小");

        System.out.println(biMap.get(1));
        System.out.println(biMap.inverse().get("张三"));

//        biMap.put(6, "小小");// java.lang.IllegalArgumentException: value already present: 小小
        biMap.forcePut(6, "小小");
        System.out.println(biMap.inverse().get("小小"));
        //null
        System.out.println(biMap.get(4));
    }
}

/*---console
张三
1
6
null
*/
```

#### 3、Bimap实现类

BiMap的常用实现有：

​    1、HashBiMap: key 集合与 value 集合都有 HashMap 实现

​    2、EnumBiMap: key 与 value 都必须是 enum 类型

​    3、ImmutableBiMap: 不可修改的 BiMap
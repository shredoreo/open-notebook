####  1、equal方法

​       使用Obejct的equals方法进行相等判断，例如：test.equals("test");如果test为null，则会发生NullPointerException，Objects的equal方法可以帮助你

避免NullPointerException，它的判断逻辑是这样的：return a == b || (a!=null&& a.equals(b));所以，可以很放心的使用，Objects.equal(test,"test")

**当然在JDK7中也提供了同样判断逻辑的方法：Objects.equals(test,"test");**


  

#### 2、**hashCode方法**

对于hashCode首先要明白两点：

​      1、如果equals()判断两个对象相等，那么它们的hashCode()方法一定返回同样的值。

　　2、并没有强制要求如果equals()判断两个对象不相等，它们的hashCode可以相同也可以不同。

Guava提供给我们了一个更加简单的方法--Objects.hashCode(Object ...)， 这是个可变参数的方法，参数列表可以是任意数量，所以可以像这样

使用Objects.hashCode(field1， field2， ...，fieldn)。非常方便和简洁。

```java
class GuavaObjectsTest {

    public static void main(String[] args) {

        boolean equal = Objects.equal("" , null);

        System.out.println(equal);
        System.out.println();

        System.out.println(Objects.hashCode("a"));
        System.out.println(Objects.hashCode("a"));
        System.out.println(Objects.hashCode("a","b"));
        System.out.println(Objects.hashCode("b","a"));
        System.out.println(Objects.hashCode("a","b","c"));

        Person person= new Person("peida",23);
        System.out.println(Objects.hashCode(person));
        person.age = 1;
        System.out.println(Objects.hashCode(person));
    }

    static class Person {
        public String name;
        public int age;

        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Person)) return false;
            Person person = (Person) o;
            return age == person.age &&
                    java.util.Objects.equals(name, person.name);
        }

        @Override
        public int hashCode() {
            return java.util.Objects.hash(name, age);
        }
    }
}
```



```verilog
false

128
128
4066
4096
126145
-991998586
-991998608
```

#### 4、firstNonNull方法

  Object的firstNonNull方法可以根据传入的两个参数来返回一个非Null的参数，Guava现在推荐使用MoreObjects.firstNonNull(T first,T second)替代它。

```java
import com.google.common.base.MoreObjects;

public class TestFilter {

    public static void main(String[] args) {
      //name不为空
        String  name="张三";
        String nann=  MoreObjects.firstNonNull(name, "哈哈");
        System.out.println(nann);

      //sex为null
        String   sex=null;
        String   sexx=  MoreObjects.firstNonNull(sex, "哈哈");
        System.out.println(sexx);

    }
    /*
     *运行结果：
     *  张三
     *  哈哈
     */
}
```


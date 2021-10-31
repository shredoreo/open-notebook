https://www.runoob.com/java/java-examples.html

# 集合反转

Collections.reverse(List) 

```java
class Main {
   public static void main(String[] args) {
      String[] coins = { "A", "B", "C", "D", "E" };
      List l = new ArrayList();
      for (int i = 0; i < coins.length; i++)
         l.add(coins[i]);
      ListIterator liter = l.listIterator();
      System.out.println("反转前");
      while (liter.hasNext())
         System.out.println(liter.next());
      Collections.reverse(l);
      liter = l.listIterator();
      System.out.println("反转后");
      while (liter.hasNext())
         System.out.println(liter.next());
   }
}
```



```
反转前
A
B
C
D
E
反转后
E
D
C
B
A
```
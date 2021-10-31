> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/sunmenggmail/article/details/9152325)

```
class A{
	
}
 
class B extends A {
	
}
 
class C extends B {
	
}
 
 
public class tt {
 
	/**
	 * @param args
	 */
	
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		C c = new C();
		B b = new B();
		A a = new A();
		
		B bc = new C();
		A ac = new C();
		
		System.out.println(c instanceof C);
		System.out.println(c instanceof B);
		System.out.println(c instanceof A);
		
		System.out.println();
		
		System.out.println(c.getClass().isInstance(c));
		System.out.println(c.getClass().isInstance(b));
		System.out.println(c.getClass().isInstance(a));
		
		System.out.println();
		
		System.out.println(c.getClass().isInstance(bc));
		System.out.println(c.getClass().isInstance(ac));
		
		System.out.println();
		
		System.out.println(A.class.isInstance(a));
		System.out.println(A.class.isInstance(b));
		System.out.println(A.class.isInstance(c));
		System.out.println(A.class.isInstance(ac));
		System.out.println(A.class.isInstance(bc));
		
		System.out.println();
		
		System.out.println(B.class.isInstance(a));
		System.out.println(B.class.isInstance(b));
		System.out.println(B.class.isInstance(c));
		System.out.println(B.class.isInstance(ac));
		System.out.println(B.class.isInstance(bc));
		
		
	}
 
}
```

true  
true  
true  
  
  
true  
false  
false  
  
  
true  
true  
  
  
true  
true  
true  
true  
true  
  
  
false  
true  
true  
true  
true  

对象 instanceof 类

obj instanceof class

如果 class obj1 = obj 成立的话，返回 true，否则返回 false

类. isInstance(对象)

class.isInstance(obj)

如果 class obj1 = obj 成立的话，返回 true，否则返回 false

看到更形象的解释

instanceof 运算符 只被用于对象引用变量，检查左边的被测试对象 是不是 右边类或接口的 实例化。如果被测对象是 null 值，则测试结果总是 false。  
形象地：自身实例或子类实例 instanceof 自身类   返回 true  
例： String s=new String("javaisland");  
       System.out.println(s instanceof String); //true  
   
Class 类的 isInstance(Object obj) 方法，obj 是被测试的对象，如果 obj 是调用这个方法的 class 或接口 的实例，则返回 true。这个方法是 instanceof 运算符的动态等价。  
形象地：自身类. class.isInstance(自身实例或子类实例)  返回 true  
例：String s=new String("javaisland");  
      System.out.println(String.class.isInstance(s)); //true  
   
Class 类的 isAssignableFrom(Class cls) 方法，如果调用这个方法的 class 或接口 与 参数 cls 表示的类或接口相同，或者是参数 cls 表示的类或接口的父类，则返回 true。  
形象地：自身类. class.isAssignableFrom(自身类或子类. class)  返回 true  
例：System.out.println(ArrayList.class.isAssignableFrom(Object.class));  //false  
      System.out.println(Object.class.isAssignableFrom(ArrayList.class));  //true  

from sforiz
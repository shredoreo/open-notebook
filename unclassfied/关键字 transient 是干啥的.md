> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/u010188178/article/details/83581506)



转瞬即逝的;短暂的;倏忽;暂住的;过往的;临时的


**百度百科的解释：**

 Java 语言的关键字，变量修饰符，如果用 **transient** 声明一个实例变量，当对象存储时，它的值不需要维持。换句话来说就是，用 **transient** 关键字标记的成员变量不参与序列化过程。

**作用：**  
        Java 的 **serialization** 提供了一种持久化对象实例的机制。当持久化对象时，可能有一个特殊的对象数据成员，我们不想用 **serialization** 机制来保存它。为了在一个特定对象的一个域上关闭 **serialization**，可以在这个域前加上关键字 **transient**。当一个对象被序列化的时候，**transient** 型变量的值不包括在序列化的表示中，然而非 **transient** 型的变量是被包括进去的。  


**编码试验加以证明：**

1. 自定义类（为了方便，我直接在 main 方法所在类中添加的一个静态**[属性类，或者叫成员类](https://blog.csdn.net/u010188178/article/details/83073085)**）

```
public static class TransientTest implements Serializable{
		private static final long serialVersionUID = 233858934995755239L;
		private String name1;
		private transient String name2;
		
		public TransientTest(String name1,String name2){
			this.name1 = name1;
			this.name2 = name2;
		}
		public String toString(){
			return String.format("TransientTest.toString(): name1=%s,name2=%s", name1,name2);
		}
	}
```

2. 写一个测试方法

```
public static void testTransient(){
		String name1="常规属性",name2="transient修饰的属性";
		TransientTest test = new TransientTest(name1, name2);
		System.out.println("序列化前："+test.toString());
		ObjectOutputStream outStream;
		ObjectInputStream inStream;
		String filePath = "D:/test/object/TransientTest.obj";
		try {
			outStream = new ObjectOutputStream(new FileOutputStream(filePath));
			outStream.writeObject(test);
			
			inStream = new ObjectInputStream(new FileInputStream(filePath));
			TransientTest readObject = (TransientTest)inStream.readObject();
			System.out.println("序列化后："+readObject.toString());
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
```

3. 在 main 方法中执行，然后可以看到得到的结果为

![](https://img-blog.csdnimg.cn/20181031125516514.png)

印证了上面所讲的 “用 transient 关键字标记的成员变量不参与序列化过程”。

用二进制查看器打开这个文件也可以看到，数据中只有 name1，没有 name2。（请忽略乱码问题，这个不是重点哈。）

![](https://img-blog.csdnimg.cn/20181031130110905.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAxODgxNzg=,size_16,color_FFFFFF,t_70)

**延伸：**

在查看 JDK 源码的时候会发现很多地方都会加上 **transient** 关键字来修饰一些属性，那究竟是出于什么考虑才这么做呢？

**我觉得，应该是为了节约磁盘空间，避免造成不必要的浪费吧。**

以 ArrayList 中的 transient Object[] elementData 为例，这个成员变量的注释为：

![](https://img-blog.csdnimg.cn/20181031131101382.png)

翻译出来就是：

/ * *

* 存储 ArrayList 元素的数组缓冲区。

* ArrayList 的容量是这个数组缓冲区的长度。任何

* 带有 elementData 的空 ArrayList == DEFAULTCAPACITY_EMPTY_ELEMENTDATA

* 当添加第一个元素时，将被扩展到 DEFAULT_CAPACITY。

* /

        这个缓冲区的容量实际上并不是 ArrayList 的容量，因为其实际上会预留一些空间，当空间不足时还会扩容，为减少浪费，因此在序列化时不会按照默认算法将这个成员变量写入磁盘。而是写了个 writeObject 方法，序列化时会调用这个方法将其持久化，在反序列化是，调用 readObject，将其恢复出来。

这 2 个方法为：

![](https://img-blog.csdnimg.cn/20181031133234918.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAxODgxNzg=,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20181031133250467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAxODgxNzg=,size_16,color_FFFFFF,t_70)

参考 ArrayList，在上面的 **TransientTest** 中添加 2 个方法，见代码：

```
public static class TransientTest implements Serializable{
		private static final long serialVersionUID = 233858934995755239L;
		private String name1;
		private transient String name2;
		
		public TransientTest(String name1,String name2){
			this.name1 = name1;
			this.name2 = name2;
		}
		public String toString(){
			return String.format("TransientTest.toString(): name1=%s,name2=%s", name1,name2);
		}
 
		private void writeObject(java.io.ObjectOutputStream s) throws java.io.IOException {
			s.defaultWriteObject();
			s.writeObject(name2);
		}
		private void readObject(java.io.ObjectInputStream s) throws java.io.IOException, ClassNotFoundException {
			s.defaultReadObject();
			name2=String.valueOf(s.readObject());
		}
	}
```

然后在 main 方法中执行 testTransient()，此时得到的结果是：

![](https://img-blog.csdnimg.cn/20181031173549537.png)

**个人观点，仅供参考，如有不足之处，欢迎批评指正！**
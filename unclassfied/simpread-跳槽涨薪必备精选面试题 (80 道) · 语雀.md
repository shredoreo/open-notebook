> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.yuque.com](https://www.yuque.com/renyong-jmovm/ds/vepkxm)

> 金九银十涨薪必备精选面试题

作者：图灵课堂 ------- 周瑜  
1、看以下代码回答问题（一）  
Java 复制代码

```
public static void main(String[] args) {

  String s = new String("abc");

  // 在这中间可以添加N行代码，但必须保证s引用的指向不变，最终将输出变成abcd

  System.out.println(s);

}
```

答案：  
Java 复制代码

```
public static void main(String[] args) {    

    String s = new String("abc");



  // 在这中间可以添加N行代码，但必须保证s引用的指向不变，最终将输出变成abcd

  Field value = s.getClass().getDeclaredField("value");

  value.setAccessible(true);

  value.set(s, "abcd".toCharArray());



  System.out.println(s);

}
```

2、看以下代码回答问题（二）  
Java 复制代码

```
public static void main(String[] args) {  

    String s1 = new String("abc");

  String s2 = "abc"; 

    // s1 == s2?

    String s3 = s1.intern();

    // s2 == s3?

}
```

答案：  
1s1 == s2 为 false  
2s2 == s3 为 true  
String 对象的 intern 方法，首先会检查字符串常量池中是否存在 "abc"，如果存在则返回该字符串引用，如果不存在，则把 "abc" 添加到字符串常量池中，并返回该字符串常量的引用。  
3、看以下代码回答问题（三）  
Java 复制代码

```
public static void main(String[] args) {    

    Integer i1 = 100;

    Integer i2 = 100;

    // i1 == i2?



    Integer i3 = 128;

    Integer i4 = 128;

  // i3 == i4?

}
```

答案：  
1i1 == i2 为 true  
2i3 == i4 为 false  
在 Interger 类中，存在一个静态内部类 IntegerCache， 该类中存在一个 Integer cache[]， 并且存在一个 static 块，会在加载类的时候执行，会将 - 128 至 127 这些数字提前生成 Integer 对象，并缓存在 cache 数组中，当我们在定义 Integer 数字时，会调用 Integer 的 valueOf 方法，valueOf 方法会判断所定义的数字是否在 - 128 至 127 之间，如果存在则直接从 cache 数组中获取 Integer 对象，如果超过，则生成一个新的 Integer 对象。  
4、String、StringBuffer、StringBuilder 的区别  
1String 是不可变的，如果尝试去修改，会新生成一个字符串对象，StringBuffer 和 StringBuilder 是可变的  
2StringBuffer 是线程安全的，StringBuilder 是线程不安全的，所以在单线程环境下 StringBuilder 效率会更高  
5、ArrayList 和 LinkedList 有哪些区别  
1 首先，他们的底层数据结构不同，ArrayList 底层是基于数组实现的，LinkedList 底层是基于链表实现的2 由于底层数据结构不同，他们所适用的场景也不同，ArrayList 更适合随机查找，LinkedList 更适合删除和添加，查询、添加、删除的时间复杂度不同3 另外 ArrayList 和 LinkedList 都实现了 List 接口，但是 LinkedList 还额外实现了 Deque 接口，所以 LinkedList 还可以当做队列来使用6、CopyOnWriteArrayList 的底层原理是怎样的  
1 首先 CopyOnWriteArrayList 内部也是用数组来实现的，在向 CopyOnWriteArrayList 添加元素时，会复制一个新的数组，写操作在新数组上进行，读操作在原数组上进行2 并且，写操作会加锁，防止出现并发写入丢失数据的问题3 写操作结束之后会把原数组指向新数组4CopyOnWriteArrayList 允许在写操作时来读取数据，大大提高了读的性能，因此适合读多写少的应用场景，但是 CopyOnWriteArrayList 会比较占内存，同时可能读到的数据不是实时最新的数据，所以不适合实时性要求很高的场景  
7、HashMap 的扩容机制原理  
1.7 版本  
1 先生成新数组2 遍历老数组中的每个位置上的链表上的每个元素3 取每个元素的 key，并基于新数组长度，计算出每个元素在新数组中的下标4 将元素添加到新数组中去5 所有元素转移完了之后，将新数组赋值给 HashMap 对象的 table 属性1.8 版本  
1 先生成新数组2 遍历老数组中的每个位置上的链表或红黑树3 如果是链表，则直接将链表中的每个元素重新计算下标，并添加到新数组中去4 如果是红黑树，则先遍历红黑树，先计算出红黑树中每个元素对应在新数组中的下标位置a 统计每个下标位置的元素个数b 如果该位置下的元素个数超过了 8，则生成一个新的红黑树，并将根节点的添加到新数组的对应位置c 如果该位置下的元素个数没有超过 8，那么则生成一个链表，并将链表的头节点添加到新数组的对应位置5 所有元素转移完了之后，将新数组赋值给 HashMap 对象的 table 属性8、ConcurrentHashMap 的扩容机制  
1.7 版本  
11.7 版本的 ConcurrentHashMap 是基于 Segment 分段实现的  
2 每个 Segment 相对于一个小型的 HashMap3 每个 Segment 内部会进行扩容，和 HashMap 的扩容逻辑类似4 先生成新的数组，然后转移元素到新数组中5 扩容的判断也是每个 Segment 内部单独判断的，判断是否超过阈值1.8 版本  
11.8 版本的 ConcurrentHashMap 不再基于 Segment 实现  
2 当某个线程进行 put 时，如果发现 ConcurrentHashMap 正在进行扩容那么该线程一起进行扩容3 如果某个线程 put 时，发现没有正在进行扩容，则将 key-value 添加到 ConcurrentHashMap 中，然后判断是否超过阈值，超过了则进行扩容4ConcurrentHashMap 是支持多个线程同时扩容的  
5 扩容之前也先生成一个新的数组6 在转移元素时，先将原数组分组，将每组分给不同的线程来进行元素的转移，每个线程负责一组或多组的元素转移工作9、ThreadLocal 的底层原理  
1ThreadLocal 是 Java 中所提供的线程本地存储机制，可以利用该机制将数据缓存在某个线程内部，该线程可以在任意时刻、任意方法中获取缓存的数据  
2ThreadLocal 底层是通过 ThreadLocalMap 来实现的，每个 Thread 对象（注意不是 ThreadLocal 对象）中都存在一个 ThreadLocalMap，Map 的 key 为 ThreadLocal 对象，Map 的 value 为需要缓存的值  
3 如果在线程池中使用 ThreadLocal 会造成内存泄漏，因为当 ThreadLocal 对象使用完之后，应该要把设置的 key，value，也就是 Entry 对象进行回收，但线程池中的线程不会回收，而线程对象是通过强引用指向 ThreadLocalMap，ThreadLocalMap 也是通过强引用指向 Entry 对象，线程不被回收，Entry 对象也就不会被回收，从而出现内存泄漏，解决办法是，在使用了 ThreadLocal 对象之后，手动调用 ThreadLocal 的 remove 方法，手动清楚 Entry 对象4ThreadLocal 经典的应用场景就是连接管理（一个线程持有一个连接，该连接对象可以在不同的方法之间进行传递，线程之间不共享同一个连接）  
![](https://cdn.nlark.com/yuque/0/2021/png/365147/1622816023795-3ae4931c-bcab-4e8c-a987-4fecf53f9855.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_25%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_863%2Climit_0)  
作者：图灵课堂 ------- 周瑜  
10、如何理解 volatile 关键字  
在并发领域中，存在三大特性：原子性、有序性、可见性。volatile 关键字用来修饰对象的属性，在并发环境下可以保证这个属性的可见性，对于加了 volatile 关键字的属性，在对这个属性进行修改时，会直接将 CPU 高级缓存中的数据写回到主内存，对这个变量的读取也会直接从主内存中读取，从而保证了可见性，底层是通过操作系统的内存屏障来实现的，由于使用了内存屏障，所以会禁止指令重排，所以同时也就保证了有序性，在很多并发场景下，如果用好 volatile 关键字可以很好的提高执行效率。  
11、ReentrantLock 中的公平锁和非公平锁的底层实现  
首先不管是公平锁和非公平锁，它们的底层实现都会使用 AQS 来进行排队，它们的区别在于：线程在使用 lock() 方法加锁时，如果是公平锁，会先检查 AQS 队列中是否存在线程在排队，如果有线程在排队，则当前线程也进行排队，如果是非公平锁，则不会去检查是否有线程在排队，而是直接竞争锁。  
不管是公平锁还是非公平锁，一旦没竞争到锁，都会进行排队，当锁释放时，都是唤醒排在最前面的线程，所以非公平锁只是体现在了线程加锁阶段，而没有体现在线程被唤醒阶段。  
另外，ReentrantLock 是可重入锁，不管是公平锁还是非公平锁都是可重入的。  
公平锁加锁干左句镇 0 品宁从列中花W55:Kle加 0 次戴功刀始入射开始入队一倍入风O 苏hees让默, 姐一个程Node为宝为 Nre 时, 为队列快THREAC-REEITra1 - 千配 6入队应功人队日名长九时, 营列巾口航标如才兰纳 u 味是队列为 - 1, 在示前一个节用并啦: 一个检 SNaAca?立风完大务家代长在户川不的的品制构设堂WA?小方司![](https://cdn.nlark.com/yuque/0/2021/png/365147/1626185425264-6d9a8ab7-12d9-4032-8fa4-1bcc40231009.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_65%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_436%2Climit_0) 非公平锁加锁c 州免防 R星河曲力1 线购 T1凯艾中1oO55h开姓入队血皮制古 HULL队列太的老六具uzm.t 包线口 g8梅 - ecSSkgs办新一个五气料饮 i 否 w>sk 六虾杯气十炎下肉情无上,KS 航 kINGNR 动![](https://cdn.nlark.com/yuque/0/2021/png/365147/1626185427450-d6a6ab8e-94a4-4e7a-be68-bab2d06b320c.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_65%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_376%2Climit_0)  
12、ReentrantLock 中 tryLock() 和 lock() 方法的区别  
1tryLock() 表示尝试加锁，可能加到，也可能加不到，该方法不会阻塞线程，如果加到锁则返回 true，没有加到则返回 false  
2lock() 表示阻塞加锁，线程会阻塞直到加到锁，方法也没有返回值  
13、CountDownLatch 和 Semaphore 的区别和底层原理  
CountDownLatch 表示计数器，可以给 CountDownLatch 设置一个数字，一个线程调用 CountDownLatch 的 await() 将会阻塞，其他线程可以调用 CountDownLatch 的 countDown() 方法来对 CountDownLatch 中的数字减一，当数字被减成 0 后，所有 await 的线程都将被唤醒。  
对应的底层原理就是，调用 await() 方法的线程会利用 AQS 排队，一旦数字被减为 0，则会将 AQS 中排队的线程依次唤醒。  
Semaphore 表示信号量，可以设置许可的个数，表示同时允许最多多少个线程使用该信号量，通过 acquire() 来获取许可，如果没有许可可用则线程阻塞，并通过 AQS 来排队，可以通过 release() 方法来释放许可，当某个线程释放了某个许可后，会从 AQS 中正在排队的第一个线程开始依次唤醒，直到没有空闲许可。  
14、Sychronized 的偏向锁、轻量级锁、重量级锁  
1 偏向锁：在锁对象的对象头中记录一下当前获取到该锁的线程 ID，该线程下次如果又来获取该锁就可以直接获取到了2 轻量级锁：由偏向锁升级而来，当一个线程获取到锁后，此时这把锁是偏向锁，此时如果有第二个线程来竞争锁，偏向锁就会升级为轻量级锁，之所以叫轻量级锁，是为了和重量级锁区分开来，轻量级锁底层是通过自旋来实现的，并不会阻塞线程3 如果自旋次数过多仍然没有获取到锁，则会升级为重量级锁，重量级锁会导致线程阻塞4 自旋锁：自旋锁就是线程在获取锁的过程中，不会去阻塞线程，也就无所谓唤醒线程，阻塞和唤醒这两个步骤都是需要操作系统去进行的，比较消耗时间，自旋锁是线程通过 CAS 获取预期的一个标记，如果没有获取到，则继续循环获取，如果获取到了则表示获取到了锁，这个过程线程一直在运行中，相对而言没有使用太多的操作系统资源，比较轻量。15、Sychronized 和 ReentrantLock 的区别  
1sychronized 是一个关键字，ReentrantLock 是一个类  
2sychronized 会自动的加锁与释放锁，ReentrantLock 需要程序员手动加锁与释放锁  
3sychronized 的底层是 JVM 层面的锁，ReentrantLock 是 API 层面的锁  
4sychronized 是非公平锁，ReentrantLock 可以选择公平锁或非公平锁  
5sychronized 锁的是对象，锁信息保存在对象头中，ReentrantLock 通过代码中 int 类型的 state 标识来标识锁的状态  
6sychronized 底层有一个锁升级的过程  
16、线程池的底层工作原理  
线程池内部是通过队列 + 线程实现的，当我们利用线程池执行任务时：  
1 如果此时线程池中的线程数量小于 corePoolSize，即使线程池中的线程都处于空闲状态，也要创建新的线程来处理被添加的任务。2 如果此时线程池中的线程数量等于 corePoolSize，但是缓冲队列 workQueue 未满，那么任务被放入缓冲队列。3 如果此时线程池中的线程数量大于等于 corePoolSize，缓冲队列 workQueue 满，并且线程池中的数量小于 maximumPoolSize，建新的线程来处理被添加的任务。4 如果此时线程池中的线程数量大于 corePoolSize，缓冲队列 workQueue 满，并且线程池中的数量等于 maximumPoolSize，那么通过 handler 所指定的策略来处理此任务。5 当线程池中的线程数量大于 corePoolSize 时，如果某线程空闲时间超过 keepAliveTime，线程将被终止。这样，线程池可以动态的调整池中的线程数17、JVM 中哪些是线程共享区  
堆区和方法区是所有线程共享的，栈、本地方法栈、程序计数器是每个线程独有的  
![](https://cdn.nlark.com/yuque/0/2021/png/365147/1622816824404-17d018de-eb06-41eb-9936-64e4c6cc046d.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_15%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_536%2Climit_0)  
18、JVM 中哪些可以作为 gc root  
什么是 gc root，JVM 在进行垃圾回收时，需要找到 “垃圾” 对象，也就是没有被引用的对象，但是直接找 “垃圾” 对象是比较耗时的，所以反过来，先找 “非垃圾” 对象，也就是正常对象，那么就需要从某些 “根” 开始去找，根据这些 “根” 的引用路径找到正常对象，而这些 “根” 有一个特征，就是它只会引用其他对象，而不会被其他对象引用，例如：栈中的本地变量、方法区中的静态变量、本地方法栈中的变量、正在运行的线程等可以作为 gc root。  
19、你们项目如何排查 JVM 问题  
对于还在正常运行的系统：  
1 可以使用 jmap 来查看 JVM 中各个区域的使用情况2 可以通过 jstack 来查看线程的运行情况，比如哪些线程阻塞、是否出现了死锁3 可以通过 jstat 命令来查看垃圾回收的情况，特别是 fullgc，如果发现 fullgc 比较频繁，那么就得进行调优了4 通过各个命令的结果，或者 jvisualvm 等工具来进行分析5 首先，初步猜测频繁发生 fullgc 的原因，如果频繁发生 fullgc 但是又一直没有出现内存溢出，那么表示 fullgc 实际上是回收了很多对象了，所以这些对象最好能在 younggc 过程中就直接回收掉，避免这些对象进入到老年代，对于这种情况，就要考虑这些存活时间不长的对象是不是比较大，导致年轻代放不下，直接进入到了老年代，尝试加大年轻代的大小，如果改完之后，fullgc 减少，则证明修改有效6 同时，还可以找到占用 CPU 最多的线程，定位到具体的方法，优化这个方法的执行，看是否能避免某些对象的创建，从而节省内存对于已经发生了 OOM 的系统：  
1 一般生产系统中都会设置当系统发生了 OOM 时，生成当时的 dump 文件（-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/usr/local/base）2 我们可以利用 jsisualvm 等工具来分析 dump 文件3 根据 dump 文件找到异常的实例对象，和异常的线程（占用 CPU 高），定位到具体的代码4 然后再进行详细的分析和调试总之，调优不是一蹴而就的，需要分析、推理、实践、总结、再分析，最终定位到具体的问题  
20、说说类加载器双亲委派模型  
JVM 中存在三个默认的类加载器：  
1BootstrapClassLoader  
2ExtClassLoader  
3AppClassLoader  
AppClassLoader 的父加载器是 ExtClassLoader，ExtClassLoader 的父加载器是 BootstrapClassLoader。  
JVM 在加载一个类时，会调用 AppClassLoader 的 loadClass 方法来加载这个类，不过在这个方法中，会先使用 ExtClassLoader 的 loadClass 方法来加载类，同样 ExtClassLoader 的 loadClass 方法中会先使用 BootstrapClassLoader 来加载类，如果 BootstrapClassLoader 加载到了就直接成功，如果 BootstrapClassLoader 没有加载到，那么 ExtClassLoader 就会自己尝试加载该类，如果没有加载到，那么则会由 AppClassLoader 来加载这个类。  
所以，双亲委派指得是，JVM 在加载类时，会委派给 Ext 和 Bootstrap 进行加载，如果没加载到才由自己进行加载。  
21、Tomcat 中为什么要使用自定义类加载器  
一个 Tomcat 中可以部署多个应用，而每个应用中都存在很多类，并且各个应用中的类是独立的，全类名是可以相同的，比如一个订单系统中可能存在 com.zhouyu.User 类，一个库存系统中可能也存在 com.zhouyu.User 类，一个 Tomcat，不管内部部署了多少应用，Tomcat 启动之后就是一个 Java 进程，也就是一个 JVM，所以如果 Tomcat 中只存在一个类加载器，比如默认的 AppClassLoader，那么就只能加载一个 com.zhouyu.User 类，这是有问题的，而在 Tomcat 中，会为部署的每个应用都生成一个类加载器实例，名字叫做 WebAppClassLoader，这样 Tomcat 中每个应用就可以使用自己的类加载器去加载自己的类，从而达到应用之间的类隔离，不出现冲突。另外 Tomcat 还利用自定义加载器实现了热加载功能。  
22、Tomcat 如何进行优化？  
对于 Tomcat 调优，可以从两个方面来进行调整：内存和线程。  
首先启动 Tomcat，实际上就是启动了一个 JVM，所以可以按 JVM 调优的方式来进行调整，从而达到 Tomcat 优化的目的。  
另外 Tomcat 中设计了一些缓存区，比如 appReadBufSize、bufferPoolSize 等缓存区来提高吞吐量。  
还可以调整 Tomcat 的线程，比如调整 minSpareThreads 参数来改变 Tomcat 空闲时的线程数，调整 maxThreads 参数来设置 Tomcat 处理连接的最大线程数。  
并且还可以调整 IO 模型，比如使用 NIO、APR 这种相比于 BIO 更加高效的 IO 模型。  
23、浏览器发出一个请求到收到响应经历了哪些步骤？  
1 浏览器解析用户输入的 URL，生成一个 HTTP 格式的请求2 先根据 URL 域名从本地 hosts 文件查找是否有映射 IP，如果没有就将域名发送给电脑所配置的 DNS 进行域名解析，得到 IP 地址3 浏览器通过操作系统将请求通过四层网络协议发送出去4 途中可能会经过各种路由器、交换机，最终到达服务器5 服务器收到请求后，根据请求所指定的端口，将请求传递给绑定了该端口的应用程序，比如 8080 被 tomcat 占用了6tomcat 接收到请求数据后，按照 http 协议的格式进行解析，解析得到所要访问的 servlet  
7 然后 servlet 来处理这个请求，如果是 SpringMVC 中的 DispatcherServlet，那么则会找到对应的 Controller 中的方法，并执行该方法得到结果8Tomcat 得到响应结果后封装成 HTTP 响应的格式，并再次通过网络发送给浏览器所在的服务器  
9 浏览器所在的服务器拿到结果后再传递给浏览器，浏览器则负责解析并渲染24、跨域请求是什么？有什么问题？怎么解决？  
跨域是指浏览器在发起网络请求时，会检查该请求所对应的协议、域名、端口和当前网页是否一致，如果不一致则浏览器会进行限制，比如在 www.baidu.com 的某个网页中，如果使用 ajax 去访问 www.jd.com 是不行的，但是如果是 img、iframe、script 等标签的 src 属性去访问则是可以的，之所以浏览器要做这层限制，是为了用户信息安全。但是如果开发者想要绕过这层限制也是可以的：  
1response 添加 header，比如 resp.setHeader("Access-Control-Allow-Origin", "*"); 表示可以访问所有网站，不受是否同源的限制  
2jsonp 的方式，该技术底层就是基于 script 标签来实现的，因为 script 标签是可以跨域的  
3 后台自己控制，先访问同域名下的接口，然后在接口中再去使用 HTTPClient 等工具去调用目标接口4 网关，和第三种方式类似，都是交给后台服务来进行跨域访问跨域攻击：  
1 我登录了 www.bank.com2 我访问了 www.a.com，并点击了该网页中的一个链接，该链接为 www.bank.com/transfer?accountid=myid&count=100000025、Spring 中的 Bean 创建的生命周期有哪些步骤  
Spring 中一个 Bean 的创建大概分为以下几个步骤：  
1 推断构造方法2 实例化3 填充属性，也就是依赖注入4 处理 Aware 回调5 初始化前，处理 @PostConstruct 注解6 初始化，处理 InitializingBean 接口7 初始化后，进行 AOP当然其实真正的步骤更加细致，可以看下面的流程图  
Spring 启动单例 BeanDotinition填充口性单创日 can 生成初始化实斛化化初始化前历 beanDefinitionMap扫播实耐化前合井 BeanDefinition填充二性垃充属性后精选出单例 BeanDrtnibon加式类生成 BeanDefiniton推断梅造方法扭始合化pulbeanDefinitionMapsbeanNa初始化后实的化实懈化汽历单哥 BeanDefinitionme,BeanDehinition实时化化后垃充属性执行回调初始化![](https://cdn.nlark.com/yuque/0/2021/png/365147/1625729452241-4bc1dbb0-a827-49f1-83b8-70e61fd29a65.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_123%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1716%2Climit_0)  
26、Spring 中 Bean 是线程安全的吗  
Spring 本身并没有针对 Bean 做线程安全的处理，所以：  
1 如果 Bean 是无状态的，那么 Bean 则是线程安全的2 如果 Bean 是有状态的，那么 Bean 则不是线程安全的另外，Bean 是不是线程安全，跟 Bean 的作用域没有关系，Bean 的作用域只是表示 Bean 的生命周期范围，对于任何生命周期的 Bean 都是一个对象，这个对象是不是线程安全的，还是得看这个 Bean 对象本身。  
27、ApplicationContext 和 BeanFactory 有什么区别  
BeanFactory 是 Spring 中非常核心的组件，表示 Bean 工厂，可以生成 Bean，维护 Bean，而 ApplicationContext 继承了 BeanFactory，所以 ApplicationContext 拥有 BeanFactory 所有的特点，也是一个 Bean 工厂，但是 ApplicationContext 除开继承了 BeanFactory 之外，还继承了诸如 EnvironmentCapable、MessageSource、ApplicationEventPublisher 等接口，从而 ApplicationContext 还有获取系统环境变量、国际化、事件发布等功能，这是 BeanFactory 所不具备的  
28、Spring 中的事务是如何实现的  
1Spring 事务底层是基于数据库事务和 AOP 机制的  
2 首先对于使用了 @Transactional 注解的 Bean，Spring 会创建一个代理对象作为 Bean3 当调用代理对象的方法时，会先判断该方法上是否加了 @Transactional 注解4 如果加了，那么则利用事务管理器创建一个数据库连接5 并且修改数据库连接的 autocommit 属性为 false，禁止此连接的自动提交，这是实现 Spring 事务非常重要的一步6 然后执行当前方法，方法中会执行 sql7 执行完当前方法后，如果没有出现异常就直接提交事务8 如果出现了异常，并且这个异常是需要回滚的就会回滚事务，否则仍然提交事务9Spring 事务的隔离级别对应的就是数据库的隔离级别  
10Spring 事务的传播机制是 Spring 事务自己实现的，也是 Spring 事务中最复杂的  
11Spring 事务的传播机制是基于数据库连接来做的，一个数据库连接一个事务，如果传播机制配置为需要新开一个事务，那么实际上就是先建立一个数据库连接，在此新数据库连接上执行 sql  
![](https://cdn.nlark.com/yuque/0/2021/png/365147/1622966825505-41961ccc-19e0-4f70-8182-e6e3337eb3af.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_223%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1716%2Climit_0)  
29、Spring 中什么时候 @Transactional 会失效  
因为 Spring 事务是基于代理来实现的，所以某个加了 @Transactional 的方法只有是被代理对象调用时，那么这个注解才会生效，所以如果是被代理对象来调用这个方法，那么 @Transactional 是不会失效的。  
同时如果某个方法是 private 的，那么 @Transactional 也会失效，因为底层 cglib 是基于父子类来实现的，子类是不能重载父类的 private 方法的，所以无法很好的利用代理，也会导致 @Transactianal 失效  
30、Spring 容器启动流程是怎样的  
1 在创建 Spring 容器，也就是启动 Spring 时：2 首先会进行扫描，扫描得到所有的 BeanDefinition 对象，并存在一个 Map 中3 然后筛选出非懒加载的单例 BeanDefinition 进行创建 Bean，对于多例 Bean 不需要在启动过程中去进行创建，对于多例 Bean 会在每次获取 Bean 时利用 BeanDefinition 去创建4 利用 BeanDefinition 创建 Bean 就是 Bean 的创建生命周期，这期间包括了合并 BeanDefinition、推断构造方法、实例化、属性填充、初始化前、初始化、初始化后等步骤，其中 AOP 就是发生在初始化后这一步骤中5 单例 Bean 创建完了之后，Spring 会发布一个容器启动事件6Spring 启动结束  
7 在源码中会更复杂，比如源码中会提供一些模板方法，让子类来实现，比如源码中还涉及到一些 BeanFactoryPostProcessor 和 BeanPostProcessor 的注册，Spring 的扫描就是通过 BenaFactoryPostProcessor 来实现的，依赖注入就是通过 BeanPostProcessor 来实现的8 在 Spring 启动过程中还会去处理 @Import 等注解![](https://cdn.nlark.com/yuque/0/2021/png/365147/1622965863563-ce71fb30-d01f-4829-93a8-a94d784ccc2c.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_151%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1716%2Climit_0)  
31、Spring 用到了哪些设计模式  
BeanFactory工厂模式FactoryBeanProxyFactory原型 Bean原型模式PrototypeTargetSourcePrototypeaspectlnstanceFactory单例 BeanSingletonTargetsource单例模式DefaultBeanNameGeneratorSimpleAutowireCandidateResolverAnnotationAwareOrderComparatorBeanDefinition 构造芸BeanDefinitionBuilder解析并梅造 @Aspecu 注解的 Bean 中所定义的 Advisor构建器模式BeanFactoryAspecUAdvisorsBuilderStringBuilder@EventListener 注解的方法适九成 ApplicationListenerApplicationListenerMethodAdapter适配器模式AdvisorAdapter把 Advison 适配成 MethodlnterceptorSpring 中的设计模式 - 周瑜属性访问器, 用来访问和设苦某个对象的某个三性PropertyAccessor访问者模式国际化资源访问器MessageSourceAccessor比单纯的 Bean 对象功能更加强大BeanWrapper装饰器模式回HttpRequestWrapper方式生成了代理对象的地方就用到了代理模式AOP代理模式@Configuration@Lazy事件监听机制ApplicationListener观察者模式国Proyfactoy 可以提交此监听器, 用来监听 Proyfacton 创建代理对象完成事件, 添加 Adi 享件等AdvisedsupportListenerSpring 需要很据 BeanDefinition 来实例化 Bean, 但是具体可以选择不同的策路来进行实例化lnstantiationStrategy策略模式国beanName 生成器BeanNameGenerato子类可以继续处理 BeanfFacitorypostProcessBeanFactory0模板方法模式 4AbstractApplicationContext子类可以做一些额外的初始化onRefresho负责造一条 Advisorchain, 代理对象执行某个方法时会恢次条过 Adisorchain 中的每个 ADiSORDefaultAdvisorChainFactory责任链模式QualifierAnnotationAutowireCandidateResolver判新某个 Bean 能不能用来进行依赖注入强可以认为也是责任链![](https://cdn.nlark.com/yuque/0/2021/png/365147/1629189022914-bb297acb-7ddf-47be-9c1a-ee7c49eaa478.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_80%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_1716%2Climit_0)  
32、SpringMVC 的底层工作流程  
1 用户发送请求至前端控制器 DispatcherServlet。2DispatcherServlet 收到请求调用 HandlerMapping 处理器映射器。  
3 处理器映射器找到具体的处理器 (可以根据 xml 配置、注解进行查找)，生成处理器及处理器拦截器(如果有则生成) 一并返回给 DispatcherServlet。4DispatcherServlet 调用 HandlerAdapter 处理器适配器。  
5HandlerAdapter 经过适配调用具体的处理器 (Controller，也叫后端控制器)  
6Controller 执行完成返回 ModelAndView。  
7HandlerAdapter 将 controller 执行结果 ModelAndView 返回给 DispatcherServlet。  
8DispatcherServlet 将 ModelAndView 传给 ViewReslover 视图解析器。  
9ViewReslover 解析后返回具体 View。  
10DispatcherServlet 根据 View 进行渲染视图（即将模型数据填充至视图中）。  
11DispatcherServlet 响应用户。  
33、SpringBoot 中常用注解及其底层实现  
1@SpringBootApplication 注解：这个注解标识了一个 SpringBoot 工程，它实际上是另外三个注解的组合，这三个注解是：  
a@SpringBootConfiguration：这个注解实际就是一个 @Configuration，表示启动类也是一个配置类  
b@EnableAutoConfiguration：向 Spring 容器中导入了一个 Selector，用来加载 ClassPath 下 SpringFactories 中所定义的自动配置类，将这些自动加载为配置 Bean  
c@ComponentScan：标识扫描路径，因为默认是没有配置实际扫描路径，所以 SpringBoot 扫描的路径是启动类所在的当前目录  
2@Bean 注解：用来定义 Bean，类似于 XML 中的 <bean> 标签，Spring 在启动时，会对加了 @Bean 注解的方法进行解析，将方法的名字做为 beanName，并通过执行方法得到 bean 对象  
3@Controller、@Service、@ResponseBody、@Autowired 都可以说  
34、SpringBoot 是如何启动 Tomcat 的  
1 首先，SpringBoot 在启动时会先创建一个 Spring 容器2 在创建 Spring 容器过程中，会利用 @ConditionalOnClass 技术来判断当前 classpath 中是否存在 Tomcat 依赖，如果存在则会生成一个启动 Tomcat 的 Bean3Spring 容器创建完之后，就会获取启动 Tomcat 的 Bean，并创建 Tomcat 对象，并绑定端口等，然后启动 Tomcat  
35、SpringBoot 中配置文件的加载顺序是怎样的？  
优先级从高到低，高优先级的配置覆盖低优先级的配置，所有配置会形成互补配置。  
1 命令行参数。所有的配置都可以在命令行上进行指定；2Java 系统属性（System.getProperties()）；  
3 操作系统环境变量 ；4jar 包外部的 application-{profile}.properties 或 application.yml(带 spring.profile) 配置文件  
5jar 包内部的 application-{profile}.properties 或 application.yml(带 spring.profile) 配置文件 再来加载不带 profile  
6jar 包外部的 application.properties 或 application.yml(不带 spring.profile) 配置文件  
7jar 包内部的 application.properties 或 application.yml(不带 spring.profile) 配置文件  
8@Configuration 注解类上的 @PropertySource  
36、Mybatis 存在哪些优点和缺点  
优点：  
1 基于 SQL 语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL 单独写，解除 sql 与程序代码的耦合，便于统一管理。2 与 JDBC 相比，减少了 50% 以上的代码量，消除了 JDBC 大量冗余的代码，不需要手动开关连接；3 很好的与各种数据库兼容（ 因为 MyBatis 使用 JDBC 来连接数据库，所以只要 JDBC 支持的数据库 MyBatis 都支持）。4 能够与 Spring 很好的集成；5 提供映射标签， 支持对象与数据库的 ORM 字段关系映射； 提供对象关系映射标签， 支持对象关系组件维护。缺点：  
1SQL 语句的编写工作量较大， 尤其当字段多、关联表多时， 对开发人员编写 SQL 语句的功底有一定要求。  
2SQL 语句依赖于数据库， 导致数据库移植性差， 不能随意更换数据库  
37、Mybatis 中 #{} 和 ${} 的区别是什么？  
1#{} 是预编译处理、是占位符， ${} 是字符串替换、是拼接符  
2Mybatis 在处理 #{} 时，会将 sql 中的 #{} 替换为? 号，调用 PreparedStatement 来赋值  
3Mybatis 在处理 ${} 时， 就是把 ${} 替换成变量的值，调用 Statement 来赋值  
4 使用 #{} 可以有效的防止 SQL 注入，提高系统安全性SQL 复制代码

```
-- 假设



password="1 or 1=1"



select * from user where name = #{name} and password = #{password} 将转为

select * from user where name = 'zhouyu' and password = '1 or 1=1'



select * from user where name = ${name} and password = ${password} 将转为

select * from user where name = zhouyu and password = 1 or 1=1
```

38、什么是 CAP 理论  
CAP 理论是分布式领域中非常重要的一个指导理论，C（Consistency）表示强一致性，A（Availability）表示可用性，P（Partition Tolerance）表示分区容错性，CAP 理论指出在目前的硬件条件下，一个分布式系统是必须要保证分区容错性的，而在这个前提下，分布式系统要么保证 CP，要么保证 AP，无法同时保证 CAP。  
分区容错性表示，一个系统虽然是分布式的，但是对外看上去应该是一个整体，不能由于分布式系统内部的某个结点挂点，或网络出现了故障，而导致系统对外出现异常。所以，对于分布式系统而言是一定要保证分区容错性的。  
强一致性表示，一个分布式系统中各个结点之间能及时的同步数据，在数据同步过程中，是不能对外提供服务的，不然就会造成数据不一致，所以强一致性和可用性是不能同时满足的。  
可用性表示，一个分布式系统对外要保证可用。  
39、什么是 BASE 理论  
由于不能同时满足 CAP，所以出现了 BASE 理论：  
1BA：Basically Available，表示基本可用，表示可以允许一定程度的不可用，比如由于系统故障，请求时间变长，或者由于系统故障导致部分非核心功能不可用，都是允许的  
2S：Soft state：表示分布式系统可以处于一种中间状态，比如数据正在同步  
3E：Eventually consistent，表示最终一致性，不要求分布式系统数据实时达到一致，允许在经过一段时间后再达到一致，在达到一致过程中，系统也是可用的  
40、什么是 RPC  
RPC，表示远程过程调用，对于 Java 这种面试对象语言，也可以理解为远程方法调用，RPC 调用和 HTTP 调用是有区别的，RPC 表示的是一种调用远程方法的方式，可以使用 HTTP 协议、或直接基于 TCP 协议来实现 RPC，在 Java 中，我们可以通过直接使用某个服务接口的代理对象来执行方法，而底层则通过构造 HTTP 请求来调用远端的方法，所以，有一种说法是 RPC 协议是 HTTP 协议之上的一种协议，也是可以理解的。  
41、分布式 ID 是什么？有哪些解决方案？  
在开发中，我们通常会需要一个唯一 ID 来标识数据，如果是单体架构，我们可以通过数据库的主键，或直接在内存中维护一个自增数字来作为 ID 都是可以的，但对于一个分布式系统，就会有可能会出现 ID 冲突，此时有以下解决方案：  
1uuid，这种方案复杂度最低，但是会影响存储空间和性能  
2 利用单机数据库的自增主键，作为分布式 ID 的生成器，复杂度适中，ID 长度较之 uuid 更短，但是受到单机数据库性能的限制，并发量大的时候，此方案也不是最优方案3 利用 redis、zookeeper 的特性来生成 id，比如 redis 的自增命令、zookeeper 的顺序节点，这种方案和单机数据库 (mysql) 相比，性能有所提高，可以适当选用4 雪花算法，一切问题如果能直接用算法解决，那就是最合适的，利用雪花算法也可以生成分布式 ID，底层原理就是通过某台机器在某一毫秒内对某一个数字自增，这种方案也能保证分布式架构中的系统 id 唯一，但是只能保证趋势递增。业界存在 tinyid、leaf 等开源中间件实现了雪花算法。42、分布式锁的使用场景是什么？有哪些实现方案？  
在单体架构中，多个线程都是属于同一个进程的，所以在线程并发执行时，遇到资源竞争时，可以利用 ReentrantLock、synchronized 等技术来作为锁，来控制共享资源的使用。  
而在分布式架构中，多个线程是可能处于不同进程中的，而这些线程并发执行遇到资源竞争时，利用 ReentrantLock、synchronized 等技术是没办法来控制多个进程中的线程的，所以需要分布式锁，意思就是，需要一个分布式锁生成器，分布式系统中的应用程序都可以来使用这个生成器所提供的锁，从而达到多个进程中的线程使用同一把锁。  
目前主流的分布式锁的实现方案有两种：  
1zookeeper：利用的是 zookeeper 的临时节点、顺序节点、watch 机制来实现的，zookeeper 分布式锁的特点是高一致性，因为 zookeeper 保证的是 CP，所以由它实现的分布式锁更可靠，不会出现混乱  
2redis：利用 redis 的 setnx、lua 脚本、消费订阅等机制来实现的，redis 分布式锁的特点是高可用，因为 redis 保证的是 AP，所以由它实现的分布式锁可能不可靠，不稳定（一旦 redis 中的数据出现了不一致），可能会出现多个客户端同时加到锁的情况  
43、什么是分布式事务？有哪些实现方案？  
在分布式系统中，一次业务处理可能需要多个应用来实现，比如用户发送一次下单请求，就涉及到订单系统创建订单、库存系统减库存，而对于一次下单，订单创建与减库存应该是要同时成功或同时失败的，但在分布式系统中，如果不做处理，就很有可能出现订单创建成功，但是减库存失败，那么解决这类问题，就需要用到分布式事务。常用解决方案有：  
1 本地消息表：创建订单时，将减库存消息加入在本地事务中，一起提交到数据库存入本地消息表，然后调用库存系统，如果调用成功则修改本地消息状态为成功，如果调用库存系统失败，则由后台定时任务从本地消息表中取出未成功的消息，重试调用库存系统2 消息队列：目前 RocketMQ 中支持事务消息，它的工作原理是：a 生产者订单系统先发送一条 half 消息到 Broker，half 消息对消费者而言是不可见的b 再创建订单，根据创建订单成功与否，向 Broker 发送 commit 或 rollbackc 并且生产者订单系统还可以提供 Broker 回调接口，当 Broker 发现一段时间 half 消息没有收到任何操作命令，则会主动调此接口来查询订单是否创建成功d 一旦 half 消息 commit 了，消费者库存系统就会来消费，如果消费成功，则消息销毁，分布式事务成功结束e 如果消费失败，则根据重试策略进行重试，最后还失败则进入死信队列，等待进一步处理3Seata：阿里开源的分布式事务框架，支持 AT、TCC 等多种模式，底层都是基于两阶段提交理论来实现的  
![](https://cdn.nlark.com/yuque/0/2021/png/365147/1627371194711-08b1bccb-cebb-4aa8-ae4d-14932f6dee74.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_25%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)  
44、什么是 ZAB 协议  
ZAB 协议是 Zookeeper 用来实现一致性的原子广播协议，该协议描述了 Zookeeper 是如何实现一致性的，分为三个阶段：  
1 领导者选举阶段：从 Zookeeper 集群中选出一个节点作为 Leader，所有的写请求都会由 Leader 节点来处理2 数据同步阶段：集群中所有节点中的数据要和 Leader 节点保持一致，如果不一致则要进行同步3 请求广播阶段：当 Leader 节点接收到写请求时，会利用两阶段提交来广播该写请求，使得写请求像事务一样在其他节点上执行，达到节点上的数据实时一致但值得注意的是，Zookeeper 只是尽量的在达到强一致性，实际上仍然只是最终一致性的。  
45、为什么 Zookeeper 可以用来作为注册中心  
可以利用 Zookeeper 的临时节点和 watch 机制来实现注册中心的自动注册和发现，另外 Zookeeper 中的数据都是存在内存中的，并且 Zookeeper 底层采用了 nio，多线程模型，所以 Zookeeper 的性能也是比较高的，所以可以用来作为注册中心，但是如果考虑到注册中心应该是注册可用性的话，那么 Zookeeper 则不太合适，因为 Zookeeper 是 CP 的，它注重的是一致性，所以集群数据不一致时，集群将不可用，所以用 Redis、Eureka、Nacos 来作为注册中心将更合适。  
46、Zookeeper 中的领导者选举的流程是怎样的？  
对于 Zookeeper 集群，整个集群需要从集群节点中选出一个节点作为 Leader，大体流程如下：  
1 集群中各个节点首先都是观望状态（LOOKING），一开始都会投票给自己，认为自己比较适合作为 leader2 然后相互交互投票，每个节点会收到其他节点发过来的选票，然后 pk，先比较 zxid，zxid 大者获胜，zxid 如果相等则比较 myid，myid 大者获胜3 一个节点收到其他节点发过来的选票，经过 PK 后，如果 PK 输了，则改票，此节点就会投给 zxid 或 myid 更大的节点，并将选票放入自己的投票箱中，并将新的选票发送给其他节点4 如果 pk 是平局则将接收到的选票放入自己的投票箱中5 如果 pk 赢了，则忽略所接收到的选票6 当然一个节点将一张选票放入到自己的投票箱之后，就会从投票箱中统计票数，看是否超过一半的节点都和自己所投的节点是一样的，如果超过半数，那么则认为当前自己所投的节点是 leader7 集群中每个节点都会经过同样的流程，pk 的规则也是一样的，一旦改票就会告诉给其他服务器，所以最终各个节点中的投票箱中的选票也将是一样的，所以各个节点最终选出来的 leader 也是一样的，这样集群的 leader 就选举出来了47、Zookeeper 集群中节点之间数据是如何同步的  
1 首先集群启动时，会先进行领导者选举，确定哪个节点是 Leader，哪些节点是 Follower 和 Observer2 然后 Leader 会和其他节点进行数据同步，采用发送快照和发送 Diff 日志的方式3 集群在工作过程中，所有的写请求都会交给 Leader 节点来进行处理，从节点只能处理读请求4Leader 节点收到一个写请求时，会通过两阶段机制来处理  
5Leader 节点会将该写请求对应的日志发送给其他 Follower 节点，并等待 Follower 节点持久化日志成功  
6Follower 节点收到日志后会进行持久化，如果持久化成功则发送一个 Ack 给 Leader 节点  
7 当 Leader 节点收到半数以上的 Ack 后，就会开始提交，先更新 Leader 节点本地的内存数据8 然后发送 commit 命令给 Follower 节点，Follower 节点收到 commit 命令后就会更新各自本地内存数据9 同时 Leader 节点还是将当前写请求直接发送给 Observer 节点，Observer 节点收到 Leader 发过来的写请求后直接执行更新本地内存数据10 最后 Leader 节点返回客户端写请求响应成功11 通过同步机制和两阶段提交机制来达到集群中节点数据一致48、Dubbo 支持哪些负载均衡策略  
1 随机：从多个服务提供者随机选择一个来处理本次请求，调用量越大则分布越均匀，并支持按权重设置随机概率2 轮询：依次选择服务提供者来处理请求， 并支持按权重进行轮询，底层采用的是平滑加权轮询算法3 最小活跃调用数：统计服务提供者当前正在处理的请求，下次请求过来则交给活跃数最小的服务器来处理4 一致性哈希：相同参数的请求总是发到同一个服务提供者[https://www.yuque.com/renyong-jmovm/ds/gwu187#yGxRv](https://www.yuque.com/renyong-jmovm/ds/gwu187#yGxRv)  
49、Dubbo 是如何完成服务导出的？  
1 首先 Dubbo 会将程序员所使用的 @DubboService 注解或 @Service 注解进行解析得到程序员所定义的服务参数，包括定义的服务名、服务接口、服务超时时间、服务协议等等，得到一个 ServiceBean。2 然后调用 ServiceBean 的 export 方法进行服务导出3 然后将服务信息注册到注册中心，如果有多个协议，多个注册中心，那就将服务按单个协议，单个注册中心进行注册4 将服务信息注册到注册中心后，还会绑定一些监听器，监听动态配置中心的变更5 还会根据服务协议启动对应的 Web 服务器或网络框架，比如 Tomcat、Netty 等50、Dubbo 是如何完成服务引入的？  
1 当程序员使用 @Reference 注解来引入一个服务时，Dubbo 会将注解和服务的信息解析出来，得到当前所引用的服务名、服务接口是什么2 然后从注册中心进行查询服务信息，得到服务的提供者信息，并存在消费端的服务目录中3 并绑定一些监听器用来监听动态配置中心的变更4 然后根据查询得到的服务提供者信息生成一个服务接口的代理对象，并放入 Spring 容器中作为 Bean51、Dubbo 的架构设计是怎样的？  
Dubbo 中的架构设计是非常优秀的，分为了很多层次，并且每层都是可以扩展的，比如：  
1Proxy 服务代理层，支持 JDK 动态代理、javassist 等代理机制  
2Registry 注册中心层，支持 Zookeeper、Redis 等作为注册中心  
3Protocol 远程调用层，支持 Dubbo、Http 等调用协议  
4Transport 网络传输层，支持 netty、mina 等网络传输框架  
5Serialize 数据序列化层，支持 JSON、Hessian 等序列化机制  
各层说明  
●config 配置层：对外配置接口，以 ServiceConfig, ReferenceConfig 为中心，可以直接初始化配置类，也可以通过 spring 解析配置生成配置类  
●proxy 服务代理层：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 ServiceProxy 为中心，扩展接口为 ProxyFactory  
●registry 注册中心层：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 RegistryFactory, Registry, RegistryService  
●cluster 路由层：封装多个提供者的路由及负载均衡，并桥接注册中心，以 Invoker 为中心，扩展接口为 Cluster, Directory, Router, LoadBalance  
●monitor 监控层：RPC 调用次数和调用时间监控，以 Statistics 为中心，扩展接口为 MonitorFactory, Monitor, MonitorService  
●protocol 远程调用层：封装 RPC 调用，以 Invocation, Result 为中心，扩展接口为 Protocol, Invoker, Exporter  
●exchange 信息交换层：封装请求响应模式，同步转异步，以 Request, Response 为中心，扩展接口为 Exchanger, ExchangeChannel, ExchangeClient, ExchangeServer  
●transport 网络传输层：抽象 mina 和 netty 为统一接口，以 Message 为中心，扩展接口为 Channel, Transporter, Client, Server, Codec  
●serialize 数据序列化层：可复用的一些工具，扩展接口为 Serialization, ObjectInput, ObjectOutput, ThreadPool  
关系说明  
●在 RPC 中，Protocol 是核心层，也就是只要有 Protocol + Invoker + Exporter 就可以完成非透明的 RPC 调用，然后在 Invoker 的主过程上 Filter 拦截点。  
●图中的 Consumer 和 Provider 是抽象概念，只是想让看图者更直观的了解哪些类分属于客户端与服务器端，不用 Client 和 Server 的原因是 Dubbo 在很多场景下都使用 Provider, Consumer, Registry, Monitor 划分逻辑拓普节点，保持统一概念。  
●而 Cluster 是外围概念，所以 Cluster 的目的是将多个 Invoker 伪装成一个 Invoker，这样其它人只要关注 Protocol 层 Invoker 即可，加上 Cluster 或者去掉 Cluster 对其它层都不会造成影响，因为只有一个提供者时，是不需要 Cluster 的。  
●Proxy 层封装了所有接口的透明化代理，而在其它层都以 Invoker 为中心，只有到了暴露给用户使用时，才用 Proxy 将 Invoker 转成接口，或将接口实现转成 Invoker，也就是去掉 Proxy 层 RPC 是可以 Run 的，只是不那么透明，不那么看起来像调本地服务一样调远程服务。  
●而 Remoting 实现是 Dubbo 协议的实现，如果你选择 RMI 协议，整个 Remoting 都不会用上，Remoting 内部再划为 Transport 传输层和 Exchange 信息交换层，Transport 层只负责单向消息传输，是对 Mina, Netty, Grizzly 的抽象，它也可以扩展 UDP 传输，而 Exchange 层是在传输层之上封装了 Request-Response 语义。  
●Registry 和 Monitor 实际上不算一层，而是一个独立的节点，只是为了全局概览，用层的方式画在一起。  
![](https://cdn.nlark.com/yuque/0/2021/jpeg/365147/1626176708107-3ad83a45-25b3-403e-beaf-02185e787ff9.jpeg?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_26%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10%2Fresize%2Cw_900%2Climit_0)  
52、Spring Cloud 有哪些常用组件，作用是什么？  
1Eureka：注册中心  
2Nacos：注册中心、配置中心  
3Consul：注册中心、配置中心  
4Spring Cloud Config：配置中心  
5Feign/OpenFeign：RPC 调用  
6Kong：服务网关  
7Zuul：服务网关  
8Spring Cloud Gateway：服务网关  
9Ribbon：负载均衡  
10Spring CLoud Sleuth：链路追踪  
11Zipkin：链路追踪  
12Seata：分布式事务  
13Dubbo：RPC 调用  
14Sentinel：服务熔断  
15Hystrix：服务熔断  
53、Spring Cloud 和 Dubbo 有哪些区别？  
Spring Cloud 是一个微服务框架，提供了微服务领域中的很多功能组件，Dubbo 一开始是一个 RPC 调用框架，核心是解决服务调用间的问题，Spring Cloud 是一个大而全的框架，Dubbo 则更侧重于服务调用，所以 Dubbo 所提供的功能没有 Spring Cloud 全面，但是 Dubbo 的服务调用性能比 Spring Cloud 高，不过 Spring Cloud 和 Dubbo 并不是对立的，是可以结合起来一起使用的。  
54、什么是服务雪崩？什么是服务限流？  
1 当服务 A 调用服务 B，服务 B 调用 C，此时大量请求突然请求服务 A，假如服务 A 本身能抗住这些请求，但是如果服务 C 抗不住，导致服务 C 请求堆积，从而服务 B 请求堆积，从而服务 A 不可用，这就是服务雪崩，解决方式就是服务降级和服务熔断。2 服务限流是指在高并发请求下，为了保护系统，可以对访问服务的请求进行数量上的限制，从而防止系统不被大量请求压垮，在秒杀中，限流是非常重要的。a 固定窗口（计数器）算法b 滑动窗口算法c 令牌桶算法d 漏桶算法55、什么是服务熔断？什么是服务降级？区别是什么？  
1 服务熔断是指，当服务 A 调用的某个服务 B 不可用时，上游服务 A 为了保证自己不受影响，从而不再调用服务 B，直接返回一个结果，减轻服务 A 和服务 B 的压力，直到服务 B 恢复。2 服务降级是指，当发现系统压力过载时，可以通过关闭某个服务，或限流某个服务来减轻系统压力，这就是服务降级。相同点：  
1 都是为了防止系统崩溃2 都让用户体验到某些功能暂时不可用不同点：熔断是下游服务故障触发的，降级是为了降低系统负载  
56、SOA、分布式、微服务之间有什么关系和区别？  
1 分布式架构是指将单体架构中的各个部分拆分，然后部署不同的机器或进程中去，SOA 和微服务基本上都是分布式架构的2SOA 是一种面向服务的架构，系统的所有服务都注册在总线上，当调用服务时，从总线上查找服务信息，然后调用  
3 微服务是一种更彻底的面向服务的架构，将系统中各个功能个体抽成一个个小的应用程序，基本保持一个应用对应的一个服务的架构57、BIO、NIO、AIO 分别是什么  
1BIO：同步阻塞 IO，使用 BIO 读取数据时，线程会阻塞住，并且需要线程主动去查询是否有数据可读，并且需要处理完一个 Socket 之后才能处理下一个 Socket  
2NIO：同步非阻塞 IO，使用 NIO 读取数据时，线程不会阻塞，但需要线程主动的去查询是否有 IO 事件  
3AIO：也叫做 NIO 2.0，异步非阻塞 IO，使用 AIO 读取数据时，线程不会阻塞，并且当有数据可读时会通知给线程，不需要线程主动去查询  
58、零拷贝是什么  
零拷贝指的是，应用程序在需要把内核中的一块区域数据转移到另外一块内核区域去时，不需要经过先复制到用户空间，再转移到目标内核区域去了，而直接实现转移。  
 ![](https://cdn.nlark.com/yuque/0/2021/gif/365147/1625820731086-61084cde-5942-4121-b7bb-a0a06e95d2b1.gif) ![](https://cdn.nlark.com/yuque/0/2021/gif/365147/1625820737332-3edf976d-4def-464d-9eee-e41ea3a0c5a4.gif) 59、Netty 是什么？和 Tomcat 有什么区别？特点是什么？  
Netty 是一个基于 NIO 的异步网络通信框架，性能高，封装了原生 NIO 编码的复杂度，开发者可以直接使用 Netty 来开发高效率的各种网络服务器，并且编码简单。  
Tomcat 是一个 Web 服务器，是一个 Servlet 容器，基本上 Tomcat 内部只会运行 Servlet 程序，并处理 HTTP 请求，而 Netty 封装的是底层 IO 模型，关注的是网络数据的传输，而不关心具体的协议，可定制性更高。  
Netty 的特点：  
1 异步、NIO 的网络通信框架2 高性能3 高扩展，高定制性4 易用性60、Netty 的线程模型是怎么样的  
Netty 同时支持 Reactor 单线程模型 、Reactor 多线程模型和 Reactor 主从多线程模型，用户可根据启动参数配置在这三种模型之间切换。  
![](https://cdn.nlark.com/yuque/0/2021/png/365147/1627369662263-ffe13b8e-5139-41fa-b27e-f5ac50245f3e.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_20%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)  
服务端启动时，通常会创建两个 NioEventLoopGroup 实例，对应了两个独立的 Reactor 线程池，bossGroup 负责处理客户端的连接请求，workerGroup 负责处理 I/O 相关的操作，执行系统 Task、定时任务 Task 等。用户可根据服务端引导类 ServerBootstrap 配置参数选择 Reactor 线程模型，进而最大限度地满足用户的定制化需求。  
61、Netty 的高性能体现在哪些方面  
1NIO 模型，用最少的资源做更多的事情。  
2 内存零拷贝，尽量减少不必要的内存拷贝，实现了更高效率的传输。3 内存池设计，申请的内存可以重用，主要指直接内存。内部实现是用一颗二叉查找树管理内存分配情况。4 串行化处理读写 ：避免使用锁带来的性能开销。即消息的处理尽可能再同一个线程内完成，期间不进行线程切换，这样就避免了多线程竞争和同步锁。表面上看，串行化设计似乎 CPU 利用率不高，并发程度不够。但是，通过调整 NIO 线程池的线程参数，可以同时启动多个串行化的线程并行运行，这种局部无锁化的串行线程设计相比一个队里 - 多个工作线程模型性能更优。5 高性能序列化协议 ：支持 protobuf 等高性能序列化协议。6 高效并发编程的体现 ：volatile 的大量、正确使用；CAS 和原子类的广泛使用；线程安全容器的使用；通过读写锁提升并发性能。62、Redis 有哪些数据结构？分别有哪些典型的应用场景？  
Redis 的数据结构有：  
1 字符串：可以用来做最简单的数据，可以缓存某个简单的字符串，也可以缓存某个 json 格式的字符串，Redis 分布式锁的实现就利用了这种数据结构，还包括可以实现计数器、分布式 ID2 哈希表：可以用来存储一些 key-value 对，更适合用来存储对象3 列表：Redis 的列表通过命令的组合，既可以当做栈，也可以当做队列来使用，可以用来缓存类似微信公众号、微博等消息流数据4 集合：和列表类似，也可以存储多个元素，但是不能重复，集合可以进行交集、并集、差集操作，从而可以实现类似，我和某人共同关注的人、朋友圈点赞等功能5 有序集合：集合是无序的，有序集合可以设置顺序，可以用来实现排行榜功能63、Redis 分布式锁底层是如何实现的？  
1 首先利用 setnx 来保证：如果 key 不存在才能获取到锁，如果 key 存在，则获取不到锁2 然后还要利用 lua 脚本来保证多个 redis 操作的原子性3 同时还要考虑到锁过期，所以需要额外的一个看门狗定时任务来监听锁是否需要续约4 同时还要考虑到 redis 节点挂掉后的情况，所以需要采用红锁的方式来同时向 N/2+1 个节点申请锁，都申请到了才证明获取锁成功，这样就算其中某个 redis 节点挂掉了，锁也不能被其他客户端获取到64、Redis 主从复制的核心原理  
全量同步：  
1 一般发生在从节点初始化的时候2 从节点发送 SYNC 命令连接主节点3 主节点接收到 SYNC 命名后，开始执行 BGSAVE 命令生成 RDB 文件，并使用缓冲区 replication buffer 记录在这个过程中接收到的写命令4 主节点 BGSAVE 命令执行完后，向所有从节点发送 RDB 文件，并在发送缓冲区 replication buffer 记录的写命令5 从节点收到 RDB 文件后丢弃所有旧数据，载入收到的 RDB 文件中的数据6 主节点 RDB 文件发送完毕后，开始向从节点发送缓冲区 replication buffer 中的写命令7 从节点完成 RDB 的载入后，开始接收客户端命令，并执行来自主节点缓冲区 replication buffer 的写命令增量同步：  
Redis 主节点每执行一个写命令就会向从节点异步发送相同的写命令，从节点接收并执行收到的写命令  
65、缓存穿透、缓存击穿、缓存雪崩分别是什么  
缓存中存放的大多都是热点数据，目的就是防止请求可以直接从缓存中获取到数据，而不用访问 Mysql。  
1 缓存雪崩：如果缓存中某一时刻大批热点数据同时过期，那么就可能导致大量请求直接访问 Mysql 了，解决办法就是在过期时间上增加一点随机值，另外如果搭建一个高可用的 Redis 集群也是防止缓存雪崩的有效手段2 缓存击穿：和缓存雪崩类似，缓存雪崩是大批热点数据失效，而缓存击穿是指某一个热点 key 突然失效，也导致了大量请求直接访问 Mysql 数据库，这就是缓存击穿，解决方案就是考虑这个热点 key 不设过期时间3 缓存穿透：假如某一时刻访问 redis 的大量 key 都在 redis 中不存在（比如黑客故意伪造一些乱七八糟的 key），那么也会给数据造成压力，这就是缓存穿透，解决方案是使用布隆过滤器，它的作用就是如果它认为一个 key 不存在，那么这个 key 就肯定不存在，所以可以在缓存之前加一层布隆过滤器来拦截不存在的 key66、Redis 和 Mysql 如何保证数据一致  
1 先更新 Mysql，再更新 Redis，如果更新 Redis 失败，可能仍然不一致2 先删除 Redis 缓存数据，再更新 Mysql，再次查询的时候在将数据添加到缓存中，这种方案能解决 1 方案的问题，但是在高并发下性能较低，而且仍然会出现数据不一致的问题，比如线程 1 删除了 Redis 缓存数据，正在更新 Mysql，此时另外一个查询再查询，那么就会把 Mysql 中老数据又查到 Redis 中3 延时双删，步骤是：先删除 Redis 缓存数据，再更新 Mysql，延迟几百毫秒再删除 Redis 缓存数据，这样就算在更新 Mysql 时，有其他线程读了 Mysql，把老数据读到了 Redis 中，那么也会被删除掉，从而把数据保持一致67、Explain 语句结果中各个字段分表表示什么  
<table><colgroup><col width="364"><col width="364"></colgroup><tbody><tr><td data-col="0"><ne-p data-lake-id="12012a6007eb9826ac20527c5d347936"><ne-text>列名</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="2585c4507d8398186c0b48fd410f5999"><ne-text>描述</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="091e5b2472876387dd687f96c6d6185b"><ne-text>id</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="750c22ea7eb58fe2f8d1efbb48907756"><ne-text>查询语句中每出现一个 SELECT 关键字，MySQL 就会为它分配一个唯一的 id 值，某些子查询会被优化为 join 查询，那么出现的 id 会一样</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="ad207a55feb49c1136e871fb3427560d"><ne-text>select_type</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="7fb424fca826f5dd9dbbc3ba59d8e65f"><ne-text>SELECT 关键字对应的那个查询的类型</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="83ed49e3e440ee028521d0d8e3dde76c"><ne-text>table</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="0c4a54c61f03f41e390e053115f642f1"><ne-text>表名</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="1320332b9224c241fa3619b535960dc7"><ne-text>partitions</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="63979907797c6b64947a02cc4fd5fe20"><ne-text>匹配的分区信息</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="17753c4182c6b360ad1e486212586be4"><ne-text>type</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="bcf2bc4bae23c13a1786ff535b3620f1"><ne-text>针对单表的查询方式（全表扫描、索引）</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="de5a386122273fc2740cc3a617399b91"><ne-text>possible_keys</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="910003ebdaefbd9a249e0400c8d46fd9"><ne-text>可能用到的索引</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="77982a95714a5ad7b8434c2edcfabf54"><ne-text>key</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="ed87586a608cc200120ccdd97f94a8d9"><ne-text>实际上使用的索引</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="e400d95487ca9fa2c860ea4de42d425b"><ne-text>key_len</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="3b1a4af7ea5fe8eb9928c576949e59c3"><ne-text>实际使用到的索引长度</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="2fd8e56f99bb20a94379d1083a89bab6"><ne-text>ref</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="5bf8c361939cdf0f5acdb4fc728651e8"><ne-text>当使用索引列等值查询时，与索引列进行等值匹配的对象信息</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="7d753131b5e7d45900545aba67322197"><ne-text>rows</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="df2c13edcf3165179420f87cd96ccd2d"><ne-text>预估的需要读取的记录条数</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="95e757095b5ae8c93dfb11c9a8deed88"><ne-text>filtered</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="79015ce59ac256989a39fda7e2c4100d"><ne-text>某个表经过搜索条件过滤后剩余记录条数的百分比</ne-text><br></ne-p></td></tr><tr><td data-col="0"><ne-p data-lake-id="629b2ee7fb223b5faf261aed85c153bd"><ne-text>Extra</ne-text><br></ne-p></td><td data-col="1"><ne-p data-lake-id="ddc15134277add0cfb55062aa4511a12"><ne-text>一些额外的信息，比如排序等</ne-text><br></ne-p></td></tr></tbody></table>68、索引覆盖是什么  
索引覆盖就是一个 SQL 在执行时，可以利用索引来快速查找，并且此 SQL 所要查询的字段在当前索引对应的字段中都包含了，那么就表示此 SQL 走完索引后不用回表了，所需要的字段都在当前索引的叶子节点上存在，可以直接作为结果返回了  
69、最左前缀原则是什么  
当一个 SQL 想要利用索引是，就一定要提供该索引所对应的字段中最左边的字段，也就是排在最前面的字段，比如针对 a,b,c 三个字段建立了一个联合索引，那么在写一个 sql 时就一定要提供 a 字段的条件，这样才能用到联合索引，这是由于在建立 a,b,c 三个字段的联合索引时，底层的 B + 树是按照 a,b,c 三个字段从左往右去比较大小进行排序的，所以如果想要利用 B + 树进行快速查找也得符合这个规则  
70、Innodb 是如何实现事务的  
Innodb 通过 Buffer Pool，LogBuffer，Redo Log，Undo Log 来实现事务，以一个 update 语句为例：  
1Innodb 在收到一个 update 语句后，会先根据条件找到数据所在的页，并将该页缓存在 Buffer Pool 中  
2 执行 update 语句，修改 Buffer Pool 中的数据，也就是内存中的数据3 针对 update 语句生成一个 RedoLog 对象，并存入 LogBuffer 中4 针对 update 语句生成 undolog 日志，用于事务回滚5 如果事务提交，那么则把 RedoLog 对象进行持久化，后续还有其他机制将 Buffer Pool 中所修改的数据页持久化到磁盘中6 如果事务回滚，则利用 undolog 日志进行回滚71、B 树和 B + 树的区别，为什么 Mysql 使用 B + 树  
B 树的特点：  
1 节点排序2 一个节点了可以存多个元素，多个元素也排序了B + 树的特点：  
1 拥有 B 树的特点2 叶子节点之间有指针3 非叶子节点上的元素在叶子节点上都冗余了，也就是叶子节点中存储了所有的元素，并且排好顺序Mysql 索引使用的是 B + 树，因为索引是用来加快查询的，而 B + 树通过对数据进行排序所以是可以提高查询速度的，然后通过一个节点中可以存储多个元素，从而可以使得 B + 树的高度不会太高，在 Mysql 中一个 Innodb 页就是一个 B + 树节点，一个 Innodb 页默认 16kb，所以一般情况下一颗两层的 B + 树可以存 2000 万行左右的数据，然后通过利用 B + 树叶子节点存储了所有数据并且进行了排序，并且叶子节点之间有指针，可以很好的支持全表扫描，范围查找等 SQL 语句。  
72、Mysql 锁有哪些，如何理解  
按锁粒度分类：  
1 行锁：锁某行数据，锁粒度最小，并发度高2 表锁：锁整张表，锁粒度最大，并发度低3 间隙锁：锁的是一个区间还可以分为：  
1 共享锁：也就是读锁，一个事务给某行数据加了读锁，其他事务也可以读，但是不能写2 排它锁：也就是写锁，一个事务给某行数据加了写锁，其他事务不能读，也不能写还可以分为：  
1 乐观锁：并不会真正的去锁某行记录，而是通过一个版本号来实现的2 悲观锁：上面所的行锁、表锁等都是悲观锁在事务的隔离级别实现中，就需要利用锁来解决幻读  
73、Mysql 慢查询该如何优化？  
1 检查是否走了索引，如果没有则优化 SQL 利用索引2 检查所利用的索引，是否是最优索引3 检查所查字段是否都是必须的，是否查询了过多字段，查出了多余数据4 检查表中数据是否过多，是否应该进行分库分表了5 检查数据库实例所在机器的性能配置，是否太低，是否可以适当增加资源74、消息队列有哪些作用  
1 解耦：使用消息队列来作为两个系统之间的通讯方式，两个系统不需要相互依赖了2 异步：系统 A 给消息队列发送完消息之后，就可以继续做其他事情了3 流量削峰：如果使用消息队列的方式来调用某个系统，那么消息将在队列中排队，由消费者自己控制消费速度75、死信队列是什么？延时队列是什么？  
1 死信队列也是一个消息队列，它是用来存放那些没有成功消费的消息的，通常可以用来作为消息重试2 延时队列就是用来存放需要在指定时间被处理的元素的队列，通常可以用来处理一些具有过期性操作的业务，比如十分钟内未支付则取消订单76、Kafka 为什么吞吐量高  
Kafka 的生产者采用的是异步发送消息机制，当发送一条消息时，消息并没有发送到 Broker 而是缓存起来，然后直接向业务返回成功，当缓存的消息达到一定数量时再批量发送给 Broker。这种做法减少了网络 io，从而提高了消息发送的吞吐量，但是如果消息生产者宕机，会导致消息丢失，业务出错，所以理论上 kafka 利用此机制提高了性能却降低了可靠性。  
77、Kafka 的 Pull 和 Push 分别有什么优缺点  
1pull 表示消费者主动拉取，可以批量拉取，也可以单条拉取，所以 pull 可以由消费者自己控制，根据自己的消息处理能力来进行控制，但是消费者不能及时知道是否有消息，可能会拉到的消息为空  
2push 表示 Broker 主动给消费者推送消息，所以肯定是有消息时才会推送，但是消费者不能按自己的能力来消费消息，推过来多少消息，消费者就得消费多少消息，所以可能会造成网络堵塞，消费者压力大等问题  
78、RocketMQ 的事务消息是如何实现的  
![](https://cdn.nlark.com/yuque/0/2021/png/365147/1627371194711-08b1bccb-cebb-4aa8-ae4d-14932f6dee74.png?x-oss-process=image%2Fwatermark%2Ctype_d3F5LW1pY3JvaGVp%2Csize_25%2Ctext_5Zu-54G15a2m6Zmi5ZGo55Gc%2Ccolor_FFFFFF%2Cshadow_50%2Ct_80%2Cg_se%2Cx_10%2Cy_10)  
a 生产者订单系统先发送一条 half 消息到 Broker，half 消息对消费者而言是不可见的b 再创建订单，根据创建订单成功与否，向 Broker 发送 commit 或 rollbackc 并且生产者订单系统还可以提供 Broker 回调接口，当 Broker 发现一段时间 half 消息没有收到任何操作命令，则会主动调此接口来查询订单是否创建成功d 一旦 half 消息 commit 了，消费者库存系统就会来消费，如果消费成功，则消息销毁，分布式事务成功结束e 如果消费失败，则根据重试策略进行重试，最后还失败则进入死信队列，等待进一步处理79、消息队列如何保证消息可靠传输  
消息可靠传输代表了两层意思，既不能多也不能少。  
1 为了保证消息不多，也就是消息不能重复，也就是生产者不能重复生产消息，或者消费者不能重复消费消息2 首先要确保消息不多发，这个不常出现，也比较难控制，因为如果出现了多发，很大的原因是生产者自己的原因，如果要避免出现问题，就需要在消费端做控制3 要避免不重复消费，最保险的机制就是消费者实现幂等性，保证就算重复消费，也不会有问题，通过幂等性，也能解决生产者重复发送消息的问题4 消息不能少，意思就是消息不能丢失，生产者发送的消息，消费者一定要能消费到，对于这个问题，就要考虑两个方面5 生产者发送消息时，要确认 broker 确实收到并持久化了这条消息，比如 RabbitMQ 的 confirm 机制，Kafka 的 ack 机制都可以保证生产者能正确的将消息发送给 broker6broker 要等待消费者真正确认消费到了消息时才删除掉消息，这里通常就是消费端 ack 机制，消费者接收到一条消息后，如果确认没问题了，就可以给 broker 发送一个 ack，broker 接收到 ack 后才会删除消息  
80、TCP 的三次握手和四次挥手  
TCP 协议是 7 层网络协议中的传输层协议，负责数据的可靠传输。  
在建立 TCP 连接时，需要通过三次握手来建立，过程是：  
1 客户端向服务端发送一个 SYN2 服务端接收到 SYN 后，给客户端发送一个 SYN_ACK3 客户端接收到 SYN_ACK 后，再给服务端发送一个 ACK在断开 TCP 连接时，需要通过四次挥手来断开，过程是：  
1 客户端向服务端发送 FIN2 服务端接收 FIN 后，向客户端发送 ACK，表示我接收到了断开连接的请求，客户端你可以不发数据了，不过服务端这边可能还有数据正在处理3 服务端处理完所有数据后，向客户端发送 FIN，表示服务端现在可以断开连接4 客户端收到服务端的 FIN，向服务端发送 ACK，表示客户端也会断开连接了
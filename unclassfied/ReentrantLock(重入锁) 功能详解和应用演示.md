> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/takumicx/p/9338983.html#reentrantlock%E7%AE%80%E4%BB%8B) 目录

*   [2. ReentrantLock 和 synchronized 的相同点](#2-reentrantlock和synchronized的相同点)
    *   [2.1 ReentrantLock 是独占锁且可重入的](#21-reentrantlock是独占锁且可重入的)
*   [3. ReentrantLock 相比 synchronized 的额外功能](#3-reentrantlock相比synchronized的额外功能)
    *   [3.1 ReentrantLock 可以实现公平锁。](#31-reentrantlock可以实现公平锁。)
    *   [3.2 .ReentrantLock 可响应中断](#32-reentrantlock可响应中断)
    *   [3.3 获取锁时限时等待](#33-获取锁时限时等待)
*   [4. 结合 Condition 实现等待通知机制](#4-结合condition实现等待通知机制)
    *   [4.1 Condition 使用简介](#41-condition使用简介)
    *   [4.2 使用 Condition 实现简单的阻塞队列](#42-使用condition实现简单的阻塞队列)
*   [5. 总结](#5-总结)

jdk 中独占锁的实现除了使用关键字 synchronized 外, 还可以使用 ReentrantLock。虽然在性能上 ReentrantLock 和 synchronized 没有什么区别，但 ReentrantLock 相比 synchronized 而言功能更加丰富，使用起来更为灵活，也更适合复杂的并发场景。

2. ReentrantLock 和 synchronized 的相同点
------------------------------------

### 2.1 ReentrantLock 是独占锁且可重入的

*   例子

```
public class ReentrantLockTest {

    public static void main(String[] args) throws InterruptedException {

        ReentrantLock lock = new ReentrantLock();

        for (int i = 1; i <= 3; i++) {
            lock.lock();
        }

        for(int i=1;i<=3;i++){
            try {

            } finally {
                lock.unlock();
            }
        }
    }
}
```

上面的代码通过`lock()`方法先获取锁三次，然后通过`unlock()`方法释放锁 3 次，程序可以正常退出。从上面的例子可以看出, ReentrantLock 是可以重入的锁, 当一个线程获取锁时, 还可以接着重复获取多次。在加上 ReentrantLock 的的独占性，我们可以得出以下 ReentrantLock 和 synchronized 的相同点。

*   1.ReentrantLock 和 synchronized 都是独占锁, 只允许线程互斥的访问临界区。但是实现上两者不同: synchronized 加锁解锁的过程是隐式的, 用户不用手动操作, 优点是操作简单，但显得不够灵活。一般并发场景使用 synchronized 的就够了；ReentrantLock 需要手动加锁和解锁, 且解锁的操作尽量要放在 finally 代码块中, 保证线程正确释放锁。ReentrantLock 操作较为复杂，但是因为可以手动控制加锁和解锁过程, 在复杂的并发场景中能派上用场。
    
*   2.ReentrantLock 和 synchronized 都是可重入的。synchronized 因为可重入因此可以放在被递归执行的方法上, 且不用担心线程最后能否正确释放锁；而 ReentrantLock 在重入时要却确保重复获取锁的次数必须和重复释放锁的次数一样，否则可能导致其他线程无法获得该锁。
    

3. ReentrantLock 相比 synchronized 的额外功能
--------------------------------------

### 3.1 ReentrantLock 可以实现公平锁。

公平锁是指当锁可用时, 在锁上等待时间最长的线程将获得锁的使用权。而非公平锁则随机分配这种使用权。和 synchronized 一样，默认的 ReentrantLock 实现是非公平锁, 因为相比公平锁，非公平锁性能更好。当然公平锁能防止饥饿, 某些情况下也很有用。在创建 ReentrantLock 的时候通过传进参数`true`创建公平锁, 如果传入的是`false`或没传参数则创建的是非公平锁

```
ReentrantLock lock = new ReentrantLock(true);
```

继续跟进看下源码

```
/**
 * Creates an instance of {@code ReentrantLock} with the
 * given fairness policy.
 *
 * @param fair {@code true} if this lock should use a fair ordering policy
 */
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

可以看到公平锁和非公平锁的实现关键在于成员变量`sync`的实现不同, 这是锁实现互斥同步的核心。以后有机会我们再细讲。

*   一个公平锁的例子

```
public class ReentrantLockTest {

    static Lock lock = new ReentrantLock(true);

    public static void main(String[] args) throws InterruptedException {

        for(int i=0;i<5;i++){
            new Thread(new ThreadDemo(i)).start();
        }

    }

    static class ThreadDemo implements Runnable {
        Integer id;

        public ThreadDemo(Integer id) {
            this.id = id;
        }

        @Override

      public void run() {
            try {
                TimeUnit.MILLISECONDS.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            for(int i=0;i<2;i++){
                lock.lock();
                System.out.println("获得锁的线程："+id);
                lock.unlock();
            }
        }
    }
}
```

*   公平锁结果  
    ![](https://images2018.cnblogs.com/blog/1422237/201807/1422237-20180719230013861-1075860918.png)

我们开启 5 个线程, 让每个线程都获取释放锁两次。为了能更好的观察到结果, 在每次获取锁前让线程休眠 10 毫秒。可以看到线程几乎是轮流的获取到了锁。如果我们改成非公平锁, 再看下结果

*   非公平锁结果  
    ![](https://images2018.cnblogs.com/blog/1422237/201807/1422237-20180719230043730-1158282129.png)

线程会重复获取锁。如果申请获取锁的线程足够多, 那么可能会造成某些线程长时间得不到锁。这就是非公平锁的 “饥饿” 问题。

*   公平锁和非公平锁该如何选择  
    大部分情况下我们使用非公平锁，因为其性能比公平锁好很多。但是公平锁能够避免线程饥饿，某些情况下也很有用。

### 3.2 .ReentrantLock 可响应中断

当使用 synchronized 实现锁时, 阻塞在锁上的线程除非获得锁否则将一直等待下去，也就是说这种无限等待获取锁的行为无法被中断。而 ReentrantLock 给我们提供了一个可以响应中断的获取锁的方法`lockInterruptibly()`。该方法可以用来解决死锁问题。

*   响应中断的例子

```
public class ReentrantLockTest {
    static Lock lock1 = new ReentrantLock();
    static Lock lock2 = new ReentrantLock();
    public static void main(String[] args) throws InterruptedException {

        Thread thread = new Thread(new ThreadDemo(lock1, lock2));//该线程先获取锁1,再获取锁2
        Thread thread1 = new Thread(new ThreadDemo(lock2, lock1));//该线程先获取锁2,再获取锁1
        thread.start();
        thread1.start();
        thread.interrupt();//是第一个线程中断
    }

    static class ThreadDemo implements Runnable {
        Lock firstLock;
        Lock secondLock;
        public ThreadDemo(Lock firstLock, Lock secondLock) {
            this.firstLock = firstLock;
            this.secondLock = secondLock;
        }
        @Override
        public void run() {
            try {
                firstLock.lockInterruptibly();
                TimeUnit.MILLISECONDS.sleep(10);//更好的触发死锁
                secondLock.lockInterruptibly();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                firstLock.unlock();
                secondLock.unlock();
                System.out.println(Thread.currentThread().getName()+"正常结束!");
            }
        }
    }
}
```

*   结果  
    ![](https://images2018.cnblogs.com/blog/1422237/201807/1422237-20180719230113736-1002051172.png)

构造死锁场景: 创建两个子线程, 子线程在运行时会分别尝试获取两把锁。其中一个线程先获取锁 1 在获取锁 2，另一个线程正好相反。如果没有外界中断，该程序将处于死锁状态永远无法停止。我们通过使其中一个线程中断，来结束线程间毫无意义的等待。被中断的线程将抛出异常，而另一个线程将能获取锁后正常结束。

### 3.3 获取锁时限时等待

ReentrantLock 还给我们提供了获取锁限时等待的方法`tryLock()`, 可以选择传入时间参数, 表示等待指定的时间, 无参则表示立即返回锁申请的结果: true 表示获取锁成功, false 表示获取锁失败。我们可以使用该方法配合失败重试机制来更好的解决死锁问题。

*   更好的解决死锁的例子

```
public class ReentrantLockTest {
    static Lock lock1 = new ReentrantLock();
    static Lock lock2 = new ReentrantLock();
    public static void main(String[] args) throws InterruptedException {

        Thread thread = new Thread(new ThreadDemo(lock1, lock2));//该线程先获取锁1,再获取锁2
        Thread thread1 = new Thread(new ThreadDemo(lock2, lock1));//该线程先获取锁2,再获取锁1
        thread.start();
        thread1.start();
    }

    static class ThreadDemo implements Runnable {
        Lock firstLock;
        Lock secondLock;
        public ThreadDemo(Lock firstLock, Lock secondLock) {
            this.firstLock = firstLock;
            this.secondLock = secondLock;
        }
        @Override
        public void run() {
            try {
                while(!lock1.tryLock()){
                    TimeUnit.MILLISECONDS.sleep(10);
                }
                while(!lock2.tryLock()){
                    lock1.unlock();
                    TimeUnit.MILLISECONDS.sleep(10);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                firstLock.unlock();
                secondLock.unlock();
                System.out.println(Thread.currentThread().getName()+"正常结束!");
            }
        }
    }
}
```

*   结果  
    ![](https://images2018.cnblogs.com/blog/1422237/201807/1422237-20180719230215064-1644661582.png)

线程通过调用`tryLock()`方法获取锁, 第一次获取锁失败时会休眠 10 毫秒, 然后重新获取，直到获取成功。第二次获取失败时, 首先会释放第一把锁, 再休眠 10 毫秒, 然后重试直到成功为止。线程获取第二把锁失败时将会释放第一把锁，这是解决死锁问题的关键, 避免了两个线程分别持有一把锁然后相互请求另一把锁。

4. 结合 Condition 实现等待通知机制
------------------------

使用 synchronized 结合 Object 上的 wait 和 notify 方法可以实现线程间的等待通知机制。ReentrantLock 结合 Condition 接口同样可以实现这个功能。而且相比前者使用起来更清晰也更简单。

### 4.1 Condition 使用简介

Condition 由 ReentrantLock 对象创建, 并且可以同时创建多个

```
static Condition notEmpty = lock.newCondition();

static Condition notFull = lock.newCondition();
```

Condition 接口在使用前必须先调用 ReentrantLock 的 lock() 方法获得锁。之后调用 Condition 接口的 await() 将释放锁, 并且在该 Condition 上等待, 直到有其他线程调用 Condition 的 signal() 方法唤醒线程。使用方式和 wait,notify 类似。

*   一个使用 condition 的简单例子

```
public class ConditionTest {

    static ReentrantLock lock = new ReentrantLock();
    static Condition condition = lock.newCondition();
    public static void main(String[] args) throws InterruptedException {

        lock.lock();
        new Thread(new SignalThread()).start();
        System.out.println("主线程等待通知");
        try {
            condition.await();
        } finally {
            lock.unlock();
        }
        System.out.println("主线程恢复运行");
    }
    static class SignalThread implements Runnable {

        @Override
        public void run() {
            lock.lock();
            try {
                condition.signal();
                System.out.println("子线程通知");
            } finally {
                lock.unlock();
            }
        }
    }
}
```

*   运行结果  
    ![](https://images2018.cnblogs.com/blog/1422237/201807/1422237-20180719230234682-2105208491.png)

### 4.2 使用 Condition 实现简单的阻塞队列

阻塞队列是一种特殊的先进先出队列, 它有以下几个特点  
1. 入队和出队线程安全  
2. 当队列满时, 入队线程会被阻塞; 当队列为空时, 出队线程会被阻塞。

*   阻塞队列的简单实现

```
public class MyBlockingQueue<E> {

    int size;//阻塞队列最大容量

    ReentrantLock lock = new ReentrantLock();

    LinkedList<E> list=new LinkedList<>();//队列底层实现

    Condition notFull = lock.newCondition();//队列满时的等待条件
    Condition notEmpty = lock.newCondition();//队列空时的等待条件

    public MyBlockingQueue(int size) {
        this.size = size;
    }

    public void enqueue(E e) throws InterruptedException {
        lock.lock();
        try {
            while (list.size() ==size)//队列已满,在notFull条件上等待
                notFull.await();
            list.add(e);//入队:加入链表末尾
            System.out.println("入队：" +e);
            notEmpty.signal(); //通知在notEmpty条件上等待的线程
        } finally {
            lock.unlock();
        }
    }

    public E dequeue() throws InterruptedException {
        E e;
        lock.lock();
        try {
            while (list.size() == 0)//队列为空,在notEmpty条件上等待
                notEmpty.await();
            e = list.removeFirst();//出队:移除链表首元素
            System.out.println("出队："+e);
            notFull.signal();//通知在notFull条件上等待的线程
            return e;
        } finally {
            lock.unlock();
        }
    }
}
```

*   测试代码

```
public static void main(String[] args) throws InterruptedException {

    MyBlockingQueue<Integer> queue = new MyBlockingQueue<>(2);
    for (int i = 0; i < 10; i++) {
        int data = i;
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    queue.enqueue(data);
                } catch (InterruptedException e) {

                }
            }
        }).start();

    }
    for(int i=0;i<10;i++){
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Integer data = queue.dequeue();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

}
```

*   运行结果  
    ![](https://images2018.cnblogs.com/blog/1422237/201807/1422237-20180719230254831-1756382721.png)

5. 总结
-----

ReentrantLock 是可重入的独占锁。比起 synchronized 功能更加丰富，支持公平锁实现，支持中断响应以及限时等待等等。可以配合一个或多个 Condition 条件方便的实现等待通知机制。
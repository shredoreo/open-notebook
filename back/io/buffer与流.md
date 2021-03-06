## NIO

## 为什么要使用 NIO?

NIO 的创建目的是为了让 Java 程序员可以实现高速 I/O 而无需编写自定义的本机代码。NIO 将最耗时的 I/O 操作 (即填充和提取缓冲区) 转移回操作系统，因而可以极大地提高速度。

## 流与块的比较

原来的 I/O 库 (在 java.io.* 中) 与 NIO 最重要的区别是数据打包和传输的方式。正如前面提到的，原来的 I/O 以流的方式处理数据，而 NIO 以块的方式处理数据。
面向流 的 I/O 系统一次一个字节地处理数据。一个输入流产生一个字节的数据，一个输出流消费一个字节的数据。为流式数据创建过滤器非常容易。链接几个过滤器，以便每个过滤器只负责单个复杂处理机制的一部分，这样也是相对简单的。不利的一面是，面向流的 I/O 通常相当慢。
一个 面向块 的 I/O 系统以块的形式处理数据。每一个操作都在一步中产生或者消费一个数据块。按块处理数据比按 (流式的) 字节处理数据要快得多。但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性。

## NIO 的 buffer 机制

NIO 性能的优势就来源于缓冲的机制，不管是读或者写都需要以块的形式写入到缓冲区中。NIO 实际上让我们对 IO 的操作更接近于操作系统的实际过程。
所有的系统 I/O 都分为两个阶段：等待就绪和操作。举例来说，读函数，分为等待系统可读和真正的读；同理，写函数分为等待网卡可以写和真正的写。
以 socket 为例：
先从应用层获取数据到内核的缓冲区，然后再从内核的缓冲区复制到进程的缓冲区。所以实际上底层的机制也是不断利用缓冲区来读写数据的。即使传统 IO 抽象成了从流直接读取数据，但本质上也依然是利用缓冲区来读取和写入数据。
所以，为了更好的理解 nio，我们就需要知道 IO 的底层机制，这样对我们将来理解 channel 和 buffer 就打下了基础。这里简单提一下，我们可以把 bufffer 就理解为内核缓冲区，所以不论读写，自然都要经过这个区域，读的话，先从设备读取数据到内核，再读到进程缓冲区，写的话，先从进程缓冲区写到内核，再从内核写回设备。

## NIO 的非阻塞机制

NIO 的非阻塞模式，使一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取。而不是保持线程阻塞，所以直至数据变的可以读取之前，该线程可以继续做其他的事情。 非阻塞写也是如此。一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。 线程通常将非阻塞 IO 的空闲时间用于在其它通道上执行 IO 操作，所以一个单独的线程现在可以管理多个输入和输出通道（channel）。

下图是几种常见 I/O 模型的对比：

以 socket.read() 为例子：

传统的 BIO 里面 socket.read()，如果 TCP RecvBuffer 里没有数据，函数会一直阻塞，直到收到数据，返回读到的数据。

对于 NIO，如果 TCP RecvBuffer 有数据，就把数据从网卡读到内存，并且返回给用户；反之则直接返回 0，永远不会阻塞。所以我们可以 NIO 实现同时监听多个 IO 通道，然后不断的轮询寻找可以读写的设备。

NIO 的 IO 模型可以理解为是 IO 多路复用模型和非阻塞模型，同时还有事件驱动模型。
这里需要知道一点，就是 IO 多路复用是一定需要实现非阻塞的。

## 小结

NIO 相对于 IO 流的优势：

- 非阻塞
- buffer 机制
- 流替代块

参考：

- [https://tech.meituan.com/nio.html](https://link.zhihu.com/?target=https%3A//tech.meituan.com/nio.html)
- [http://www.importnew.com/19816.html](https://link.zhihu.com/?target=http%3A//www.importnew.com/19816.html)
- [https://www.ibm.com/developerworks/cn/education/java/j-nio/j-nio.html](https://link.zhihu.com/?target=https%3A//www.ibm.com/developerworks/cn/education/java/j-nio/j-nio.html)



> 来源知乎 https://www.zhihu.com/question/59356897?ivk_sa=1024320u


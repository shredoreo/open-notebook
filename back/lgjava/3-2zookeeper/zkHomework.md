**编程题一：**

  在基于Netty的自定义RPC的案例基础上，进行改造。基于Zookeeper实现简易版服务的注册与发现机制

**要求完成改造版本：**

\1. 启动2个服务端，可以将IP及端口信息自动注册到Zookeeper

\2. 客户端启动时，从Zookeeper中获取所有服务提供端节点信息，客户端与每一个服务端都建立连接

\3. 某个服务端下线后，Zookeeper注册列表会自动剔除下线的服务端节点，客户端与下线的服务端断开连接

\4. 服务端重新上线，客户端能感知到，并且与重新上线的服务端重新建立连接

**编程题二：**

  在“编程题一”的基础上，实现基于Zookeeper的简易版负载均衡策略

**要求完成改造版本：**

1. Zookeeper记录每个服务端的最后一次响应时间，有效时间为5秒，5s内如果该服务端没有新的请求，响应时间清零或失效。（如下图所示）

   ![img](https://s0.lgstatic.com/i/image/M00/47/0B/Ciqc1F9HGqWAez0dAACwX3IKKGc361.png)

2. 当客户端发起调用，每次都选择最后一次响应时间短的服务端进行服务调用，如果时间一致，随机选取一个服务端进行调用，从而实现负载均衡



 \-------------------------------------------------------------------------

作业资料说明：



1、提供资料：代码工程、验证及讲解视频。

2、讲解内容包含：题目分析、实现思路、代码讲解。

3、效果视频验证：

   编程题一：服务端的上线与下线，客户端能动态感知，并能重新构成负载均衡。

   编程题二：作业完成情况下，选择性能好的服务器处理（响应时间短的服务器即为性能好）。Zookeeper记录客户端响应有效时间为5s，超时判定该客户端失效。





思路

- 服务端

1. 启动rpc服务端前，连接zookeeper，创建永久根节点/netty，用于管理netty的Rpc服务
2. 启动netty时，将ip和端口注册到zk，在根节点下创建临时节点，名称为ip#port，值为0
3. 通过命令行参数传入端口，每次启动不同端口



- 客户端

1. 先启动zookeeper，获取/netty下的子节点，作为服务端列表。并创建响应的netty连接。
2. 监听/netty节点及其子节点的变化，以感知服务端的上下线
3. 启动定时线程，定时上报，更新状态，清除超时节点的值
4. 发起客户端请求前，获取服务端节点的值，获取上次请求耗时，优先取请求时间小的；若时间一样，随机选择服务端。
5. 每次请求记录请求时间，并保存到服务端节点，规则：timestamp#cost，当前时间戳#本次请求耗时

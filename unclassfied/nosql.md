redis

# nosql

数据之间无关系，易扩展。

非常高的读写性能，

多样灵活的数据模型。

**KV、cache、persistence**



对于亲戚关系，

主从复制：容灾备份、

读写分离：

分表分库：跟业务相关的，放一个库；一些冷数据，放另一个。

传统的关系型数据库，

## 数据结构

### BSON

bson是类似于json的。



### 聚合模型

1. KV
2. BSON
3. 列族
4. 图形



## 数据库分类

1. KV键值：Redis
2. 文档型数据库（bson）：典型：mongodb
3. 列存储数据库
4. 图关系数据库：社交网络、推荐系统等。Neo4j,InfoGrid



## CAP和BASE原理

###CAP

- Consistency：

  Consistency 中文叫做"一致性"。意思是，写操作之后的读操作，必须返回该值。

- Availability：

  Availability 中文叫做"可用性"，意思是只要收到用户的请求，服务器就必须给出回应。

- Partition tolerance：

  大多数分布式系统都分布在多个子网络。每个子网络就叫做一个区（partition）。分区容错的意思是，区间通信可能失败。比如，一台服务器放在中国，另一台服务器放在美国，这就是两个区，它们之间可能无法通信。**一般来说，分区容错无法避免，因此可以认为 CAP 的 P 总是成立。**CAP 定理告诉我们，剩下的 C 和 A 无法同时做到。

Cap三进二：

CA

AP

CP

![](D:\ProgramData\md\CAP.png)

> 一致性和可用性，为什么不可能同时成立？答案很简单，因为可能通信失败（即出现分区容错）。
>
> 如果保证 G2 的一致性，那么 G1 必须在写操作时，锁定 G2 的读操作和写操作。只有数据同步后，才能重新开放读写。锁定期间，G2 不能读写，没有可用性不。
>
> 如果保证 G2 的可用性，那么势必不能锁定 G2，所以一致性不成立。
>
> 综上所述，G2 无法同时做到一致性和可用性。系统设计时只能选择一个目标。如果追求一致性，那么无法保证所有节点的可用性；如果追求所有节点的可用性，那就没法做到一致性。

###BASE：

为了解决关系数据库强一致性引起的问题而引起可用性降低而提出的方案。

Basically Available：基本可用

soft state

eventually  consistent：

核心思想：通过系统放松对某一时刻的一致性要求，来换取系统的整体伸缩性和性能上的改观。

###分布式与集群

分布式：不同服务器上部署不同的服务模块，他们之间通过rpc、rmi之间通信和调用，对外提供服务和组内协作。

集群：不同服务器上部署相同的模块，通过分布式软件进行统一的调度。



# Redis

是什么？

**REmote DIctionary Server,远程字典服务器**，C语言编写，BSD协议，

特点：*3

## 配置文件

### GENERAL

- dadmonize
- pidfile
- port
- tcp-blocking
- timeout
- bind
- tcp-keepalive
- loglevel
- logfile
- syslog-enabled
- syslog-ident
- syslog-facility
- databases

### SNAPSHOTTING

- save

  ```
  RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件，
  默认
  是1分钟内改了1万次，
  或5分钟内改了10次，
  或15分钟内改了1次。
  ```

- stop-write-on-bgsave-error

- rdbcompression

  使用LZF算法压缩。

- rdbchecksum

- dbfilename

- dir

### SECURITY

`config get requirepass`  获取安全口令

`config set requirepass xxx` 设置口令

`auth xxx ` 需要该指令以使用系统。

### LIMITS

- maxclients
- maxmemory
- maxmemory-policy
  	1. volatile-lru
   	2. allkeys-lru
   	3. volatile-random
   	4. allkeys-random
   	5. volatile-ttl
   	6. noeviction
- maxmemory-samples

## 安装redis

1. 下载

2. 解压

   tar xzf redis-xxx

3. make 安装

   默认安装路径：/usr/loacl/bin

4. make install

   检查安装

5. 拷贝redis.config到/redis/

6. 到/usr/loacl/bin 启动redis-server，使用自定义的配置文件/myredis/redis.config

### 基础知识

1. 单进程
2. flushdb 清空当前库
3. flushall 清空所有库
4. 端口号6379
5. 索引从0开始
6. 默认16个库，统一的密码管理，使用select bdid 切换库
7. dbsize 查看当前数据库的key数量。

### 负索引

![](D:\ProgramData\md\python负索引.png)

redis中支持负索引，这里借用python的图。
每个位置都有两个索引，如a，它的索引为0 或 -6； 如f 它是最后一个元素，负索引从-1开始。

所以，[1,4] \ [1,-2] \ [-5,-2] \ [-5,4] 都是一个意思。就是截取出bcde 

# 五大数据类型

## String

- 二进制安全，可以存储图片或序列化对象
- value理论支持最大512M

## *Hash

类似于java中的Map

```
hset key field val/ hmset key [field val ..]
hsetnx key field val
hincrby/ hincrbyfloat

hget / hmget/ hgetall
hkeys/hvals

hdel key [field..]

hlen
hexists key field

```



## List

底层实际是个**链表**，可以从头尾添加删除

```
lpush/rpush
llen/lindex/lrange
lrem key count value 删除n个value
ltrim key offset end 
rpoplpush [source] [des] 弹出一个到des的顶
lset key index value
linsert key before/after pivot_val1 val2 找到第一个匹配的pivot，然后插入 

```

性能总结

它是一个字符串链表，left、right都可以插入添加；
如果键不存在，创建新的链表；
如果键已存在，新增内容；
如果值全移除，对应的键也就消失了。

链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。

## Set

String型的无序集合，用hashtable实现

```
sadd key [value..] ：添加元素，可多个
srem key [value..] 

smembers key/ 获取所有元素
srandmember key count / 随机获取count个元素
spop key 随机弹出元素

sismember/
scard: 获取集合里元素的个数

smove key1 key2 val 将key1中的val 移动到 key2中

sdiff/
sinter/
sunion
```



## Zset

Sorted Set

每个元素都会关联一个double类型的分数

通过分数为成员排序。

```
zadd key [val score ].. 新建、添加、修改

zrange key startindex endindex从小到大排名
zrangebyscore key min max
zrevrange 从大到小
zrevrangebyscore

zcard key 总数
zcount key score score2 分数区间的总数 

zrank key val 获得下标值

zrem key val..

```

#持久化

## RDB（redis database）

dump.rdb redis备份的文件。

###是什么

在指定的时间间隔内将内存中的数据集快照写入到硬盘中，也就是snapshot，恢复的过程就是将快照文件直接读到内存中。

redis会单独fork一个进程来进行持久化，这个进程将数据写到临时文件，完成后再替换上次的持久化文件。
在这个过程中，主进程是不允许进行任何IO操作的，这就保证了极高的性能。

若需要进行大规模的数据恢复，且对于数据的完整性要求不敏感，那么RDB要比AOF高效。它的缺点就是最后一次持久化后的数据可能会丢失。

### Fork

fork的作用就是复制一个与当前进程一样的进程，包含变量、环境变量、pc计数器等都和原进程一样。

### 如何触发RDB快照？

- 配置文件中设置save
- save 或 bgsave
- 执行flushall也会产生dump.rdb，但是是空文件，无意义。

###如何恢复？

​	将备份文件拷贝到redis安装目录
​	config get dir	获取目录

### 优势和劣势

![1565359637227](D:\ProgramData\md\redis优劣势.png)

### 如何停止？

动态停止所有RDB保存规则的方法：`redis-cli config set save ""	`

##AOF（append only file)

将执行的写操作都记录下来。追加到aof文件中。

保存的文件名：appendonly.aof

如果开启了aof，那么redis会在启动的时候加载aof文件，根据日志内容将写指令都执行一遍，重新构建数据。

启动时发生异常，可以使用`redis-check-aof --fix`修复aof文件。

### rewrite

aof文件可能过大，使用重写机制，当文件大小超过阈值，redis就会启动aof文件的内容压缩，只保留可以恢复数据的最小指令集。

- 使用`bgrewriteaof`启动重写

- 原理：aof文件过大时，redis会fork一个新进程来进行aof重写，遍历新进程的内存中数据，每条记录都有一条set语句。重写时并非读取旧的aof文件，而是将内存中的数据库内容用命令的方式重写了一个新的aof文件，类似快照。

- 机制：redis会记录上次重写时的aof文件大小，默认配置是当aof文件大小是上次rewrite的一倍，且文件大于 64M 则重写。

### 优势

每秒同步：`appendfsync everysec`异步操作，每秒记录，若一秒内宕机，则可能有数据丢失。

每修改同步：appendfsync always 同步操作，每次数据发生变更就写aof，完整性好

不同步：`appendfsync no`

### 劣势

aof文件远大于rdb文件。

aof效率慢于rdb，每秒同步策略效率好，不同步效率和rdb相同。

# Redis事务

redis并非严格支持事务，如case4冤头债主。

###是什么

可以一次性执行多个命令，本质是redis指令的集合

###指令

使用：开启、入队、执行

- WATCH	：监视一个或多个Key，若在事务执行之前，这些key被其他命令改动，那么事务将被打断。类似于乐观锁

- UNWATCH：用于取消watch对所有key的监视

- MULTI: 开启事务

- EXEC ：执行事务快中的所有命令。

- DISCARD ：取消事务，放弃执行事务块中的命令。

  ![1565527545943](D:\ProgramData\md\redis_watch.png)

 ### 特点

- 一个队列中，一次性、顺序性、排他性的执行一系列命令。
- **单独的隔离操作**：事务中的所有命令都会被序列化、按顺序地执行，事务在执行的过程中不会被其他客户端送来的命令打断。
- **没有隔离级别的概念**：
  队列中的指令在事务没有提交前不会被实际执行。
  也就不存在 “事务内的查询要看到事务里的更新，在事务外的查询不能看到” 这个问题。

- **不保证原子性**：
  如果有一条命令执行失败，其后的命令仍然被执行，没有回滚。
  也可能出现在入队时就抛出异常，这时事务会被放弃，都不执行。

![1565525808849](D:\ProgramData\md\redis事务.png)

# redis主从复制

一主二从、薪火相传、反客为主

###配置方式

- 配从不配主
- 先开启主数据库，然后开启从库，在从库中使用`slaveof 主库ip 主库端口`
- 从库若与主库断开，都需要重新连接，除非配置写进redis.conf文件
- 使用`info replication`查看当前库的主从信息。
- 反客为主：`slaveof no one`让当前库脱离原来的m，自己成为m

### 哨兵模式

使用哨兵，设置监控。

被监控的库会成为主库，主库发生故障时，哨兵会让slave进行投票，选出新的master，并进行新的监控

设置方式：

1. 新建一个sentinel.conf文件

2. 在文件中写入要监控的master：

   ```
   sentinel monitor  aliasname host port 1
   如
   sentinel monitor xxxx 127.0.0.1 6379 1
   ```

3. 启动sentinel 

   `redis-sentinel sentinel.conf`

# Redis命令

```
redis-server /myredis/redis.conf
redis-cli -p 6379
```


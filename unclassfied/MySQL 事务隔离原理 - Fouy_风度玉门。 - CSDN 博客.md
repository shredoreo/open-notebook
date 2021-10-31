> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://blog.csdn.net/fouy_yun/article/details/87889520

### 文章目录

*   [一致性视图工作原理](#_7)
*   [更新操作](#_33)
*   [可重复读和读已提交的区别](#_43)

首先来介绍一下 MySQL 里面的 “视图” 的概念。

*   视图：查询语句定义的虚拟表，可以通过 create view … 来创建。
  
*   一致性视图：InnoDB 实现的，在 MVCC 中用到的，用于支持 RC (Read Commited，`读提交`) 和 RR （Repeatable Read，`可重复读`）隔离级别的实现。
  

一致性视图工作原理
=========

通过之前的文章我们知道，在可重复读隔离级别下，事务开始前会创建一个一致性视图。下面我们来详细说明一下这个一致性视图的工作原理。

在 InnoDB 引擎中，每个事务都有一个唯一的 ID，就是 transaction id。它是在事务开始的时候向系统申请的，是严格按顺序**递增**的。我们知道，每个数据行都是有多个版本的。每一次的事务更新都会有一个新的版本，并且每个版本都有对应的 transaction id（row trx_id）。  
![](https://img-blog.csdnimg.cn/20190223104309849.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eGlhbjkw,size_16,color_FFFFFF,t_70)  
上图是一条行数据的多个版本，最新的版本是 V4。但是需要注意的是，上图中的 U1、U2、U3 对应的就是 **undo log (回滚日志)**，小版本 trx_id 的值都是通过 undo log 计算出来的。

按照 可重复读的语义，每个事务启动的时候只能看到已经提交的事务，并且在本事务执行的过程中，不可以读取到其他事务的更新操作。在 InnoDB 中，为每个事务构造了一个 **`当前事务ID数组`** 的快照，就是记录事务开启时，当前正在执行的事务 ID 的集合。数组里面 trx_id 最小的记为 低水位，trx_id 最大的 + 1 记为高水位。如下图所示：  
![](https://img-blog.csdnimg.cn/20190223104340279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eGlhbjkw,size_16,color_FFFFFF,t_70)  
对于一个新事务而言，所读取到的记录版本的 trx_id 可能有以下几种情况：

*   在绿色区域：说明数据版本在事务开始前已提交，当前版本是`可见`的。
  
*   在红色区域：说明数据版本在事务开始后变更的，当前版本是`不可见`的。
  
*   在橙色区域：包含 2 种情况。
  
    *   如果 数据版本的 trx_id 在数组中，说明是正在执行的事务，不可见。
    *   如果 数据版本的 trx_id 不在数组中，说明是已经提交的事务，可见。

对于 MVCC 的多版本图，如果当前有一个事务，它的低水位是 18 。此时它访问这个数据行时，会通过 V4 版本计算出 V3 的版本。由此我们可以看出，InnoDB 利用了 数据多版本的特点，实现了快速创建快照的能力。

我们下面看一个 Demo：1、事务 A 开始前，系统中只有一个 99 的已提交事务；2、事务 A、B、C 的版本号分别是 100、101、102，且当前系统中只有这 4 个事务；3、事务开始前，(1, 1) 这一行数据的 trx_id 是 90.  
![](https://img-blog.csdnimg.cn/20190223104525177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eGlhbjkw,size_16,color_FFFFFF,t_70)  
上图从 事务 A 的可重复读角度看来，101、102 版本都不可见，因此找到了 90 这个版本的数据。

更新操作
====

前面一段说了，数据行多版本的读取规则。下面说明一下，更新数据的读取规则。如下所示：  
![](https://img-blog.csdnimg.cn/20190223104553614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eGlhbjkw,size_16,color_FFFFFF,t_70)  
上面 事务 B 执行的过程中，有事务 C 的提交流程。此时事务 B 就不能按照 undo log 回滚到 90 这个版本了（如果是这样事务 B 执行完成数据就变成 (1, 2) 了，与正确的结果 (1, 3) 不符）。此时更新的读取为 “`当前读`”，也就是读取到最新的数据，事务 B 执行完 k=k+1 后，再次获取 k 的值时，返回的就是 (1, 3) 了。

上面的过程，我们看到的是 事务 C 在事务 B 执行更新之前就已经提交了。如果事务 C 没有提交，那又会是一个什么流程呢？  
![](https://img-blog.csdnimg.cn/2019022310463414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1eGlhbjkw,size_16,color_FFFFFF,t_70)  
从上图可以看出，如果 事务 C’ 没有提交，那么事务 B 的更新就会等待，知道事务 C’ 执行完成。

可重复读和读已提交的区别
============

在可重复读隔离级别下，需要在事务开启前创建一个一致性视图。而读已提交，则是每执行一个语句前都会创建一个新的视图。

* * *

参考：《极客时间：MySQL 实战》、《高性能 MySQL》  
链接：[http://moguhu.com/article/detail?articleId=119](http://moguhu.com/article/detail?articleId=119)
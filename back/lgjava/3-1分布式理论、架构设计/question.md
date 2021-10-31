# 分布式理论

## 3.7 Lease机制

@@??那么主节点宕机期间，其他节点没法同步了吗？

获得有效期的lease的 主节点Node1 在宕机或断线期间，其他节点Node2向中心节点申请lease时无法申请，因为认定了Node1的租期未失效。这段期间内，其他节点是怎么同步状态的呢？

![image-20210822011242899](../../../../../Library/Application Support/typora-user-images/image-20210822011242899.png)



## 6.5.2Sentinel

熔断手段跟 hytrix的区别

Sentinel 熔断手段: @@??

- 通过并发线程数进行限制 @@??
- 通过响应时间对资源进行降级
- 系统负载保护


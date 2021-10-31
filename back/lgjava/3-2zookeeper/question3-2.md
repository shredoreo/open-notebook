基本概念

除Leader外，其他机器包括Follower和 Observer,Follower和Observer都能提供读服务，唯一的区别在于Observer不参与Leader选举过程， 不参与写操作的过半写成功策略，因此Observer可以在不影响写性能的情况下提升集群的性能。@@??

Observer如何提升集群 的性能？


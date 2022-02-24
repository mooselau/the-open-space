# Architecture Guide 架构设计
主要说明一些架构设计中的问题和解决方案。

## Distributed Hashing & Consistent Hashing 分布式哈希和一致性哈希

Hashtable/Hashmap 放在内存中就可以当作 Memcache 来使用，但是如果需要更多的内存空间和性能，就必须要考虑从一台节点增长为多台节点，同时让不同的几点负责Hashtable/HashMap的不同部分，于是就形成了分布式哈希 __Distributed Hashtable/Hashmap__。

>"Arbitrarily large hashtable could be split into several parts, hosted by different servers, to BYPASS memory limitation."

分布式哈希表的好处自然是，降低单个节点的压力，让多个节点平均分担压力，同时提高整体服务性能。最典型的例子就是 Memcache, Redis.

__Issues__

分布式哈希存在问题，最典型的问题就是增删节点要怎么处理？

增删节点会导致hash的结果不可靠，最直接的答案就是需要重新计算哈希，也就是 Rehashing，即如果有 server 节点挂了，需要通过 rehash 计算剩余节点的哈希继续提供服务，或者如果有 server 节点新增加进来，也需要通过 rehash 计算所有节点的哈希继续提供服务。

但是 Rehashing 耗时，同时 Rehashing 过程可能会导致服务异常，如果处理？

目前的解决办法就是：一致性哈希(Consistent Hashing)，具体来说就是 __哈希环__。

__Consistent Hashing__

![Hash Ring](../assets/images/hashring.png)

> "Hash Ring, allows servers to scale without affecting the overall system. It has rehashing, but only affects part of the servers, other servers/keys are staying the same."

所以，通过哈希环，增删节点只会影响到一部分的key/servers。

Q: 如何保证 A,B,C 或者 请求 能够均匀分布呢？
A: 可以使用 labels (虚拟节点) 来帮助平衡性能分布，label的节点数量可以根据server 性能来决定。（可以使用 weidht 权重 来表示性能，进行计算，比如A机器算作权重 10，可以配置十个label A0 - A10，B机器算作 5权重 配置5个label，这样以此类推，最后进行计算分配。）

Q: 如何应对不均匀？
A: 这时只能依赖 hashing function了。

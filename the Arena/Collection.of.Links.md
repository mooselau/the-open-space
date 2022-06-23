# Collection of Links

用来记录一些非常不错的技术文章连接，在需要的时候可以用来回顾。

## JDK

[JDK 8 Stream 数据流效率怎么样？](https://mp.weixin.qq.com/s/l9rhtf_klcjQBroy8ULcDQ?forceh5=1)
>Stream 是Java SE 8类库中新增的关键抽象，它被定义于 java.util.stream （这个包里有若干流类型：Stream<T> 代表对象引用流，此外还有一系列特化流，如 IntStream，LongStream，DoubleStream等。
>Java 8 引入的的Stream主要用于取代部分Collection的操作，每个流代表一个值序列，流提供一系列常用的聚集操作，可以便捷的在它上面进行各种运算。集合类库也提供了便捷的方式使我们可以以操作流的方式使用集合、数组以及其它数据结构；

[各大框架都在使用的Unsafe类，到底有多神奇？](https://mp.weixin.qq.com/s/cBte2hchplRVvkxyXYb29w?forceh5=1)
>几乎每个使用 Java开发的工具、软件基础设施、高性能开发库都在底层使用了sun.misc.Unsafe，比如Netty、Cassandra、Hadoop、Kafka等。

### Java Collections

[一文澄清网上对 ConcurrentHashMap 的一个流传甚广的误解!](https://mp.weixin.qq.com/s/UmR5-NUvTrniNSWsC1mt8w?forceh5=1)
>上周我在极客时间某个课程看到某个讲师在讨论 ConcurrentHashMap（以下简称 CHM）是强一致性还是弱一致性时，提到这么一段话。

## Database 数据库

[数据库主键一定要自增吗？有哪些场景不建议自增？](https://mp.weixin.qq.com/s/Xu_SfM-DzKarybGpspwIHA?forceh5=1)
>出于习惯，我们一般会加一列id作为主键，而这个主键一般边上都有个AUTO_INCREMENT, 意思是这个主键是自增的。自增就是i++，也就是每次都加1。
>但问题来了。主键id不自增行不行？为什么要用自增id做主键？离谱点，没有主键可以吗？什么情况下不应该自增？

## WebServer

[Netty 实现百万连接服务的难点和优化点](https://mp.weixin.qq.com/s/MacyYS3rbTQHlO7KA77OQA?forceh5=1)
>还记得一年半前，做的一个项目需要用到 Android 推送服务。和 iOS 不同，Android 生态中没有统一的推送服务。Google 虽然有 Google Cloud Messaging ，但是连国外都没统一，更别说国内了，直接被墙。

## Framework 框架

[使用 Spring Cache 实现缓存，这种方式才叫优雅！](https://mp.weixin.qq.com/s/20KeeJ6u00_lu9TZVpJMtw?forceh5=1)
>最近负责教育类产品的架构工作，两位研发同学建议：“团队封装的Redis客户端可否适配Spring Cache，这样加缓存就会方便多了” 。

## Middlewares 中间件

### Gateway 网关

[API 网关 Apache APISIX 助力雪球双活架构演进
](https://mp.weixin.qq.com/s/O7HH03unTwo_JbOtmz8m2A?forceh5=1)
>雪球的愿景是做「中国人首选的在线财富管理平台」，为投资者提供优质内容、实时行情、交易工具、财富管理等多种服务。其中实时行情服务对接了多种上游数据源，通过数据流式计算、存储、分发，为投资者提供稳定的数据服务。实时行情一直是雪球业务系统中的资源消耗大户，持续在高水位运行。雪球内部一项重要的工作就是持续进行稳定性建设，其中包括对行情服务进行性能优化。

### Redis

[深度解析单线程的 Redis 如何做到每秒数万 QPS 的超高处理能力！](https://mp.weixin.qq.com/s/4bSy5cld0oZg8gqNGuemZA?forceh5=1)
>有一次去面试服务器端岗位，面试官问我有一个连接过来，你该怎么编程处理它。我答道：“主线程收到请求后，创建一个子线程处理。” 面试官接着问，那如果有一千个连接同时来呢？我说“那就多创建一点线程，搞个线程池”。面试官继续追问如果一万个呢？我答道:“......不会...”。

## PaaS 平台能力

### 服务通讯

[有了HTTP，为什么还要RPC？](https://mp.weixin.qq.com/s/quMRZIwJqQyG8G7X7hdyBA?forceh5=1)
>RPC主要是基于TCP/IP协议的，而HTTP服务主要是基于HTTP协议的，我们都知道HTTP协议是在传输层协议TCP之上的，所以效率来看的话，RPC当然是要更胜一筹啦！下面来具体说一说RPC服务和HTTP服务。

## Design 设计思路

[优雅地处理重复请求（并发请求）](https://mp.weixin.qq.com/s/xhaZsLY5XTE5Sm9Z_4eoYw?forceh5=1)
>对于一些用户请求，在某些情况下是可能重复发送的，如果是查询类操作并无大碍，但其中有些是涉及写入操作的，一旦重复了，可能会导致很严重的后果，例如交易的接口如果重复请求可能会重复下单。

[后端思维篇：手把手教你写一个并行调用模板](https://mp.weixin.qq.com/s/-E0-srX7L93BMKNN638eXQ?forceh5=1)
>如果让你设计一个 APP 首页查询的接口，它需要查用户信息、需要查 banner 信息、需要查标签信息等等。一般情况，小伙伴会实现如下。

[架构与思维：设计容量，到底有多重要 ？](https://mp.weixin.qq.com/s/if-fhwu8fgvWZKyi-doDbA?forceh5=1)
>单位每年都会举行运动会，有一个 2000 米长跑的项目。大约每年报名人员为男选手 40 人，女选手 20 人，只有一条橡胶跑道。一次比赛 10 人一起跑，所以至少需要 6 场比赛。2000 米的完成时间要求是 20 分钟，超过 20 分钟不计数，所以比赛耗时我们计算为 20 分钟。加上比赛前的动员组织，比赛后的清场，我们假定每场比赛耗时 30 分钟。现在我们预估下耗时。

[我做系统架构的一些原则](https://coolshell.cn/articles/21672.html)
>工作 20 多年了，这 20 来年看到了很多公司系统架构，也看到了很多问题，在跟这些公司进行交流和讨论的时候，包括进行实施和方案比较的时候，都有很多各种方案的比较和妥协，因为相关的经历越来越多，所以，逐渐形成了自己的逻辑和方法论。

## Solution 解决方案

### SSO 单点登录

[Oauth2.0实现单点登录的原理流程，这次总该懂了！](https://mp.weixin.qq.com/s/6dqv9jk-725KE3D5mO47Bg?forceh5=1)
>单点登录是多域名企业站点流行的登录方式。本文以现实生活场景辅助理解，力争彻底理清 OAuth2.0 实现单点登录的原理流程。同时总结了权限控制的实现方案，及其在微服务架构中的应用。

## Interview 面经

[坐标深圳｜虾皮 Shopee｜后端开发工程师面经](https://mp.weixin.qq.com/s/pC3Z5zQAu3L48iM9yHiFEQ?forceh5=1)

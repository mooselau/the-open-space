# Collection of Links

用来记录一些非常不错的技术文章连接，在需要的时候可以用来回顾。

## JDK

[JDK 8 Stream 数据流效率怎么样？](https://mp.weixin.qq.com/s/l9rhtf_klcjQBroy8ULcDQ?forceh5=1)
>Stream 是Java SE 8类库中新增的关键抽象，它被定义于 java.util.stream （这个包里有若干流类型：Stream<T> 代表对象引用流，此外还有一系列特化流，如 IntStream，LongStream，DoubleStream等。
>Java 8 引入的的Stream主要用于取代部分Collection的操作，每个流代表一个值序列，流提供一系列常用的聚集操作，可以便捷的在它上面进行各种运算。集合类库也提供了便捷的方式使我们可以以操作流的方式使用集合、数组以及其它数据结构；

[玩转 Java8 中的 Stream 之从零认识 Stream](https://mp.weixin.qq.com/s/vxZthSjG3zNYNFd2oDZK3g)
>相信Java8的Stream 大家都已听说过了，但是可能大家不会用或者用的不熟，文章将带大家从零开始使用，循序渐进，带你走向Stream的巅峰。

[各大框架都在使用的Unsafe类，到底有多神奇？](https://mp.weixin.qq.com/s/cBte2hchplRVvkxyXYb29w?forceh5=1)
>几乎每个使用 Java开发的工具、软件基础设施、高性能开发库都在底层使用了sun.misc.Unsafe，比如Netty、Cassandra、Hadoop、Kafka等。

[妙用Java8中的“Function”消灭if...else！](https://mp.weixin.qq.com/s/jnM0WIOzPrFeHl2VilDa2g)
>在开发过程中经常会使用if...else...进行判断抛出异常、分支处理等操作。这些if...else...充斥在代码中严重影响了代码代码的美观，这时我们可以利用Java 8的Function接口来消灭if...else...。

[面试官：private修饰的方法可以通过反射访问，那么private的意义是什么？](https://mp.weixin.qq.com/s/U_4KvDcVY4RizX1owFgzTA)
>在一个类中，为了不让外界访问到某些属性和方法，通常将其设置为private，用正常的方式（对象名.属性名，对象名.方法名）将无法访问此属性与方法，但有没有其他方法可以访问呢？答案是有的，这就是java反射带来的便利。

[这才是Thread Local的正确原理与适用场景](https://mp.weixin.qq.com/s/SebbO7p2uJHUKQO0Fc6uX)
>由于 ThreadLocal 支持范型，如 ThreadLocal< StringBuilder >，为表述方便，后文用 变量 代表 ThreadLocal 本身，而用 实例 代表具体类型（如 StringBuidler ）的实例。

[史上最强Java NIO入门：担心从入门到放弃的，请读这篇！](https://mp.weixin.qq.com/s/UkGPHjJdgcs1oc-XSrImzQ)
>本文原题“《NIO 入门》，作者为“Gregory M. Travis”，他是《JDK 1.4 Tutorial》等书籍的作者。

### Java Collections

[一文澄清网上对 ConcurrentHashMap 的一个流传甚广的误解!](https://mp.weixin.qq.com/s/UmR5-NUvTrniNSWsC1mt8w?forceh5=1)
>上周我在极客时间某个课程看到某个讲师在讨论 ConcurrentHashMap（以下简称 CHM）是强一致性还是弱一致性时，提到这么一段话。

[如何手写一个 AQS？](https://mp.weixin.qq.com/s/oHx2n0_EWUPt8m-7weRX0Q)
>AQS 即 AbstractQueuedSynchronizer，是用来实现锁和线程同步的一个工具类。大部分操作基于 CAS 和 FIFO 队列来实现。

[周末自己动手撸一个 HashMap，美滋滋](https://mp.weixin.qq.com/s/OAxCZJiicbD-fp9tTHQ1BQ)
>HashMap是Java中常用的集合，而且HashMap的一些思想，对于我们平时解决业务上的一些问题，在思路上有帮助，基于此，本篇博客将分析HashMap底层设计思想，并手写一个迷你版的HashMap！

## Database 数据库

[万字总结：学习MySQL优化原理，这一篇就够了！](https://mp.weixin.qq.com/s/uPbMzhqfRbsseExZxJAQJg)
>说起MySQL的查询优化，相信大家收藏了一堆奇技淫巧：不能使用SELECT \*、不使用NULL字段、合理创建索引、为字段选择合适的数据类型... 你是否真的理解这些优化技巧？是否理解其背后的工作原理？在实际场景下性能真有提升吗？


[数据库主键一定要自增吗？有哪些场景不建议自增？](https://mp.weixin.qq.com/s/Xu_SfM-DzKarybGpspwIHA?forceh5=1)
>出于习惯，我们一般会加一列id作为主键，而这个主键一般边上都有个AUTO_INCREMENT, 意思是这个主键是自增的。自增就是i++，也就是每次都加1。
>但问题来了。主键id不自增行不行？为什么要用自增id做主键？离谱点，没有主键可以吗？什么情况下不应该自增？

[6亿数据秒级查询，ClickHouse太快了！](https://mp.weixin.qq.com/s/CI0Y7YQtSsLdRJzOoSq74A)
>clickhouse 在数据分析技术领域早已声名远扬，如果还不知道可以 点这里 了解下。

[InnoDB原理篇：为什么使用索引会变快?](https://mp.weixin.qq.com/s/wUrsOvRwofnawNOOWLw2UQ)
>假设给你一本非常厚的《Java编程思想》阅读，没有目录，你想快速找到某一个章节的知识点，那估计得找一会了，如果有目录就不一样。

[MySQL + JSON = 王炸！！](https://mp.weixin.qq.com/s/0Vme6stfIYg-E43GM5UnuQ)
>关系型的结构化存储存在一定的弊端，因为它需要预先定义好所有的列以及列对应的类型。但是业务在发展过程中，或许需要扩展单个列的描述功能，这时，如果能用好JSON数据类型，那就能打通关系型和非关系型数据的存储之间的界限，为业务提供更好的架构选择。

## WebServer

[Netty 实现百万连接服务的难点和优化点](https://mp.weixin.qq.com/s/MacyYS3rbTQHlO7KA77OQA?forceh5=1)
>还记得一年半前，做的一个项目需要用到 Android 推送服务。和 iOS 不同，Android 生态中没有统一的推送服务。Google 虽然有 Google Cloud Messaging ，但是连国外都没统一，更别说国内了，直接被墙。

## Framework 框架

[Spring 中经典的 9 种设计模式，打死也要记住啊！](https://mp.weixin.qq.com/s/-YkOeOk6t2fy7EruDdxigQ)
>1.简单工厂(非23种设计模式中的一种)..

[使用 Spring Cache 实现缓存，这种方式才叫优雅！](https://mp.weixin.qq.com/s/20KeeJ6u00_lu9TZVpJMtw?forceh5=1)
>最近负责教育类产品的架构工作，两位研发同学建议：“团队封装的Redis客户端可否适配Spring Cache，这样加缓存就会方便多了” 。

[非常哇塞的 SpringBoot性能优化长文！](https://mp.weixin.qq.com/s/W5_smkDtDfJA9HDlSi2KSw)
>SpringBoot已经成为Java届的No.1框架，每天都在蹂躏着数百万的程序员们。当服务的压力上升，对SpringBoot服务的优化就会被提上议程。

## Middlewares 中间件

### Gateway 网关

[API 网关 Apache APISIX 助力雪球双活架构演进](https://mp.weixin.qq.com/s/O7HH03unTwo_JbOtmz8m2A?forceh5=1)
>雪球的愿景是做「中国人首选的在线财富管理平台」，为投资者提供优质内容、实时行情、交易工具、财富管理等多种服务。其中实时行情服务对接了多种上游数据源，通过数据流式计算、存储、分发，为投资者提供稳定的数据服务。实时行情一直是雪球业务系统中的资源消耗大户，持续在高水位运行。雪球内部一项重要的工作就是持续进行稳定性建设，其中包括对行情服务进行性能优化。

### Redis

[深度解析单线程的 Redis 如何做到每秒数万 QPS 的超高处理能力！](https://mp.weixin.qq.com/s/4bSy5cld0oZg8gqNGuemZA?forceh5=1)
>有一次去面试服务器端岗位，面试官问我有一个连接过来，你该怎么编程处理它。我答道：“主线程收到请求后，创建一个子线程处理。” 面试官接着问，那如果有一千个连接同时来呢？我说“那就多创建一点线程，搞个线程池”。面试官继续追问如果一万个呢？我答道:“......不会...”。

[快看我在Redis分布式锁上，栽的8个跟头！](https://mp.weixin.qq.com/s/YmzOPFdASHMasbPdQuWbUg)
>在分布式系统中，由于redis分布式锁相对于更简单和高效，成为了分布式锁的首先，被我们用到了很多实际业务场景当中。

[Redisson 是如何实现分布式锁的？](https://mp.weixin.qq.com/s/ZNz5TKpRA-5pa9bxyhRsYQ)
>针对项目中使用的分布式锁进行简单的示例配置以及源码解析，并列举源码中使用到的一些基础知识点，但是没有对redisson中使用到的netty知识进行解析。

[因 Redis 分布式锁造成的 P0 级重大事故，整个项目组被扣了绩效。。。](https://mp.weixin.qq.com/s/GaQ_jemfkUrF-PiPfpimtw)
>基于Redis使用分布式锁在当今已经不是什么新鲜事了。本篇文章主要是基于我们实际项目中因为redis分布式锁造成的事故分析及解决方案。

[Redis 由浅入深深深深深剖析](https://mp.weixin.qq.com/s/5wAp844dUrV5djCcJzZipg)
>常用的SQL数据库的数据都是存在磁盘中的，虽然在数据库底层也做了对应的缓存来减少数据库的IO压力，但由于数据库的缓存一般是针对查询的内容，而且粒度也比较小，一般只有表中的数据没有发生变动的时候，数据库的缓存才会产生作用。

### Message Queue

[消息队列面试连环问：如何保证消息不丢失？处理重复消息？消息有序性？消息堆积处理？](https://mp.weixin.qq.com/s/Dr7m_9hsf4boSbuNmOAvYw)
>最近我一直扎在消息队列实现细节之中无法自拔，已经写了 3 篇Kafka源码分析，还剩很多没肝完。之前还存着RocketMQ源码分析还没整理。今儿暂时先跳出来盘一盘大方向上的消息队列有哪些核心注意点。

[面试基操：MQ怎么保障消息可靠性？](https://mp.weixin.qq.com/s/F6JsNbLfeo_63kCzcBgogA)
>面试官：在MQ的整个消息生产消费过程中，如何保障消息100%被消费？

[28 张图带你体系化全面认识 Kafka](https://mp.weixin.qq.com/s/4ggUHipWRaYKg-aJmtbeXQ)
>Kafka 是主流的消息流系统，其中的概念还是比较多的，下面通过图示的方式来梳理一下 Kafka 的核心概念，以便在我们的头脑中有一个清晰的认识。

[Kafka 消息丢失与消费精确一次性](https://mp.weixin.qq.com/s/6-bgLMg5ISfB6i2pcPKGBg)
>如果Kafka Producer使用“发后即忘”的方式发送消息，即调用producer.send(msg)方法来发送消息，方法会立即返回，但此时并不能说明消息已经发送成功。消息发送方式详见初次邂逅Kafka生产者。

## PaaS 平台能力

### Kubernetes

[新手必须知道的 Kubernetes 架构](https://mp.weixin.qq.com/s/FpfgN44xsG5je511Q_Pbcw)
>etcd 是一个快速、分布式、一致的键值存储，用作持久存储 Kubernetes 对象数据（如 pod、replication controllers, secrets, services 等）的后备存储。

[一套基础自动化部署搭建过程](https://mp.weixin.qq.com/s/pQewaClxNnXfJsTTxis6DQ)
>公司初创技术团队，没有任何基础设施的情况下，需要搭建一系列code管理以及自动化部署等工具....所以 引发了下面一系列的部署过程，历时两天，中间也是碰到各种问题，但最终把基本工具全部搭建成功，耶~，下面带大家一起看下此次搭建过程。

## Design 设计思路

[怎么写设计文档？](https://mp.weixin.qq.com/s/_3iBa4CCeS3k8MtxlUmJmg)
>一份好的设计文档需要提供清晰的问题描述、整体的概要设计、涵盖各个细节的详细设计等。

### 概念理解

[有了HTTP，为什么还要RPC？](https://mp.weixin.qq.com/s/quMRZIwJqQyG8G7X7hdyBA?forceh5=1)
>RPC主要是基于TCP/IP协议的，而HTTP服务主要是基于HTTP协议的，我们都知道HTTP协议是在传输层协议TCP之上的，所以效率来看的话，RPC当然是要更胜一筹啦！下面来具体说一说RPC服务和HTTP服务。

[为什么graphql可以替代restful？](https://mp.weixin.qq.com/s/EeEIuqQYyM7dAbj7gU6uLg)
>上图是笔者刚参加工作的时候使用的架构图，当时前后端并未分离，所有代码都刚在一个工程里，如果后端代码改了，前端代码得跟着改，每次改一点，都需要触动全身，非常痛苦。

[雪花算法的原理和 Java 实现](https://mp.weixin.qq.com/s/0kzisisJmEDDyEZiWebkVQ)
>SnowFlake 算法，是 Twitter 开源的分布式 id 生成算法。其核心思想就是：使用一个 64 bit 的 long 型的数字作为全局唯一 id。

[关于加解密、加签验签的那些事](https://mp.weixin.qq.com/s/L6T-J38TMDUS31_8RxG78A)
>面对MD5、SHA、DES、AES、RSA等等这些名词你是否有很多问号？这些名词都是什么？还有什么公钥加密、私钥解密、私钥加签、公钥验签。这些都什么鬼?

### 逻辑设计

[优雅地处理重复请求（并发请求）](https://mp.weixin.qq.com/s/xhaZsLY5XTE5Sm9Z_4eoYw?forceh5=1)
>对于一些用户请求，在某些情况下是可能重复发送的，如果是查询类操作并无大碍，但其中有些是涉及写入操作的，一旦重复了，可能会导致很严重的后果，例如交易的接口如果重复请求可能会重复下单。

[后端思维篇：手把手教你写一个并行调用模板](https://mp.weixin.qq.com/s/-E0-srX7L93BMKNN638eXQ?forceh5=1)
>如果让你设计一个 APP 首页查询的接口，它需要查用户信息、需要查 banner 信息、需要查标签信息等等。一般情况，小伙伴会实现如下。

[22条API设计的最佳实践](https://mp.weixin.qq.com/s/O_qKVwZUFvFpocHbzrh1Ig)
>曾经因为一个糟糕的API而感到沮丧吗？在这个微服务的世界里，后端API的一致性设计是必不可少的。

[OAuth2和JWT - 如何设计安全的API？](https://mp.weixin.qq.com/s/0qE-B9kUXZ-yUsvw70Vdvg)
>本文会详细描述两种通用的保证API安全性的方法：OAuth2和JSON Web Token (JWT)。

[拒绝接口裸奔！开放 HTTP API 接口签名验证！](https://mp.weixin.qq.com/s/VJX6h2Fl_a5N3iCWe777nA)

[四连问：API 接口应该如何设计？如何保证安全？如何签名？如何防重？](https://mp.weixin.qq.com/s/G9aRj8CABeN6A1rgKz62Jg)
>说明：在实际的业务中，难免会跟第三方系统进行数据的交互与传递，那么如何保证数据在传输过程中的安全呢（防窃取）？除了https的协议之外，能不能加上通用的一套算法以及规范来保证传输的安全性呢？

### 架构设计

[架构与思维：设计容量，到底有多重要 ？](https://mp.weixin.qq.com/s/if-fhwu8fgvWZKyi-doDbA?forceh5=1)
>单位每年都会举行运动会，有一个 2000 米长跑的项目。大约每年报名人员为男选手 40 人，女选手 20 人，只有一条橡胶跑道。一次比赛 10 人一起跑，所以至少需要 6 场比赛。2000 米的完成时间要求是 20 分钟，超过 20 分钟不计数，所以比赛耗时我们计算为 20 分钟。加上比赛前的动员组织，比赛后的清场，我们假定每场比赛耗时 30 分钟。现在我们预估下耗时。

[我做系统架构的一些原则](https://coolshell.cn/articles/21672.html)
>工作 20 多年了，这 20 来年看到了很多公司系统架构，也看到了很多问题，在跟这些公司进行交流和讨论的时候，包括进行实施和方案比较的时候，都有很多各种方案的比较和妥协，因为相关的经历越来越多，所以，逐渐形成了自己的逻辑和方法论。

[支付系统架构设计详解](https://mp.weixin.qq.com/s/yMkmybVi8zLLD32wrQIYbw)
>支付永远是一个公司的核心领域，因为这是一个有交易属性公司的命脉。那么，支付系统到底长什么样，又是怎么运行交互的呢?

[当前疫情下火爆的直播应用，你了解背后的技术架构吗？](https://mp.weixin.qq.com/s/UVTQWiE3AfPuUWk-E284RQ)
>这场疫情让线下零售降至冰点，但是却带火了直播应用。直播电商、直播教育等各类直播应用可谓赢得了历史性的机会，很多大众开始接受并认可这种新型应用的便利和价值，个人感觉随着5G的普及，『直播+垂直领域+精细化的私域流量』将会是互联网的一个大热点，迎来真正的红利期。

[都说春节车票难抢是因为系统烂？看完这篇文章后你就知道 12306 的架构有多牛了！](https://mp.weixin.qq.com/s/rmsSzICB5zf3AW7Qtj731g)
>每到节假日期间，一二线城市返乡、外出游玩的人们几乎都面临着一个问题：抢火车票！

## Solution 解决方案

### SSO 单点登录

[Oauth2.0实现单点登录的原理流程，这次总该懂了！](https://mp.weixin.qq.com/s/6dqv9jk-725KE3D5mO47Bg?forceh5=1)
>单点登录是多域名企业站点流行的登录方式。本文以现实生活场景辅助理解，力争彻底理清 OAuth2.0 实现单点登录的原理流程。同时总结了权限控制的实现方案，及其在微服务架构中的应用。

### Tracing 分布式链路追踪

[怎么理解分布式链路追踪技术？](https://mp.weixin.qq.com/s/t6JBon8wXLE64vcpWocFXg)
>在学习分布式链路追踪之前，我们需要先理解这项技术产生的背景，以及它能够帮我们解决哪些棘手问题。

### 异地多活

[搞懂异地多活，看这篇就够了](https://mp.weixin.qq.com/s/jRwVBZ8ENbY8Qw0a9v7K_A)
>在软件开发领域，「异地多活」是分布式系统架构设计的一座高峰，很多人经常听过它，但很少人理解其中的原理。异地多活到底是什么？为什么需要异地多活？它到底解决了什么问题？究竟是怎么解决的？

### 大文件上传

[大文件上传：秒传、断点续传、分片上传](https://mp.weixin.qq.com/s/MucuifpPC5bUkCMDj2wu1A)
>文件上传是一个老生常谈的话题了，在文件相对比较小的情况下，可以直接把文件转化为字节流上传到服务器，但在文件比较大的情况下，用普通的方式进行上传，这可不是一个好的办法，毕竟很少有人会忍受，当文件上传到一半中断后，继续上传却只能重头开始上传，这种让人不爽的体验。那有没有比较好的上传体验呢，答案有的，就是下边要介绍的几种上传方式。

### 接口超时/服务雪崩

[外部接口大量超时，把整个系统拖垮，引发雪崩！如何解决？](https://mp.weixin.qq.com/s/0GE_CZO_AfxHFM4kq6la0w)
>互联网+时代，业务数字化已经蔓延到你能想到的各个行业。各种业务功能、营销玩法越来越多，系统也越来越复杂。

### 一致性

[一口气说出 4 种分布式一致性 Session 实现方式，面试杠杠的~](https://mp.weixin.qq.com/s/eNU0WPWRHHOx5bmnZDjEhg)
>阿粉公司有一个Web管理系统，使用Tomcat进行部署。由于是后台管理系统，所有的网页都需要登录授权之后才能进行相应的操作。

## 其他

[从 0 到 1 开发一个 IntelliJ IDEA 插件](https://mp.weixin.qq.com/s/rMUHvhDJVKLbNpu087283Q)
>写 Java 代码的时候，经常会涉及到重复性的操作，这个时候就会想要是有这样一个插件就好了，如果是大家都会遇到的场景，IDE或许已经提供了，再不然也有可能有人编写了相关的插件。

## Interview 面经

[坐标深圳｜虾皮 Shopee｜后端开发工程师面经](https://mp.weixin.qq.com/s/pC3Z5zQAu3L48iM9yHiFEQ?forceh5=1)

[之前遇到一位老面试官，问我的问题真的有点东西](https://mp.weixin.qq.com/s/vaucBCskb8TWP7So_zI_aw)
>这篇文章其实源于一次我的面试经历。那次我面对是一位老面试官，真的很有东西。那次面试我和他叨叨了两小时....我滴妈我嘴巴都干了真的。他的提问都很有深度，可以说对我的学习之路有很大的帮助。我记得有个问题，差不多是面了一个小时的时候他问我：Cookie、Session、Token知道的吧？我说：知道。那你从演进的角度来讲讲 Cookie 、Session、Token？
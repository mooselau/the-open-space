# Middleware General 中间件

* Java Popular Modules
  - Zookeeper
  - Redis
  - Message Queue, see [link](https://blog.csdn.net/maihilton/article/details/80037824?from=singlemessage&isappinstalled=0)
  - Lucene & ElasticSearch

主要整理一些常用的中间件的资料。

使用消息队列的好处是：异步，解耦，削峰。异步处理，可以提高处理效率；解耦，可以帮助提高开发效率，使得项目独立，减轻依赖；削峰，可以在大流量的时候进行消息缓冲，减轻处理压力，保护服务器的处理资源；

## Redis 缓存

- Redis 在 Spring 中的依赖是 spring-boot-starter-data-redis;

- 常见的缓存清理算法：FIFO, LRU, LFU;
  + FIFO 先进先出队列；
  + LRU 最近使用算法，命中的元素的移动到队头，队尾最长时间没有命中的淘汰；
  + LFU 最近最常使用，命中的元素会频次加一，排序后影响顺序，队尾最不常使用的被淘汰；
  
- 缓存的一般是 \<Key, Value> 的形式，value 可以是 String, List, Set, ZSet(Sorted Set), 以及 Hash；
  + 在 RedisTemplate 中是 StringRedisTemplate, ListOperations, SetOperations, ZSetOperations, 以及 ValueOperations(OpsForValue.get/set);
  + List Operations 可以选择 LeftPush/LeftPop, RightPush/RightPop; 
  
- key 可以配置 keyserializer, value 可以配置 value serializer;
  + 可供选择的常用的序列化器有： StringSerializer, Jackson2JsonSerializer;

- setNX (set if not exists) 命令常用来判断是否存在，进而用来做分布式锁；
  + 在 RedisTemplate 中是 setIfAbsent();

- Redis 中默认没有 超时时间，即永不过期，所以需要手动设置超时时间；
  + 永不过期带来的潜在问题，就是内存泄漏，一旦不过期的key非常多，有可能导致缓存拒绝和缓存删除；
  
- set() 和 expire() 是两步操作（非原子操作），一旦 expire() 失败（比如网络故障，机器挂机等等），key 就会永不过期；
  + 为了将两步操作并成一步操作，可以考虑使用 Lua 脚本进行处理；
  + 同时 Spring 也提供了 setIfAbsent(key, value, timeout) 来设置缓存同时配置超时时间;

```java

public class RedisDemo {

  public void doOperations(Object key, Object value) {
    // set <key,value>
    redisTemplate.opsForValue().set(key, value);
    // set expire time !!important
    redisTemplate.expire(key, 2000, TimeUnit.MILLISECONDS);

    // delete cache
    redisTemplate.opsForValue.delete(key);

    // Spring also has set&expire in one API
    redisTemplate.opsForValue.setIfAbsent(key, value, long, TimeUnit);
  }

}
```

- Redis 可以部署成 集群和主从的形式;

## Rabbit MQ 消息队列

- RabbitMQ 在 Spring 中的依赖是 spring-boot-starter-amqp;

- RabbitMQ 常见的注解主要在消费者中：@RabbitListener, @RabbitHandler;
  + @RabbitListener 标记在类上；
  + @RabbitHandler 标记在方法，表示消息消费入口；

```text

Producer ---> (broker)Exchange ---> Queue ---> Consumer

```

```java
public class MQDemo {

  public void doOperations() {
    rabbitTemplate.convertAndSend(Exchange, RoutingKey, Message);
  }

}


```

- Rabbit MQ 的三大模式：Direct, Topic, Fanout;
  + Direct, 需要 exchange, routingkey 来唯一指定一个 queue;
  + Topic, 需要 exchange, routingkey（带匹配逻辑）来指定一个或者多个 queue；
  + Fanout, 只需要 exchange 就可以面向所有绑定的 queue 广播消息；

```yaml
spring:
  listener:
    simple:
      # 测试retry，对应RabbitProperties.ListenerRetry
      retry:
        enabled: true
        max-attempts: 3
        initial-interval: 3000
```
- SpringBoot 中，可以配置 retry 次数来本地对处理失败的消息进行retry，注意不走MQ；

```text
(broker)Exchange ---> Queue (---> Dead-letter Queue)
```
- 常见的完整的方案，会需要在配置 queue 的同时配置 DLQ (Dead-letter-queue);
  + 这样当消息被拒绝的时候会进入 DLQ 中；

- 注意 MQ 的事务和 Confirm 机制不能共存；

### 常见的 MQ 问题

__可靠性消费__

- 在 producer 端可以配置事务消息，可以确保消息发送成功；
- 在 MQ 端，可以配置持久化，确保消息都会落库；
- 在 Consumer 端，可以操作 ack/nack 来确保消息被正常消费；
- 同时配置好 DLQ，确保处理失败的消息会被收集起来；

__重复消费__

- 一般重复消费的原因：1. 生产者重复生产了消息；2. 消费者消息处理失败重新发送到了 MQ；
- 针对生产端重复发送相同的消息问题，意味着 __生产端没有做好 幂等或者排他上锁__，导致多个线程同时在做一件事情，最终导致生产重复的消息，需要 Debug；
- 针对消费者处理失败的问题，默认会抛出异常，意味着 除了返回给MQ basic.nack，同时 requeue = true，导致了 消息重新进入队尾，再次消费；
  + 通过配置 DLQ，可以讲失败的消息导入 DLQ，避免重复消费；
  + 通过配置 spring retry，可以消费端独自重试，失败后进入 DLQ 或者 抛弃；

__顺序消费__

- producer 将相关连的消息发送到相同的队列中，consumer可以从同样的队列中顺序获得消息消费；
- producer 可以对 message 某个固定 id 进行取余操作，routing key和 取余结果 一一对应，比如：QUEUE_USER_OO, QUEUE_USER_01 等等，这样不同的消息会通过 routing key 固定发送到特定的 queue中，从而保证消息生产是顺序的，同时进入 queue中是顺序的；
- consumer 在只有一个的情况下可以对消息进行顺序消费，或者引入 zookeeper 来保证有且仅有一个 consumer 会对同一个 queue 进行消费；(?)

__消息补偿__

- 在某些极端情况下，消息没有被正常消费，但是由于超过次数被丢弃了，这时候需要采取补偿措施来修复问题；
- 为了避免被丢弃，__需要配置 DLQ__，来确保消息不回丢失；
  + 在配置了 DLQ 的基础上，可以设置定时任务，__周期性地消费 DLQ 的消息，可以选择重复投递，也可以转人工干预__；
- 在没有配置 DLQ 的情况下，可以考虑对进入消息的业务记录 __设置新的状态属性__，这样一旦消费失败同时消息丢失，数据库记录不会变化，这样 __长时间没有被推进的业务记录可以被捞起__，然后 __周期性进行补偿投递或者人工处理__；

>更多细节，可以参考[芋艿源码 -- SpringBoot RabbitMQ 系列](https://www.iocoder.cn/Spring-Boot/RabbitMQ/)
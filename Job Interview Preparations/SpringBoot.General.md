# SpringBoot General

SpringBoot 本身可以考察的内容比较多，但由于它只是 Java 成熟的框架，另外很多大公司都会基于它进行包装，所以主要还是以了解和学习为主，不建议八股文。

## IoC 容器

__Bean Lifecycle__

__Bean Type__

常见的 Bean 的类型有：
- Prototype, IoC 中有且仅有一个类实例；
- Request, 每次请求都会产生一个不同的类实例；

## AOP 面向切面

- 简单来说，在Spring中获取到的 Bean 不是单纯的类实例，其实是一个代理类 Proxy Bean，目的之一是为了可以执行各种针对 Bean 的特殊操作，包括：
  + @Before, 
  + @After, 
  + @AfterReturning,
  + @AfterThrowing,
  + @Around

- 其中 @After 作为后置通知是 __一定会执行的__，但是考虑到一般针对正常返回或者异常抛错会有不同的行为逻辑，建议具体具体使用 @AfterReturning 和 @AfterThrowing 替代；
- @Around 作为环绕通知，需要手动调用目标类和方法(通过JointPoint处理)，在这里可以手动对调用前和调用后进行处理（通过 try-catch 处理异常），但是考虑到 @Around 太灵活，非常容易遗漏信息甚至出错，还是建议使用分开的处理方式；

>注意的是，在 AOP 调用情况下，必须要通过外部调用，而不可以使用自我调用，否则类中的 AOP 无效。

__Dynamic Proxy__

Spring在运行过程中会根据是否是 接口类 来判断使用哪一种动态代理类：
- JDK 代理，代理类必须实现至少一个接口；
- CGLIB 代理，代理类没有实现接口；

## 常见注解

### @Transactional()

用来开启数据库事务的注解，主要作用是利用代理去除了：建立数据库连接，开启事务，提交事务，关闭连接等代码。

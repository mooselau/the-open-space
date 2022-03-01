# Java Locking / Java 锁机制

Java 常见的锁的机制，也是面试的重点。

## Synchronized 同步锁

Synchronized 是 Java 内置的最基本也是最常用的锁。

>在 Java 中，所有 Object 对象主要都存放在堆中，而对象引用则存放在栈中，堆中的对象，又由对象头、实例数据和填充数据三部分组成，其中对象头包含：
>- Mark Word，对象运行时记录信息 (monitor 在其中)
>- Klass Pointer，数据指针

- 使用 Sync 关键字上锁之后，在访问同步块的过程中会存在 MonitorEnter, MonitorExit 过程，这就是进入同步块和离开同步块的过程；
- Monitor 有一个字段 __Owner__，用来存放当前拥有这个对象 Monitor 锁的线程ID，这样每个线程就可以判断是否是自身已经拥有了这把锁了；
  + 如果该线程已经拥有这把锁，就可以直接进入，故而 Sync 锁也是重入锁；
  
### 同步锁类型

- 偏向锁，即根据 Owner 位置来判断是否是自身，是则直接进入(利用CAS来判断)，这时候会开启偏向锁，否则意味着存在竞争，之后会进入轻量级锁；
- 轻量锁，即在线程获得锁的时候会通过CAS置位 Owner + 轻量锁，成功则进入，否则意味着依然存在竞争，这之后会进入重量级锁；
- 自旋锁，即在少数线程竞争的情况下，通过自旋可以在短时间自旋结束后立即获得锁，而不需要进行唤醒切换；
- 重量锁，在线程获得锁后，会开启Monitor Enter/Exit，这时候其他线程会进入 BLOCKED 状态，可能需要被唤醒，存在切换开销；
- 锁消除/粗化，是指 JIT 会判断该段代码是否存在线程竞争，如果不存在，则会忽视代码中的同步块而达到锁消除，提升运行效率，相反，如果竞争较大，则会突出/强化同步块，比如从操作的外部就开始准备好竞争锁；

更多细节实现，可以参考外部链接：[Syncrhonized 的底层实现](https://www.jianshu.com/p/c97227e592e1)


### Usage 使用方法

- Synchronized 使用在普通方法上，需要 __本对象的锁__，但如果使用在 static 静态方法 上的话，则需要本 __类对象的锁__，两种锁不相关；
- 经常和 Object.wait(), Object.notify(), Object.notifyAll() 连用；

## ReentrantLock 重入锁

- 经常和 Condition.await(), Condition.signal(), Condition.signalAll() 连用；

tryLock(time) 限时获取锁

lockInterruptbly() 随时可以中断并放弃当前锁

lock() vs lockInterruptbly()

### vs Synchronized 同步锁

- ReentrantLock 的出现一定程度上是为了弥补 Synchronized 同步锁的不足，是高级锁，是对 sync 内置锁的补充和扩展；
- 等待打断，possible to interrupt a thread waiting for a lock;
- 无限时等待，possible to attempt to acquire a lock & without being willing to wait for the lock forever;
- 代码风格，possible to not use block style; (不用写块状代码)

### ReentrantLock vs ReentrantReadWriteLock
??

## AQS

??
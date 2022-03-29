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

- 经常和 Condition 以及 Condition.await(), Condition.signal(), Condition.signalAll() 连用；

### Internal Logics 内部实现

最值得关注的是 lock(), unlock() 的实现；

__lock()__

ReentrantLock.lock() 其实调用 sync.lock()，这里有两个实现：FairSync.lock(), NonFairSync.lock();

```text
FairSync.lock() ---> AQS.acquire(1) --> FairSync.tryAcquire(1);
```
- 公平锁 FairSync.lock() 实则调用了 FairSync.tryAcquire();
  + 获取并检查当前状态位 state c，__如果 c 为 0__，则表示没有任何线程获得这把锁，由于是公平锁，在没有正在排队的前驱节点的情况下，它可以直接对 state c CAS 来获得这把锁，同时设置 占有线程为自己，最后返回 true:
    * >if noPredecessors && CAS(0, 1) && setExclusiveOwnerThread(current);
  + 否则，意味着当前这把锁有线程占据，进而判断 __是否当前线程就是占有线程(已经获取了这把锁)__，是的话则设置 state 加一并返回 true:
    * >currentThread is exclusiveOwnerThread
    * 由于 int state c 不应该超过最大 Integer，如果超过了这里会报错超过可重入最大限度("Maximum lock count exceeded")；
  + 否则 意味着 当前这把锁正在被其他线程占用，返回 false；
- 如果 tryAcquire() 失败，则会继续调用 addWaiter(node) 和 acquireQueued() 进行排队；
  + addWaiter(node) 就是将当前线程放入队尾(list tail);
    * >node.prev = tail; tail.next = node;
  + acquireQueued() 就是 loop and check，循环检查当前节点是否可以获取锁(tryAcquire(1))，如果成功则结束，否则 自我park/睡眠，等待 unlock() 过程的唤醒;

```text
NonfairSync.lock() ---> AQS.compareAnsSetState()
                or ---> AQS.acquire(1) ---> NonfairSync.tryAcquire(1)
```
- 非公平锁 NonfairSync.lock() 会立即 CAS 抢占 state 为0 时候的锁，如果成功则会设置 占有线程并结束；
- 否则执行 NonfairSync.tryAcquire(1)，过程和公平锁比较相似；
  + 获取当前状态位 state c, 如果 c 为 0，则表示没有任何线程获得这把锁，在非公平锁的情况下，它会直接通过 CAS 来获得这把锁，同时设置占有线程 为自身，最后返回 true:
    * >if CAS(0,1), then setExclusiveOwnerThread(current);
  + 否则，进行判断 是否是当前线程已经获取了这把锁，是的话 state c 加一 并返回true；
  + 否则，意味着其他线程占用了这把锁，同时返回 false；
- 如果 tryAcquire() 失败，则会调用 AQS.addWaiter() 和 AQS.acquireQueued() 进行排队；
  + 过程和 公平锁一样，但是需要注意的是，非公平锁的 tryAcquire() 是不用看前置节点的，可以直接去 CAS setState 抢锁；

>公平锁，非公平锁的主要 上锁 的过程基本是一样的，唯一需要注意的就是 非公平锁 是不用看前置节点可以直接抢锁的，但公平锁一定需要检查前置节点是否存在。

__unlock()__
```text
unlock() ---> Sync.release() ---> AQS.release() ---> ReentrantLock.tryRelease()
```
- unlock() 其实内部在调用 AQS.release()，进而调用 ReentrantLock.tryRelease();
  + 检查当前线程是不是已经获得了锁，如果当前线程在非法release 则会报错:
    * >if currentThread is exclusiveOwnerThread, then throw exception
  + 检查状态位 state c, 在减去了当前 release(1) 之后，是否等于0:
    * >if getState() - releases == 0, then set free and non exclusiveOwnerThread
    * 如果等于 0，意味着当前线程 已经不持有这把锁了，会把设置 boolean free，同时将占有线程设置成 null(表示 无线程占有)；
  + 设置新的状态位 state c，同时返回变量 free；
- 在 tryRelease() 成功之后，会通过 unparkSuccessor() 唤醒它之后的节点进行获取锁；

>注意 state c 是 volatile的，而且 AQS 中的前后头尾 Node 都是 volatile的；

__tryLock(time)__ 
限时获取锁，在循环 tryAcquire() 过程中(doAcquireNanos() 类似于 acquireQueued())，一边循环等待一边计时。

__lockInterruptbly()__ 
在循环 tryAcquire() 过程中(doAcquireInterruptibly() 类似于 acquireQueued())，每次都会检查是否被 interrupted，并随时可以中断并放弃获取锁。

>注意 lock() 是不会看 interrupted 标志的。

### vs Synchronized 同步锁

- ReentrantLock 的出现一定程度上是为了弥补 Synchronized 同步锁的不足，是高级锁，是对 sync 内置锁的补充和扩展；
- 等待打断，possible to interrupt a thread waiting for a lock;
- 无限时等待，possible to attempt to acquire a lock & without being willing to wait for the lock forever;
- 代码风格，possible to not use block style; (不用写块状代码)

__Lock & Wait__

- 注意 Object.wait() 和 Condition.await() 是有区别的；
- 一般来说，Object.wait() 和 Object.notify()/notifyAll() 连用，类似地，Consition.await() 和 Condition.signal()/signalAll() 连用；

- 用法上，Object.wait() 经常用在一个loop中：
```java
synchronized(mutex) {
    while(volatile flag) {
        mutex.wait();
    }
}
```

类似地，Condition.await() 经常用在 loop 和 try-catch block 中：
```java
lock.trylock();
try {
    while(volatile flag) {
        condition.await();
    }
} finally {
    lock.unlock();
}

```

__Wait() & sleep()__

Apparently, wait() belongs to Object classes, however, sleep() belongs to thread. 

Another difference is that wait() gives up its locks and resources while sleep() does not.

>wait() will let current thread give up its lock and release system resources like CPU to hang for a while until it is been notified.

### ReentrantLock vs ReentrantReadWriteLock
??

## AQS (AbstractQueuedSynchronizer)

- 是实现锁需要继承的抽象类，主要的参数 Node head, tail, state 都是 volatile 修饰；
  + 主要的内部类有：Node, ConditionObject;
- tryAcquire() 需要实现的抽象方法，获取锁的逻辑；
  + acquire() 是它的 public 入口；
- tryRelease() 需要实现的抽象方法，释放锁的逻辑；
  + release() 是他的 public 入口；
- addWaiter() 是用来将当前线程排队到当前等待的 list 中；
- unparkxxx() 是用来唤醒队列的节点的；
- acquireQueued() 是通过循环 tryAcquire() 来循环请求锁，类似的还有 doAcquireInterruptibly(), doAcquireNanos()；

>注意以上常用到的都是在 Exclusive mode 独占模式下的方法和逻辑，在 AQS 中还有一类 Shared mode 共享模式(?)。


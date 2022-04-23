# Java Threading / Java 多线程

* Multi-threading
  - Thread
    + Daemon Thread
    + Timer and TimerTask
    + ThreadLocal, see [link](https://www.cnblogs.com/jasongj/p/8079718.html)
  - ThreadPool
    + ThreadPoolSize, see [link](https://mp.weixin.qq.com/s/RcsGOUtWQKO7w2BZNb8NzQ)

## Thread 线程

__Thread Status 线程状态__
NEW, RUNNABLE/BLOCKED/WAITING/TIMED_WAITING, TERMINATED

__Thread Interrupt 线程中断__

- 线程中断主要有两种场景：主动中断 Active Interruption ，被动中断 Passive Interruption；
- 主动中断一般发生在调用 Thread.interrupt()，被动中断可以发生在线程销毁的时候；

>注意 _Thread.interrupted()_ 会检查当前中断标志 同时 会 __清除中断标志__，如果仅仅需要检查中断标记，可以考虑使用 _Thread.isInterrupted()_ 来判断。

### ThreadLocal

- 每个线程内部维护了私有的一个 ThreadLocalMap；
  + 每个 ThreadLocal 在 set(value) 的时候，实际上会新建 entry(this, value) 存放到这个私有的 map 中，其中使用该 ThreadLocal 对象为 key，value 即为传进来的值；
  + 所以除了 set()，也需要手动在合适的时候使用 remove() 清理掉不用的对象，以避免内存泄漏；
  + 但也不是必须要 remove()，因为每一个 Entry 其实是一个 WeakReference，在空间不够的时候一定会被清理掉；??
- 因为 ThreadLocal 以及 ThreadLocalMap 是线程私有的对象，一旦对象交给其他线程，则会丢失内容；

## ThreadPool 线程池

__Usage 使用注意__

> 顶层接口 Executor <- 二层接口 ExecutorService <- 三层抽象 AbstractExecutorService <- 四层实用类 ThreadPoolExecutor;
> Executors 是工厂类，用来生成 FixedThreadPool(), CachedThreadPool(), ScheduledThreadPool 等等;

- 顶层 Executor 只包含execute()，ExecutorService 开始引入 submit();
- execute(Runnable) 和 submit(Callable) 入参不一样，同时 submit() 还有任务返回值;
  + 使用 submit()，Callable 会包装成 FutuerRunnable，以方便可以作为 Runnable 执行 execute() 方法，另一方面 Future 对象可以用于返回结果；
  + 使用 submit() 会返回 Future 类，通过 Future.get() 可以拿到返回值，但是会阻塞线程;
  + 建议使用 Future.isDone() 来判断执行完成情况，之后再采用 get() 拿到返回值;

__Key Parameters__
CorePoolSize, MaximumPoolSize, KeepAliveTime, Unit, BlockingQueue;

__Thread Number__
- 理想情况下，线程数最好是等于CPU 核心数目，但现实情况下，由于CPU存在各种开销：IO 开销，网络延迟，甚至 线程切换开销，所以 __线程数一定是 大于 CPU 核心数目的__；
- 但是线程数过多，则会有很多线程进入等待运行状态；
- 理想的线程数量 =  CPU 核心数目 * ( 线程IO时间/线程CPU时间 + 1)
  + 当IO时间越长的时候，线程数目会越多；当IO时间越短，线程数目越少；
  + 对于IO密集型服务，线程数目需要很多，对于CPU 计算密集型服务，线程数不需要很多；

__Reject Execution Handler__
AbortPolicy(exception if fail), DiscardPolicy(Silently Drop), DiscardOldest, CallerRunsPolicy;

### Blocking Queue 阻塞队列

Blocking Queue 本身包含较多知识点，拆开来分析。

Blocking Queue 四大类实现: ArrayBlockingQueue, LinkedBlockingQueue, LinkedBlockingDequeue, PriorityBlockingQueue。

BlockingQueue 不存在扩容问题，它们会直接返回 FALSE 或者报错。

#### ArrayBlockingQueue

__Operations 基本操作__

|Inserting|Notes|Getting|Notes|
|-|-|-|-|
|add(E)         | Immediately insert, throws exception if queue is full, otherwise return true. |peek()| Read first element. Return NULL if empty. |
|offer(E)       | Immediately insert, return TRUE if successful, otherwise FALSE if queue is full. Preferable to add(). |poll()| Remove and get first element, return null if empty. |
|offer(E, time) | Wait specific time until space available and insert. Return TRUE if successful, otherwise FALSE if full. |poll(time)| Waiting available element for a specific time and then poll(), return NULL if still empty. |
|put(E)         | Wait until space available for new element to insert. |take()| Waiting until element available and get element. |

__ArrayBLQ vs LinkedBLQ__

ArrayBlockingQueue 有界队列，初始化必须设置size;

LinkedBlockingQueue 默认无界队列，初始化可以设置 size 成为有界队列，使用上要注意内存溢出(OOM);

__vs ConcurrentLinkedQueue__

ConcurrentLinkedQueue 是无界队列，不同于BlockingQueue，它没有使用ReentrantLock，取而代之的是通过使用 CAS 来实现非阻塞的队列，使用上同样要注意内存使用;

__ArrayBLQ 的公平与非公平锁__

```java
public ArrayBlockingQueue(int capacity, boolean fair) {}
```
- ArrayBLQ 的构造函数除了 int capacity，还有一个 boolean fair 参数，表示是否开启公平锁；
- 非公平锁在 ArrayBLQ 中就是意味着直接去抢锁资源而不用排队，但如果抢夺失败则会去排队，存在饥饿问题；
- 公平锁则是老老实实排队，按顺序分配锁资源；

#### Blocking Dequeue & Priority BlockingQueue

__Dequeue__

- Dequeue 全称 Double-ended Queue，双端队列，队首和队尾都可以进行操作，比如 addFirst(), addLast()，removeFirst(), removeLast()，在 BlockingDequeue 的情况下，可以进行 putFirst(), putLast(), takeFirst(), takeLast() 操作；
- Dequeue是非线程安全的，BlockingDequeue 是线程安全的；

__Priority Queue__
- PriorityQueue 是优先队列，底层使用的是堆(二叉树)实现的结构，默认是小顶堆，可以保证每次操作节点会自动调整树，将最小/最大的数值放到堆顶；
- PriorityQueue 是非线程安全，PriorityBlockingQueue 则是线程安全的；

#### BlockingQueue 的写法

生产者消费者模式与锁的使用 

- way 1: 使用 ArrayBlockingQueue 来充当生产者与消费者的队列；
- way 2: 新建一个 queue，比如使用 ArrayList / Array 但需要指定长度来限制最大长度，通过 synchronized 来加锁，实现等待阻塞队列；
  + 注意这里除了 sync 关键字，还需要一个对象(比如mutex)来实现 Object.wait() / Object.notifyAll();
- way 3: 和上面类似，但是使用 ReentrantLock 来加锁，实现等待阻塞队列；

关于 ReentrantLock 请参考 >> [Java Locking](./Java.Locking.md).

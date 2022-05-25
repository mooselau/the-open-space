# JVM / GC

* JVM and JMM
  - JVM structure
  - JMM concept, see [link](https://blog.csdn.net/javazejian/article/details/72772461)
* GC
  - GC Root, see [link](https://www.dynatrace.com/resources/ebooks/javabook/how-garbage-collection-works/)
  - Parallel GC, CMS, see [link](http://www.importnew.com/1993.html)
  - G1GC, see [link](https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html)

## JVM Java虚拟机

```text
"Thread Shared"      "Thread Private"
--------------- -------------------------------
| Heap        | | JVM Stack | | Native Stack  |
--------------- -------------------------------
| Method Area | | Program Counter Register    |
--------------- -------------------------------
```
- 左侧属于 线程共享区域，右侧属于 线程私有区域;
- 方法区 还包含 常量池 (Constants Pool)，类信息 (Class info);
- JVM Stack 包含本地变量 local vars；
- Native Stack 包含 原生方法和变量；
- Program Counter Register 程序计数器，包含 线程需要执行的下一行代码或者指令；

- JVM 结构
  + JVM 参数 -Xms 最小内存，-Xmx 最大内存，-Xx: +UseConcurrentMarkSweepGC 使用 CMS GC

>更多解释可以参考[Java永久区的简单理解](https://www.jianshu.com/p/589811d61d11)

### JMM Java Memory Model Java 内存模型

- Happens-before 原则
  + 比如 线程的 start() 一定在线程任何其他操作之前进行，还比如，同一个线程中 A操作在 B操作之前，B操作在 C操作之前，那么A操作一定在C操作之前 等等；
  + 当两步操作之间没有 happens-before 的关系的时候，那么 JVM在运行的时候就可能会对他们进行 __指令重排序__;
- 内存模型中，每个线程除了拥有独立的 线程栈，也拥有自己独立的 CPU 缓存区；
  + 线程在工作的时候会将需要的参数变量从 主存中拷贝到自己私有的缓存区进行高效工作；
  + 当两个线程各自拷贝了一个共享变量到缓存进行工作的时候，就可能会出现 读写不可见 的冲突；
- Volatile 关键字可以确保 JVM 对该变量的操作上下文不会发生重排序，并且对该变量的写操作 一定优先于 读操作；
  + 另外，使用 volatile 修饰的变量可以确保 所有的线程在该变量被修改的时候都看到一致的结果；
- 更详细的解释可以参考[这篇文章](https://www.jianshu.com/p/1c704d027e1e)

## Garbage Collection GC 垃圾回收器

### Generation Concept 年代回收概念

```text

--------  -------------- --------------
| Eden |  | Survivor 1 | | Survivor 2 | Young Generation
---------------------------------------
|                                     | Old Generation
---------------------------------------
|                 | Permanent Generation
-------------------
```

- 新对象会首先分配在 Eden 区域；
  + 如果 Eden 区域无法分配了，则会产生 Young GC / Minor GC；
  + Young GC 会将 存活的对象全部转移到 S1/S2 上面
  + 如此来回近 15次之后，依然存活的 对象会从 S1/S2 被升级到 老年代；
- 老年代用来存储一直存活的对象，但当老年代达到一定比率（可配置）的时候，则会产生 Old GC；
  + Old GC 会清理掉老年代中不再存活的对象；
- 当老年代 Old GC 也无法清理出足够的空间给新晋升的对象的时候，则会触发 Full GC；
  + Full GC 会对所有区域产生影响 ？？？
  + Full GC 会伴有 Compact 压缩过程，会对系统性能产生影响；

(要尽可能减少 FullGC 的可能)

## GC 类型

__Serial GC__
古老的线性GC，已过时。

单线程的 GC 过程，STW Event 巨长；

__Parallel GC__
比较老的并行 GC，已过时。

多线程的 GC 过程，STW Event 依然很长；

__CMS GC__

全称 ConcurrentMark&Sweep GC，并行标记清除 GC；

过程主要是：
- initial mark (初次标记 stop-the-world)
- resume execution
- remark (再标记)
- sweep (清除)

比较主流的 GC 过程，STW Event 比较短；

> 注意的是，默认CMS是没有 Compact的，在运行的过程中会不断产生碎片，直到某一天会因为饱和而触发 FullGC；

__G1GC__

```text
——————————
|  |  |  |
——————————
|  |  |  |
——————————
|  |  |  |
——————————
```
- G1GC 把 整体内存 Split 成了大小不同的 regions;
  + 1mb <= region size <= 32mb
- 任何一个 region 都可以用来作为 Eden/Survivor/Old generation 存放对象；

```text
Young GC:
————————————
| O |  | E |
————————————
| E |  |   |
————————————

- E + E => S, 两块 Eden 由于GC触发 合并称为一个 Survivor；

————————————
| O |  |   |
————————————
|   |  | S |
————————————

Old GC:
————————————
| O | O | E |
————————————
| S |   |   |
————————————

- 两块 Old 区域产生GC 合并 成为 一块 O 区域；

————————————
|   |  | E |
————————————
| S | O |  |
————————————
```

- G1GC 的优势非常明显，就是在GC的过程中就产生了清除和整理的效果，从而空出更多完整的空间给新的对象；
  + 具体发生在 remark phase, 就会被move；
  + GC 发生时，最优最低生存比率的 region 会被 清除，cleaned and removed；
- G1GC 相对于CMS GC 更快，更低延时；
- G1GC 有 compact；
- G1GC 的问题是，需要更多更大的memory；

### GC ROOTs

GC Roots有：
- local variables，本地变量
- active threads，活跃线程
- static variables，静态变量
- JNI References，JNI 引用

## Java & GC Java 和 GC

### Reference Types in Java Java 四大引用类型

__Strong Reference 强引用__
Object obj = new Ojbect() ，就是一个强引用，JVM在内部不足时，宁愿抛出OOM也不会清除掉强引用；

__Soft Reference 软引用__
软引用，就是在内存空间不够的时候会进行回收和清除；

__Weak Reference 弱引用__
而弱引用，就是当一个对象用弱引用指向它时，GC不论当前JVM空间是否足够，都(可能)会进行回收；

__Phantom Reference 虚引用__
而虚引用，就是在任意时间都可能被GC回收的类型，一般用来跟踪GC的活动的。另外，软引用，弱引用和虚引用在建立的时候都需要放入一个引用队列(ReferenceQueue)中方便JVM进行存储和清除。

？？ 测试 weakReferences

## JVM ClassLoader 类加载器

>注意，这里讨论的主要针对于 JDK<=8 的版本

JVM 在启动的时候需要加载各个类，有些类是 JVM 自身的类，有些是 JVM扩展类，还有些是应用所定义的类，为了区别加载这些类，JVM 支持了多种类加载器。

```text
Bootstrap Classloader
|
Extension Classloader
|
Application Classloader
|
*User Classloader
```
- 其中，Boostrap Classloader 主要负责加载 JAVA_HOME/lib 下的 \*.jar 和 \*.class，包括 rt.jar, resource.jar, charset.jar 等等;
- Extension Classloader 主要负责加载 JAVA_HOME/lib/ext 下的 \*.jar 和 \*.class;
- Application Classloader 主要负责加载当前应用 classpath 下的所有类 \*.class;
- User Classloader 是用户自定义的 类加载器，可以加载指定路径的 class 文件;

如上所示，加载器之间存在上下级关系，加载过程使用双亲委派模型。

### Parent-Delegation Model 双亲委派模型

双亲委派 或者 父类委派 模型指的是，当需要加载一个类的时候，当前的类加载器会先调用父类加载器尝试加载，如果加载失败，则会使用自身加载器对该类进行加载。

比如当应用启动的时候，需要加载 String.class，这时候 Application Classloader 会先调用父类加载器 即 Extension Classloader 进行加载，而 Extension Classloader 会继续调用它的父类加载器 Bootstrap Classloader 进行加载，由于 String.class 正好就是在 rt.jar 中的，需要 Bootstrap Classloader 来加载的，所以 Boostrap Classloader 可以正常加载 String.class 进而不需要 Extension Classloader 和 Application Classloader 来进行加载；如果这时候 需要加载应用定义的 MyService.class 类，这时候也一样，会从 Application Classloader 到 Extension Classloader 再到 Boostrap Classloder 一层层申请加载，但都会失败(他们会报 ClassNotFound Exception，意味着加载失败)，这时候子类就开始尝试加载，最终 Application Classloader 因为可以在 classpath下找到这个类，故而会加载成功。

这段逻辑可以尝试在 ClassLoader::loadClass() 中看到。

```java
public abstract class ClassLoader {
  protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException
  {
    synchronized (getClassLoadingLock(name)) {
        // First, check if the class has already been loaded
        if (c == null) {
            long t0 = System.nanoTime();
            try {
                if (parent != null) {
                    c = parent.loadClass(name, false);
                } else {
                    c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
                // ClassNotFoundException thrown if class not found
                // from the non-null parent class loader
            }

            if (c == null) {
                // If still not found, then invoke findClass in order
                // to find the class.
                long t1 = System.nanoTime();
                c = findClass(name);
            }
        }
        if (resolve) {
            resolveClass(c);
        }
        return c;
    }
  }
}
```

### 委派模型的问题和解决办法

双亲委派模型对于一些特殊情况就非常不适用，这时候就需要 违背 双亲委派模型来实现。

- 当需要定义同名类实现自定义功能的时候，往往因为父类加载器提前加载了同名类，而导致自身的类加载失败
  + 如果可以实现自身的类加载器，都先加载自身的类，失败的情况下，再去正常走 双亲委派加载，就没问题了。
- 当需要实现目前很多 IDE 的热插拔功能的时候，需要在服务运行过程中实现重新加载，而委派模型只能在启动的时候加载
  + 只有实现自定义的类加载器，才可以在运行过程中不断刷新 .class 字节码并重新加载类。
- 当需要通过 SPI 调用第三方库的时候，比如在项目启动的时候和 MySQL 建立连接
  + 由于在系统启动的时候就会通过 父类加载器 对数据库连接进行加载，但是 MySQL 的驱动在第三方的类库中，往往出现在 classpath下面，所以 在 正常双亲委派到 Bootstrap Classloader 进行加载 Driver 的时候，再去实现新的 ThreadContextClassLoader 来指定加载特定的 classpath 下的 第三方驱动，而不是让 bootstrap classloader 加载 (这样反而会由于在核心类库找不到而加载失败)。

更多分析可以参考链接[Java Service Provider Interface - Baeldung](https://www.baeldung.com/java-spi#spi-samples-in-the-java-ecosystem), [双亲委派模式](https://www.jianshu.com/p/5e0441cd2d4c)。
